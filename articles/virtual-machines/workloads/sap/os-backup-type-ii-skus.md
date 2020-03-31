---
title: Zálohování a obnovení sap hana v Azure (velké instance) typu Skladové objekty II| Dokumenty společnosti Microsoft
description: Provedení zálohování a obnovení operačního systému pro SAP HANA v azure (velké instance) typ Skladové objekty Typu II
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 100e1b974e54d8c0065194bc7beb18f458011434
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616871"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Zálohování a obnovení operačního systému pro skladové sloky typu II razítek revize 3

Tento dokument popisuje kroky k provedení zálohování a obnovení na úrovni souboru operačního systému pro **skladové položky typu II** velkých instancí revize 3. 

>[!Important]
> **Tento článek se nevztahuje na nasazení sku typu II v razítkách velké instance revize 4 HANA.** Spouštěcí logické jednotky jednotek velké instance typu II HANA, které jsou nasazeny v razítkách velké instance revize 4 HANA, lze zálohovat pomocí snímků úložiště, protože se jedná o jednotky SKU typu I, které jsou již v razítkách revize 3


>[!NOTE]
>Zálohovací skripty operačního systému používají software ReaR, který je předinstalován na serveru.  

Po dokončení zřizování `Service Management` týmem společnosti Microsoft je ve výchozím nastavení server nakonfigurován se dvěma plány zálohování pro zálohování úrovně systému souborů zpět operačního systému. Plány úloh zálohování můžete zkontrolovat pomocí následujícího příkazu:
```
#crontab –l
```
Plán zálohování můžete kdykoli změnit pomocí následujícího příkazu:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Jak provést ruční zálohování?

Zálohování souborového systému operačního systému je naplánováno pomocí **úlohy cron** již. Zálohování na úrovni souboru operačního systému však můžete provést také ručně. Ruční zálohování provedete takto:

```
#rear -v mkbackup
```
Následující obrazovka ukazuje ukázkové ruční zálohování:

![Jak](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak obnovit zálohu?

Ze zálohy můžete obnovit úplnou zálohu nebo samostatný soubor. Chcete-li obnovení obnovit, použijte následující příkaz:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po obnovení je soubor obnoven v aktuálním pracovním adresáři.

Následující příkaz zobrazuje obnovení souboru */etc/fstabfrom* *backup.tar.gz* záložního souboru
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Po obnovení ze zálohy je nutné soubor zkopírovat do požadovaného umístění.

Následující snímek obrazovky ukazuje obnovení úplné zálohy:

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak nainstalovat nástroj ReaR a změnit konfiguraci? 

Balíčky Relax-and-Recover (ReaR) jsou **předinstalované** v **objektech SKU typu II** velkých instancí HANA a není od vás nutná žádná akce. Můžete přímo začít používat ReaR pro zálohování operačního systému.
Však za okolností, kdy je třeba nainstalovat balíčky ve své vlastní, můžete postupovat podle uvedených kroků k instalaci a konfiguraci nástroje ReaR.

Chcete-li nainstalovat záložní balíčky **ReaR,** použijte následující příkazy:

Pro operační systém **SLES** použijte následující příkaz:
```
#zypper install <rear rpm package>
```
Pro operační systém **RHEL** použijte následující příkaz: 
```
#yum install rear -y
```
Chcete-li nakonfigurovat nástroj ReaR, je třeba aktualizovat parametry **OUTPUT_URL** a **BACKUP_URL** v *souboru /etc/rear/local.conf*.
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

Následující snímek obrazovky ukazuje obnovení ![úplné zálohy: RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
