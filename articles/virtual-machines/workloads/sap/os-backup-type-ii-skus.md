---
title: Zálohování a obnovení operačního systému SAP HANA v Azure (velké instance) – SKU typu II | Microsoft Docs
description: Provedení zálohování a obnovení operačního systému pro SAP HANA v Azure (velké instance) SKU typu II
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
ms.openlocfilehash: b02c669439c54f34afb4212949b20f6793784103
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91359421"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Zálohování a obnovení operačního systému pro SKU typu II u razítek revize 3

Tento dokument popisuje kroky pro zálohování a obnovení na úrovni souborů operačního systému pro **skladové jednotky typu II** pro velké instancey revize 3 pro Hana. 

>[!Important]
> **Tento článek neplatí pro nasazení typu II SKU v revizi velká instance na úrovni 4 HANA.** Spouštěcí jednotky logických jednotek typu II HANA velké instance, které jsou nasazené v revizi 4 HANA velká instance, se dají zálohovat pomocí snímků úložiště, protože se jedná o případ, kdy se v nich už v revizích 3 razítka nacházejí.


>[!NOTE]
>Skripty pro zálohování operačního systému využívají zadní software, který je předem nainstalován na serveru.  

Po dokončení zřizování `Service Management` týmem Microsoftu ve výchozím nastavení je server nakonfigurovaný se dvěma plány zálohování k zálohování na úrovni systému souborů zpátky v operačním systému. Plány úloh zálohování můžete kontrolovat pomocí následujícího příkazu:
```
#crontab –l
```
Plán zálohování můžete kdykoli změnit pomocí následujícího příkazu:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Jak provést ruční zálohování?

Zálohování systému souborů operačního systému je naplánováno pomocí **úlohy cron** . Zálohování na úrovni souborů operačního systému ale můžete provádět také ručně. Ruční zálohování provedete spuštěním následujícího příkazu:

```
#rear -v mkbackup
```
Na následující obrazovce vidíte ukázková ruční záloha:

![použití](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Jak obnovit zálohu?

Můžete obnovit úplnou zálohu nebo jednotlivý soubor ze zálohy. K obnovení použijte následující příkaz:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Po obnovení se soubor obnoví v aktuálním pracovním adresáři.

Následující příkaz ukazuje obnovení souboru */etc/fstabfrom* zálohy záložního souboru *. tar. gz*
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>Až se soubor obnoví ze zálohy, musíte ho zkopírovat do požadovaného umístění.

Následující snímek obrazovky ukazuje obnovení úplné zálohy:

![Snímek obrazovky s obnovením zobrazuje okno příkazového řádku.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Jak nainstalovat zadní nástroj a změnit konfiguraci? 

Balíčky s vysokou a obnovenou **instalací jsou předem nainstalovány** v **SKU typu II** velkých instancí Hana a žádná akce není od vás nutná. Pro zálohování operačního systému můžete přímo začít používat zezadu.
V případech, kdy potřebujete balíčky nainstalovat sami, můžete postupovat podle pokynů uvedených v části instalace a konfigurace nástroje pro zpětnou instalaci.

Chcete **-li nainstalovat záložní balíčky** , použijte následující příkazy:

Pro operační systém **SLES** použijte následující příkaz:
```
#zypper install <rear rpm package>
```
Pro operační systém **RHEL** použijte následující příkaz: 
```
#yum install rear -y
```
Chcete-li nakonfigurovat zadní nástroj, je nutné aktualizovat parametry **OUTPUT_URL**  a **BACKUP_URL**  v *souboru/etc/Rear/Local.conf*.
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

Následující snímek obrazovky ukazuje obnovení úplné zálohy: ![ snímek obrazovky zobrazuje okno příkazového řádku s obnovením pomocí zadního nástroje.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
