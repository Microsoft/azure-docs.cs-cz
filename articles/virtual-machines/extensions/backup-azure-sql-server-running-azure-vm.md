---
title: Zálohování Azure pro SQL Server spuštěné ve virtuálním počítači Azure
description: V tomto článku se dozvíte, jak zaregistrovat Azure Backup v SQL Serveru spuštěném ve virtuálním počítači Azure.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247380"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Zálohování Azure pro SQL Server spuštěné ve virtuálním počítači Azure

Azure Backup, mimo jiné nabídky, poskytuje podporu pro zálohování úloh, jako je SQL Server běží v virtuálních počítačích Azure. Vzhledem k tomu, že aplikace SQL běží v rámci virtuálního počítače Azure, služba zálohování potřebuje oprávnění pro přístup k aplikaci a načíst potřebné podrobnosti.
K tomu Azure Backup nainstaluje rozšíření **AzureBackupWindowsWorkload** na virtuální počítač, ve kterém je spuštěn SQL Server, během procesu registrace aktivovanéuživatelem.

## <a name="prerequisites"></a>Požadavky

Seznam podporovaných scénářů najdete v [matici podpory](../../backup/sql-support-matrix.md#scenario-support) podporované službou Azure Backup.

## <a name="network-connectivity"></a>Připojení k síti

Azure Backup podporuje značky NSG, nasazuje proxy server nebo uvedené rozsahy IP adres. podrobnosti o každé z metod naleznete v tomto [článku](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Schéma rozšíření

Schéma rozšíření a hodnoty vlastností jsou hodnoty konfigurace (nastavení za běhu), které služba předává rozhraní CRP API. Tyto hodnoty konfigurace se používají při registraci a upgradu. **Rozšíření AzureBackupWindowsWorkload** také používá toto schéma. Schéma je přednastaveno. nový parametr lze přidat do pole objectStr

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

Name (Název) | Hodnota/příklad | Datový typ
 --- | --- | ---
locale | cs-cz  |  řetězec
Taskid | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | řetězec
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxTItOGRjNy00ZGE5LWI4NTMtMjdjYTJjhNDZllzlz2ZkIiwiSWRNZ210Q29u dGGFpbmVySWQiOjM0NTY3ODg5LCJZXNvdXJJJJJUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODITKwOWMyMGVjNjViiiwiU3Vic2NyaXB0aW9uSWQiOi JkNGEzOTtliny1iYjAyLTQ2MWMtODdmY1jNTM5ODI3ZTgzNTQILCLCJVmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTTCtNWMzN zNhYjg3OTcyIn0sIn00YW1wTGlzdCI6W3siU2VydmljZ5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | řetězec
příkazStartTimeUTCTicks | "636967192566036845"  | řetězec
vmTyp  | "microsoft.compute/virtualmachines"  | řetězec
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxTItOGRjNy00ZGE5LWI4NTMtMjdjYTJjhNDZllzlz2ZkIiwiSWRNZ210Q29u dGGFpbmVySWQiOjM0NTY3ODg5LCJZXNvdXJJJJJUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODITKwOWMyMGVjNjViiiwiU3Vic2NyaXB0aW9uSWQiOi JkNGEzOTtliny1iYjAyLTQ2MWMtODdmY1jNTM5ODI3ZTgzNTQILCLCJVmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTTCtNWMzN zNhYjg3OTcyIn0sIn00YW1wTGlzdCI6W3siU2VydmljZ5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | řetězec
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | řetězec
stavBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | řetězec

## <a name="template-deployment"></a>Nasazení šablon

Doporučujeme přidat rozšíření AzureBackupWindowsWorkload do virtuálního počítače je povolením zálohování SQL Serveru na virtuálním počítači. Toho lze dosáhnout prostřednictvím [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) určené pro automatizaci zálohování na virtuálním počítači sql serveru.

## <a name="powershell-deployment"></a>Nasazení PowerShellu

Musíte "zaregistrovat" virtuální počítač Azure, který obsahuje aplikaci SQL s trezorem služeb obnovení. Během registrace se na virtuálním počítači nainstaluje rozšíření AzureBackupWindowsWorkload. Pomocí rutiny [Register-AzRecoveryServicesBackupBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) zaregistrujte virtuální ho.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Příkaz vrátí **záložní kontejner** tohoto prostředku a stav bude **zaregistrován**.

## <a name="next-steps"></a>Další kroky

- [Další informace](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) o pokynech pro řešení potíží s zálohováním virtuálních počítačů Azure SQL Server
- [Časté otázky](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) týkající se zálohování databází SQL Serveru, které běží na virtuálních počítačích Azure (VM) a které používají službu Azure Backup.
