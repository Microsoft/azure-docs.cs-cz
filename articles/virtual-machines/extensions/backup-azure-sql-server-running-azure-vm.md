---
title: Azure Backup pro SQL Server běžící na virtuálním počítači Azure
description: Postup při registraci Azure zálohování SQL serveru běžícího na virtuálním počítači Azure
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607608"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup pro SQL Server běžící na virtuálním počítači Azure

Služba Azure Backup, kromě jiných nabídek poskytuje podporu pro zálohování úloh, jako jsou SQL serveru spuštěné ve virtuálních počítačích Azure. SQL aplikace běží ve Virtuálním počítači Azure, musí služba backup oprávnění pro přístup k aplikaci a načíst potřebné podrobnosti.
K tomu Azure Backup nainstaluje **AzureBackupWindowsWorkload** rozšíření na virtuálním počítači, ve kterém běží SQL Server během procesu registrace se aktivuje tímto uživatelem.

## <a name="prerequisites"></a>Požadavky

Seznam podporovaných scénářů, najdete [matice podpory](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) podporovány službou Azure Backup.

## <a name="network-connectivity"></a>Připojení k síti

Azure Backup podporuje značky skupiny zabezpečení sítě, nasazení proxy serveru nebo uvedené rozsahy IP adres; Podrobnosti o každé z metod, najdete na tomto [článku](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Schéma rozšíření

Rozšíření schématu a vlastností hodnoty jsou hodnoty konfigurace (nastavení modulu runtime), která služba je předat do rozhraní API CRP. Tyto hodnoty konfigurace se používají během registrace a upgrade. **AzureBackupWindowsWorkload** toto schéma používají rozšíření. Schéma je předem nastavené; v poli objectStr můžete přidat nový parametr

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

Následující kód JSON ukazuje schéma pro WorkloadBackup rozšíření.  

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

Name | Příklad hodnoty / | Datový typ
 --- | --- | ---
Národní prostředí | En-us  |  řetězec
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | řetězec
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == " | řetězec
commandStartTimeUTCTicks | "636967192566036845"  | řetězec
vmType  | "microsoft.compute/virtualmachines"  | řetězec
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == " | řetězec
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | řetězec


## <a name="template-deployment"></a>Nasazení šablon

Doporučujeme přidání AzureBackupWindowsWorkload rozšíření virtuálního počítače je tím, že zálohování serveru SQL Server na virtuálním počítači. Toho lze dosáhnout prostřednictvím [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) navržené pro automatizaci zálohování na virtuální počítač s SQL serverem.


## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

Budete muset zaregistrovat virtuální počítač Azure, která obsahuje aplikaci SQL s trezorem Recovery services. Během registrace AzureBackupWindowsWorkload rozšíření se nainstaluje na virtuálním počítači. Použití [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) rutiny k registraci virtuálního počítače.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
Příkaz vrátí **záložní kontejner** tohoto prostředku a stav bude **zaregistrovaný**.


## <a name="next-steps"></a>Další postup

- [Další informace](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) o zálohování virtuálních počítačů Azure SQL Server Poradce při potížích
- [Běžné otázky](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) o zálohování databází systému SQL Server, na kterých běží na Azure virtual machines (VM) a které používají službu Azure Backup.
