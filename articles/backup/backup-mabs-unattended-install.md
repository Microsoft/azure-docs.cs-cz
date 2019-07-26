---
title: Tichá instalace Azure Backup Server v2
description: K tiché instalaci Azure Backup Server v2 použijte skript prostředí PowerShell. Tento typ instalace se také označuje jako bezobslužná instalace.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: raynew
ms.openlocfilehash: add1f4057b5b52310f53553dcd23e3357fb1ee29
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465010"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Spuštění bezobslužné instalace Azure Backup Server

Přečtěte si, jak spustit bezobslužnou instalaci Azure Backup Server.

Tyto kroky neplatí, pokud instalujete Azure Backup Server v1.

## <a name="install-backup-server"></a>Nainstalovat záložní server

1. Na serveru, který je hostitelem Azure Backup Server v2 nebo novějším, vytvořte textový soubor. (Soubor můžete vytvořit v programu Poznámkový blok nebo jiném textovém editoru.) Uložte soubor jako MABSSetup. ini.

2. Vložte následující kód do souboru MABSSetup. ini. Text v závorkách (\< \>) nahraďte hodnotami z vašeho prostředí. Následující text je příklad:

   ```
   [OPTIONS]
   UserName=administrator
   CompanyName=<Microsoft Corporation>
   SQLMachineName=localhost
   SQLInstanceName=<SQL instance name>
   SQLMachineUserName=administrator
   SQLMachinePassword=<admin password>
   SQLMachineDomainName=<machine domain>
   ReportingMachineName=localhost
   ReportingInstanceName=<reporting instance name>
   SqlAccountPassword=<admin password>
   ReportingMachineUserName=<username>
   ReportingMachinePassword=<reporting admin password>
   ReportingMachineDomainName=<domain>
   VaultCredentialFilePath=<vault credential full path and complete name>
   SecurityPassphrase=<passphrase>
   PassphraseSaveLocation=<passphrase save location>
   UseExistingSQL=<1/0 use or do not use existing SQL>
   ```

3. Uložte soubor. Pak na příkazovém řádku se zvýšenými oprávněními na instalačním serveru zadejte tento příkaz:

   ```
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Tyto příznaky můžete použít pro instalaci:</br>
**/f**:. ini cesta k souboru</br>
**/l**: Cesta protokolu</br>
**/i**: Instalační cesta</br>
**/x**: Odinstalace cesty</br>

## <a name="next-steps"></a>Další postup
Po instalaci záložního serveru se dozvíte, jak připravit server nebo začít chránit úlohu.

- [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
- [Použití záložního serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Zálohování SQL Server pomocí záložního serveru](backup-azure-sql-mabs.md)
- [Přidat Moderní úložiště zálohování do záložního serveru](backup-mabs-add-storage.md)
