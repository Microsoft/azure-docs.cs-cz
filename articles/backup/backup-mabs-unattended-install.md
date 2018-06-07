---
title: Tichou instalaci serveru Azure Backup v2
description: Použití skriptu prostředí PowerShell k bezobslužné instalaci serveru Azure Backup v2. Tento typ instalace je také označován bezobslužné instalace.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/30/2017
ms.author: markgal
ms.openlocfilehash: 126c1971d83a8874c096caf407231fb6dee2ff59
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606405"
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Spusťte bezobslužnou instalaci serveru Azure Backup v2

Postup spuštění bezobslužné instalace serveru Azure Backup v2. 

Tyto kroky neplatí, pokud instalujete Azure Backup Server v1.

## <a name="install-backup-server-v2"></a>Nainstalujte Backup Server v2

1. Na serveru, který je hostitelem serveru Azure Backup v2 vytvořte textový soubor. (Soubor můžete vytvořit v poznámkovém bloku nebo v jiném textovém editoru.) Uložte soubor jako MABSSetup.ini. 

2. Vložte následující kód v souboru MABSSetup.ini. Nahraďte text v závorkách (\< \>) s hodnotami ze svého prostředí. Tento text je příklad:

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

3. Uložte soubor. Na příkazovém řádku se zvýšenými oprávněními na serveru pro instalaci, zadejte tento příkaz:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Pro instalaci můžete použít tyto příznaky:</br>
**/f**: cesta k souboru INI</br>
**/l**: cesta protokolu</br>
**/i**: Instalační cesta</br>
**/x**: Odinstalujte cesta</br>

## <a name="next-steps"></a>Další postup
Po instalaci serveru zálohování, zjistěte, jak připravit server nebo začít chránit zatížení.

- [Příprava úlohy zálohování serveru](backup-azure-microsoft-azure-backup.md)
- [Pomocí zálohování serveru zálohovat VMware server](backup-azure-backup-server-vmware.md)
- [Použít zálohování serveru k zálohování systému SQL Server](backup-azure-sql-mabs.md)
- [Přidat moderní úložiště zálohování k zálohování serveru](backup-mabs-add-storage.md)
