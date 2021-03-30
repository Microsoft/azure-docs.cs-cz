---
title: Tichá instalace Azure Backup Server v2
description: K tiché instalaci Azure Backup Server v2 použijte skript prostředí PowerShell. Tento typ instalace se také označuje jako bezobslužná instalace.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74172247"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Spuštění bezobslužné instalace Azure Backup Server

Přečtěte si, jak spustit bezobslužnou instalaci Azure Backup Server.

Tyto kroky neplatí, pokud instalujete Azure Backup Server v1.

## <a name="install-backup-server"></a>Nainstalovat záložní server

1. Na serveru, který je hostitelem Azure Backup Server v2 nebo novějším, vytvořte textový soubor. (Soubor můžete vytvořit v programu Poznámkový blok nebo jiném textovém editoru.) Uložte soubor jako MABSSetup.ini.

2. Do souboru MABSSetup.ini vložte následující kód. Text v závorkách () nahraďte \< \> hodnotami z vašeho prostředí. Následující text je příklad:

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

3. Soubor uložte. Pak na příkazovém řádku se zvýšenými oprávněními na instalačním serveru zadejte tento příkaz:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

Tyto příznaky můžete použít pro instalaci:</br>
**/f**:. ini cesta k souboru</br>
**/l**: protokolová cesta</br>
**/i**: Instalační cesta</br>
**/x**: odinstalace cesty</br>

## <a name="next-steps"></a>Další kroky

Po instalaci záložního serveru se dozvíte, jak připravit server nebo začít chránit úlohu.

- [Příprava úloh záložního serveru](backup-azure-microsoft-azure-backup.md)
- [Použití Backup Serveru k zálohování serveru VMware](backup-azure-backup-server-vmware.md)
- [Použití Backup Serveru k zálohování SQL Serveru](backup-azure-sql-mabs.md)
- [Přidání moderního úložiště zálohování na Backup Server](backup-mabs-add-storage.md)
