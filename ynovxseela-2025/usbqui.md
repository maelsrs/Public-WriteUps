# 🔌 USB Qui ?

### 🛠 Type : `Forensics`

## 📝 Description

> _"J'ai fais visiter la tour de contrôle à un passionné"_

Un utilisateur a détecté des ralentissements et des fichiers supprimés sur son poste **Windows** après le passage d'un inconnu dans son bureau. 

Manque de bol, vous étiez en pleine migration, et ce poste là n'avait pas d'agent **Sysmon** ! Pour couronner le tout, **aucun collecteur de log** et vous n'avez même pas accès à votre **SIEM** préféré car votre tuteur est en congé ! 

Dans l'urgence, vous avez du extraire les logs de cette machine avec les outils d'**Eric Zimmerman**. 

Votre objectif à présent c'est d'**identifier** si oui ou non elle est compromise, et *dépêchez-vous* car l'utilisateur attend de pouvoir redémarrer son poste.

> 📦 Fichier remis: `Export.zip`

> 🎯 Format du flag attendu : `YNOV{****_*****}`


## 📂 Arborescence

Voici le contenu de l'archive `Export.zip` :

```
Export.zip
└── Output
    ├── auditpol.txt
    ├── ConsoleHost_history.txt
    ├── EVTX
    │   ├── 20250212081318_EvtxECmd_Output.csv
    │   └── 20250212081318_EvtxECmd_Output.json
    ├── MFT
    │    ├── 20250212081454_MFTECmd_$MFT_Output.csv
    │    ├── 20250212081454_MFTECmd_$MFT_Output.json
    │    └── Resident
    │       ├── 100000-1_69538-1_amd64_microsoft-windows-s..k-transformers-core_31bf3856ad364e35_10.0.18362.1_none_3e910aa752a97cd5.manifest.bin.bin
    │         ├── 100020-1_69568-1_amd64_microsoft-windows-s..line-user-interface_31bf3856ad364e35_10.0.18362.1_none_af44e2c05a10722d.manifest.bin.bin
    │         ├── 100040-1_69608-1_amd64_microsoft-windows-s..ngc-ctnrgidshandler_31bf3856ad364e35_10.0.18362.1_none_35cc36fcaca82030.manifest.bin.bin
    │         └── ....
    └── Prefetch
        ├── 20250212081311_PECmd_Output.csv
        ├── 20250212081311_PECmd_Output.json
        └── 20250212081311_PECmd_Output_Timeline.csv

```

## 🔍 Analyse

Pour rechercher tout périphérique USB ayant pu être branché, j’ai utilisé `grep` pour filtrer tous les fichiers avec des mots-clés spécifiques aux périphériques de stockage ou de type USB.

### 💡 Commande utilisée :

```bash
grep -iE -R "USBSTOR|Disk&Ven|MountedDevices|RemovableMedia|VID|WPDBUSENUM|Volume{|Enum\\USB|FriendlyName" * > output.txt
```

#### 📌 Explication des options :

Les motifs recherchés sont tous typiques de la présence d’un périphérique USB :

- `USBSTOR`, `VID`, `PID` : identifiants dans la base de registre
- `MountedDevices`, `Enum\\USB`, `RemovableMedia`, etc.

## 🧠 Découverte

Avec 1600 lignes et 4060513 caractères, trouver le nom du periphérique est presque impossible, mais, grâce a un peu de hasard, une ligne m'interpelle

`#text\":\"USB\\\\VID_0483&amp;PID_5740\\\\flip_Ihaki\"}`

Cela correspond à un périphérique avec les identifiants suivants :

- `VID_0483` : STMicroelectronics (fabricant)

- `PID_5740` : périphérique type Virtual COM Port

- `flip_Ihaki` : nom assigné au périphérique

Le nom ressemblant au format demandé, est effectivement le flag !

## 🏁 Flag

```
YNOV{flip_Ihaki}
```
