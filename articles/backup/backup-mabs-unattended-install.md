---
title: Netichou instalaci serveru Azure Backup V2
description: Pomocí skriptu PowerShellu můžete bezobslužně nainstalovat Server zálohování Azure V2. Tento druh instalace se také nazývá bezobslužná instalace.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172247"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Spuštění bezobslužné instalace serveru Azure Backup Server

Přečtěte si, jak spustit bezobslužnou instalaci Azure Backup Serveru.

Tyto kroky neplatí, pokud instalujete Azure Backup Server V1.

## <a name="install-backup-server"></a>Instalace zálohovacího serveru

1. Na serveru, který je hostitelem Serveru Zálohování Azure V2 nebo novějšího, vytvořte textový soubor. (Soubor můžete vytvořit v poznámkovém bloku nebo v jiném textovém editoru.) Uložte soubor jako soubor MABSSetup.ini.

2. Vložte následující kód do souboru MABSSetup.ini. Nahraďte text uvnitř\< \>závorek ( ) hodnotami z vašeho prostředí. Následující text je příkladem:

   ```text
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

3. Uložte soubor. Potom na příkazovém řádku se zvýšenými oprávněními na instalačním serveru zadejte tento příkaz:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Pro instalaci můžete použít tyto příznaky:</br>
**/f**: Cesta k souboru INI</br>
**/l**: Cesta protokolu</br>
**/i**: Instalační cesta</br>
**/x**: Odinstalace cesty</br>

## <a name="next-steps"></a>Další kroky

Po instalaci zálohovacího serveru se dozvíte, jak připravit server nebo jak začít chránit pracovní vytížení.

- [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
- [Zálohování serveru VMware pomocí zálohovacího serveru](backup-azure-backup-server-vmware.md)
- [Zálohování serveru SQL Server pomocí zálohovacího serveru](backup-azure-sql-mabs.md)
- [Přidání moderního úložiště záloh na záložní server](backup-mabs-add-storage.md)
