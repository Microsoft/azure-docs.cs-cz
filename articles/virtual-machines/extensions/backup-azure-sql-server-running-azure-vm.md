---
title: Azure Backup pro SQL Server běžící na virtuálním počítači Azure
description: V tomto článku se dozvíte, jak zaregistrovat Azure Backup v SQL Server spuštěném na virtuálním počítači Azure.
author: dcurwin
manager: carmonm
ms.service: virtual-machines
ms.subservice: extensions
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: 66675f77d480ce8d9f21e5ffb507c475337c9dab
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490726"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup pro SQL Server běžící na virtuálním počítači Azure

Azure Backup, mimo jiné nabídky, poskytuje podporu pro zálohování úloh, jako je SQL Server spouštění na virtuálních počítačích Azure. Vzhledem k tomu, že aplikace SQL běží v rámci virtuálního počítače Azure, služba zálohování potřebuje oprávnění pro přístup k aplikaci a načtení potřebných podrobností.
K tomu Azure Backup nainstaluje na virtuální počítač rozšíření **AzureBackupWindowsWorkload** , ve kterém je spuštěná SQL Server během procesu registrace aktivovaného uživatelem.

## <a name="prerequisites"></a>Předpoklady

Seznam podporovaných scénářů najdete v tématu věnovaném [podpoře](../../backup/sql-support-matrix.md#scenario-support) , které podporuje Azure Backup.

## <a name="network-connectivity"></a>Připojení k síti

Azure Backup podporuje značky NSG, nasazení proxy server nebo uvedené rozsahy IP adres; Podrobnosti o jednotlivých metodách najdete v tomto [článku](../../backup/backup-sql-server-database-azure-vms.md#establish-network-connectivity).

## <a name="extension-schema"></a>Schéma rozšíření

Hodnoty schématu a vlastností rozšíření jsou konfigurační hodnoty (nastavení modulu runtime), které služba předává rozhraní CRP API. Tyto hodnoty konfigurace se používají během registrace a upgradu. Rozšíření **AzureBackupWindowsWorkload** používá i toto schéma. Schéma je předem nastaveno; do pole objectStr se dá přidat nový parametr.

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Následující JSON zobrazuje schéma pro rozšíření WorkloadBackup.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Hodnoty vlastností

Name | Hodnota/příklad | Datový typ
 --- | --- | ---
locale | cs-cz  |  řetězec
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | řetězec
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | řetězec
commandStartTimeUTCTicks | "636967192566036845"  | řetězec
vmType  | "Microsoft. COMPUTE/VirtualMachines"  | řetězec
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250 YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ = =" | řetězec
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | řetězec
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | řetězec

## <a name="template-deployment"></a>Nasazení šablon

K virtuálnímu počítači doporučujeme přidat rozšíření AzureBackupWindowsWorkload povolením zálohování SQL Server na virtuálním počítači. To je možné dosáhnout pomocí [Správce prostředků šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) navržené pro automatizaci zálohování na SQL SERVERm virtuálním počítači.

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Musíte zaregistrovat virtuální počítač Azure, který obsahuje aplikaci SQL, do trezoru služby Recovery Services. Během registrace se na virtuální počítač nainstaluje rozšíření AzureBackupWindowsWorkload. K registraci virtuálního počítače použijte rutinu [Register-AzRecoveryServicesBackupContainerPS](/powershell/module/az.recoveryservices/register-azrecoveryservicesbackupcontainer?view=azps-1.5.0) .

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Příkaz vrátí **kontejner zálohování** tohoto prostředku a stav bude **zaregistrován**.

## <a name="next-steps"></a>Další kroky

- [Další informace](../../backup/backup-sql-server-azure-troubleshoot.md) o pokynech pro řešení potíží se ZÁLOHOVÁNÍM virtuálních počítačů Azure SQL Server
- [Běžné dotazy](../../backup/faq-backup-sql-server.md) týkající se zálohování SQL Server databází, které běží na virtuálních počítačích Azure a využívají službu Azure Backup.
