# [Proxmox] Impossible de wipe un disque : "has a holder (500)"

> **En résumé :** Le disque ne peut pas être effacé (wipe) via l'interface Proxmox car un élément du système (LVM, ZFS, Ceph OSD, etc.) l'utilise encore et bloque l'opération.

---

## Informations
- **Catégorie :** Proxmox
- **Date :** 2026-07-30
- **Système / Version :** Proxmox VE 8.x

---

## Erreur & Symptômes

En essayant de wipe un disque depuis l'interface web Proxmox (**Datacenter > Node > Disks > Wipe Disk**), le message suivant apparaît :

```
disk/partition '/dev/sdb1' has a holder (500)
```

Le disque `/dev/sdb1` (ou `sda`, `nvme0n1`, etc. selon les cas) refuse d'être wipé, alors que le disque ne semble pas monté explicitement dans le système.

---

## Cause du problème

Ce message signifie qu'un **"holder"** — un processus ou une couche logicielle — retient activement la partition. Cela arrive généralement quand le disque a déjà été utilisé auparavant pour :

- un **Volume Group LVM** (souvent visible avec un nom du type `pve-...` ou un ancien nom de stockage) ;
- un **pool ZFS** ;
- un **OSD Ceph** (le cas le plus fréquent sur les disques recyclés depuis un cluster Ceph) ;
- un **device-mapper** actif qui référence encore la partition.

Proxmox refuse de wipe le disque tant que cette ancienne configuration (le "holder") n'a pas été proprement supprimée, pour éviter de casser un stockage encore actif par erreur.

---

## Solution

**1. Identifier le holder avec `lsblk`**

```bash
lsblk
```

Cherche les entrées `lvm` ou `dm-` en dessous de la partition concernée (ex: `/dev/sdb1`). Le nom donne un indice sur l'origine (`ceph-...`, `pve-...`, nom de VG personnalisé, etc.).

**2. Cas n°1 — Le holder est du LVM classique**

```bash
# Lister les volumes group
vgs

# Supprimer le volume group concerné
vgremove <nom_du_vg>

# Si besoin, supprimer aussi le physical volume
pvremove /dev/sdb1
```

**3. Cas n°2 — Le holder est un ancien OSD Ceph**

```bash
ceph-volume lvm zap /dev/sdb --destroy
```

**4. Cas n°3 — Le holder est un device-mapper "orphelin"**

```bash
# Repérer l'ID exact (souvent visible dans lsblk, ex: ceph-xxxx ou pve--OLD--xxxx)
dmsetup ls

# Supprimer l'entrée correspondante
dmsetup remove <id_du_holder>
```

**5. Si le holder persiste malgré tout (cas "tenace")**

Cette méthode force le noyau à relire complètement le disque :

```bash
sgdisk --zap-all /dev/sdb

readlink /sys/block/sdb

echo 1 > /sys/block/sdb/device/delete

echo "- - -" > /sys/class/scsi_host/hostX/scan
```

> Remplace `hostX` par l'ID retourné par la commande `readlink` précédente (ex: `host0`, `host5`...).

**6. Relancer le wipe**

Une fois le holder supprimé, retourne dans l'interface Proxmox (**Disks > Wipe Disk**) ou utilise directement :

```bash
wipefs -fa /dev/sdb1
```

---

## Liens & Références
- Forum Proxmox — discussions autour de l'erreur "has a holder (500)"
- Documentation Proxmox — gestion des disques (`pvesm`, `Disks` GUI)