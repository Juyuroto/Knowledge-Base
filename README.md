# Knowledge Base — Bugs, Erreurs & Solutions

Ce dépôt est ma base de connaissances personnelle (TIL — *Today I Learned*).
J'y répertorie les bugs, erreurs système et problèmes de configuration rencontrés au quotidien sur mes différents projets tech, accompagnés de leurs solutions.

L'objectif est simple : **ne jamais perdre une solution deux fois**.

## Organisation

Chaque fiche est un fichier Markdown, classé dans un dossier correspondant à sa technologie :

```
proxmox/
```

Chaque fichier suit un nommage explicite, par exemple :
`proxmox/lock-file-pve-config.md`

## Format des fiches

Toutes les fiches suivent une structure commune :

- **Informations** (catégorie, date, système/version)
- **Erreur & Symptômes** (message d'erreur exact, logs)
- **Cause du problème**
- **Solution** (procédure pas à pas)
- **Liens & Références**

## Index des problèmes

### Proxmox

| Référence | Titre | Système / Version | Date |
|---|---|---|---|
| [proxmox-disk-partition-has-a-holder](proxmox/1.disk-partition-has-a-holder.md) | Impossible de wipe un disque : "has a holder (500)" | Proxmox 8.4.0 | 2026-07-30 |
| [virtualisation-bios-désactivée.](proxmox/2.virtualisation-bios-désactivée.md) | Erreur KVM : Virtualisation CPU désactivée | Proxmox 8.4.0 | 2026-07-30 |
| [zfs-stripe.](proxmox/3.zfs-stripe.md) | Fusion de 2 disques en ZFS Stripe (RAID0) | Proxmox 8.4.0 | 2026-08-06 |
| [fix-err400-ram.](proxmox/4.fix-err400-ram.md) | Erreur 400 allow-ksm lors du changement de RAM | Proxmox 8.4.0 | 2026-08-06 |
| [pve-no-subscription](proxmox/5.pve-no-subscription.md) | Erreur 401 Unauthorized lors des mises à jour APT | Proxmox 8.4.0 | 2026-08-06 |

### Linux

| Référence | Titre | Système / Version | Date |
|---|---|---|---|
| [stockage-insufisant](linux/1.stockage-insufisant.md) | Disque qui n'a pas pris la totalité de son stockage | Ubuntu 26.04 LTS | 2026-07-31 |




---

> Chaque nouvelle fiche ajoutée dans un dossier doit être référencée dans le tableau correspondant ci-dessus, avec un lien relatif vers le fichier.