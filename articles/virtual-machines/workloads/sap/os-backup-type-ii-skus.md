---
title: Operační systém zálohování a obnovení systému SAP HANA v Azure (velké instance) zadejte II SKU | Dokumentace Microsoftu
description: Provedení operační systém zálohování a obnovení pro SAP HANA v Azure (velké instance) SKU typu II
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
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60708558"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Operační systém zálohování a obnovení pro SKU typu II

Tento dokument popisuje postup, jak provést zálohu úrovni souboru operačního systému a obnovení **SKU typu II** z velkých instancích HANA. 

>[!NOTE]
>Skripty pro zálohování operačního systému používá zpětná software, který je předem nainstalované na serveru.  

Po dokončení týmem Microsoft Service Management ve výchozím nastavení, zřizování je server nakonfigurován s plánem dvojí zálohování k zálohování systému souborů na úrovni zálohování operačního systému. Plán úlohy zálohování můžete zkontrolovat pomocí následujícího příkazu:
```
#crontab –l
```
Můžete změnit plán zálohování kdykoli pomocí následujícího příkazu:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Jak vytvořit ruční zálohu?

Zálohu systému souborů operačního systému je naplánováno, použití **úlohy cron** již. Však můžete provést operační systém záloha na úrovni souborů i ručně. K provedení ruční zálohy, spusťte následující příkaz:

```
#rear -v mkbackup
```
Tyto obrazovky zobrazit ukazuje ruční zálohy vzorku:

![Jak](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak obnovit zálohu?

Úplné zálohování nebo jednotlivých souborů můžete obnovit ze zálohy. Pokud chcete obnovit, použijte následující příkaz:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po obnovení je obnovena soubor v aktuálním pracovním adresáři.

Následující příkaz ukazuje, obnovení souboru */etc/fstabfrom* záložní soubor *backup.tar.gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Budete muset po obnovení ze zálohy zkopírujte soubor do požadovaného umístění.

Následující snímek obrazovky ukazuje obnovení úplné zálohy:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak nainstalovat nástroj zadní a změňte konfiguraci? 

Balíčky Relax a obnovení (zpětná) jsou **předinstalovaným** v **SKU typu II** velkých instancích HANA, a od vás nevyžaduje žádná akce. Můžete přímo začít používat zadní pro zálohování operačního systému.
Ale v případech, kdy potřebujete k instalaci balíčků ve vašem vlastním, můžete použít uvedené postup instalace a konfigurace nástroje zadní.

Chcete-li nainstalovat **zadní** zálohování balíčky, použijte následující příkazy:

Pro **SLES** operační systém, použijte následující příkaz:
```
#zypper install <rear rpm package>
```
Pro **RHEL** operační systém, použijte následující příkaz: 
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
