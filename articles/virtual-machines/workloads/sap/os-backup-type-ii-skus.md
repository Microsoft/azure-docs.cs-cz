---
title: Operační systém zálohování a obnovení SAP HANA v Azure (velké instance) zadejte II SKU | Microsoft Docs
description: Provést Operatign systému zálohování a obnovení pro SAP HANA na Azure (velké instance) typu II SKU
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f01a32612b335003856a372ece15ef300b9d93db
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37063270"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Operační systém zálohování a obnovení pro typ II SKU

Tento dokument popisuje postup proveďte zálohu úrovni souboru operačního systému a obnovíte **typu II SKU** velké instancí HANA. 

>[!NOTE]
>Skripty zálohování operačního systému používá zadní software, který je předem nainstalovaná na serveru.  

Po dokončení týmem Microsoft Service Management ve výchozím nastavení, zajišťování je server nakonfigurovaný s dvě zálohy plán zálohování systému souborů úrovně zálohování operačního systému. Plán úlohy zálohování můžete zkontrolovat pomocí následujícího příkazu:
```
#crontab –l
```
Můžete změnit plán zálohování kdykoli pomocí následujícího příkazu:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Postup provedení ruční zálohy?

Zálohu systému souborů operačního systému je naplánováno, použití **cron úlohy** již. Můžete však provést operačního systému úrovni záložní soubor také ručně. Pokud chcete provést ruční zálohy, spusťte následující příkaz:

```
#rear -v mkbackup
```
Následující zobrazení obrazovky ukazuje ukázka ručního zálohování:

![postupy](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak obnovit zálohu?

Úplné zálohování nebo jednotlivých souborů můžete obnovit ze zálohy. Pokud chcete obnovit, použijte následující příkaz:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po obnovení je soubor obnovena v aktuálním pracovním adresáři.

Následující příkaz ukazuje obnovení souboru */etc/fstabfrom* záložní soubor *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Budete muset po obnovení ze zálohy zkopírujte soubor do požadovaného umístění.

Následující snímek obrazovky ukazuje obnovení úplné zálohování:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak nainstalovat nástroj zadní a změnit konfiguraci? 

Balíčky Relax a obnovit (zpětná) jsou **předinstalovaným** v **typu II SKU** HANA velké instancí a od vás vyžadována žádná akce. Můžete začít přímo používat zadní pro zálohování operačního systému.
Však v případech, kdy potřebujete instalaci balíčků ve vašem vlastním, můžete provést uvedené kroky k instalaci a konfiguraci nástroje zadní.

K instalaci **zadní** zálohování balíčků, použijte následující příkazy:

Pro **SLES** operačního systému, použijte následující příkaz:
```
#zypper install <rear rpm package>
```
Pro **RHEL** operačního systému, použijte následující příkaz: 
```
#yum install rear -y
```
Ke konfiguraci nástroje zadní, budete muset aktualizovat parametry **OUTPUT_URL** a **BACKUP_URL** v *souboru /etc/rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

Následující snímek obrazovky ukazuje obnovení úplné zálohy: ![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
