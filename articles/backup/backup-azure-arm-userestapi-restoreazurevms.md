---
title: Obnovení virtuálních počítačů Azure pomocí REST API
description: V tomto článku se dozvíte, jak spravovat operace obnovení zálohy virtuálního počítače Azure pomocí REST API.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: da6b4cd6134f0cd1fd3d6e04e814bbf8aec9b07d
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452148"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Obnovení virtuálních počítačů Azure pomocí REST API

Po dokončení zálohování virtuálního počítače Azure pomocí Azure Backup může jeden obnovit celý virtuální počítač nebo disky nebo soubory Azure ze stejné záložní kopie. Tento článek popisuje, jak obnovit virtuální počítač nebo disky Azure pomocí REST API.

Pro všechny operace obnovení je nutné nejprve identifikovat příslušný bod obnovení.

## <a name="select-recovery-point"></a>Vybrat bod obnovení

Dostupné body obnovení zálohované položky mohou být uvedeny pomocí [REST APIho bodu obnovení seznamu](/rest/api/backup/recoverypoints/list). Jedná se o jednoduchou operaci *Get* se všemi relevantními hodnotami.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

`{containerName}`A `{protectedItemName}` jsou sestaveny [zde](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` je "Azure".

Identifikátor URI *Get* má všechny požadované parametry. Není potřeba další text žádosti.

### <a name="responses"></a>Odpovědi

|Název  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Příklad odpovědi

Po odeslání identifikátoru URI *Get* se vrátí odpověď 200 (ok).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

Bod obnovení je označený `{name}` polem ve výše uvedené reakci.

## <a name="restore-operations"></a>Operace obnovení

Po výběru [relevantního bodu obnovení](#select-recovery-point)pokračujte a aktivujte operaci obnovení.

***Všechny operace obnovení v zálohovaných položkách se provádějí se stejným rozhraním API pro *odeslání* . V rámci scénářů obnovení se mění pouze tělo žádosti.***

> [!IMPORTANT]
> Všechny podrobnosti o různých možnostech obnovení a jejich závislostech jsou uvedeny [zde](./backup-azure-arm-restore-vms.md#restore-options). Než budete pokračovat v aktivaci těchto operací, přečtěte si je.

Aktivace operací obnovení je požadavek *post* . Pokud chcete získat další informace o rozhraní API, přečtěte si [téma "Trigger Restore" REST API](/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

`{containerName}`A `{protectedItemName}` jsou sestaveny [zde](backup-azure-arm-userestapi-backupazurevms.md#example-responses-to-get-operation). `{fabricName}` je "Azure" a `{recoveryPointId}` je `{name}` pole bodu obnovení uvedeného [výše](#example-response).

Po získání bodu obnovení musíme pro příslušný scénář obnovení vytvořit text žádosti. Následující části popisují tělo žádosti pro každý scénář.

- [Obnovit disky](#restore-disks)
- [Nahradit disky](#replace-disks-in-a-backed-up-virtual-machine)
- [Obnovit jako nový virtuální počítač](#restore-as-another-virtual-machine)

### <a name="restore-response"></a>Odpověď na obnovení

Triggerem jakékoli operace obnovení je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a po dokončení této operace 200 (OK).

|Název  |Typ  |Popis  |
|---------|---------|---------|
|202 přijato     |         |     Přijato    |

#### <a name="example-responses"></a>Příklady odpovědí

Jakmile odešlete identifikátor *URI* pro aktivaci disků pro obnovení, počáteční odpověď je 202 (přijato) s hlavičkou umístění nebo Azure-Async-Header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Pak Sledujte výslednou operaci pomocí záhlaví umístění nebo hlavičky Azure-AsyncOperation jednoduchým příkazem *Get* .

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) ID výsledné úlohy obnovení v těle odpovědi.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Vzhledem k tomu, že úloha obnovení je dlouhodobě spuštěná operace, měla by být sledována, jak je vysvětleno v tématu [Monitorování úloh pomocí REST API dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="restore-disks"></a>Obnovit disky

Pokud je potřeba přizpůsobit vytvoření virtuálního počítače ze záložních dat, stačí obnovit disky do zvoleného účtu úložiště a z těchto disků vytvořit virtuální počítač podle jejich požadavků. Účet úložiště by měl být ve stejné oblasti jako úložiště Recovery Services a neměl by být zóna redundantní. Disky a také konfigurace zálohovaného virtuálního počítače ("vmconfig.jszapnuté") se uloží do daného účtu úložiště. Jak je vysvětleno [výše](#restore-operations), níže je uveden relevantní text žádosti o obnovení disků.

#### <a name="create-request-body"></a>Vytvořit text žádosti

Pokud chcete aktivovat obnovení disku ze zálohy virtuálního počítače Azure, níže jsou uvedené součásti textu žádosti.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Úplný seznam definic těla žádosti a další podrobnosti najdete v tématu [Aktivace obnovení REST API dokumentu](/rest/api/backup/restores/trigger#request-body).

##### <a name="example-request"></a>Příklad požadavku

Následující text žádosti definuje vlastnosti vyžadované k aktivaci obnovení disku.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="restore-disks-selectively"></a>Selektivní obnovení disků

Pokud jste [selektivně zálohovali disky](backup-azure-arm-userestapi-backupazurevms.md#excluding-disks-in-azure-vm-backup), pak je v [souhrnu bodů obnovení](#select-recovery-point) k dispozici aktuální seznam zálohovaných disků a [podrobná odpověď](/rest/api/backup/recoverypoints/get). Můžete také selektivně obnovit disky a další podrobnosti najdete [tady](selective-disk-backup-restore.md#selective-disk-restore). Chcete-li selektivně obnovit disk ze seznamu zálohovaných disků, najděte logickou jednotku disku z odpovědi bodu obnovení a přidejte vlastnost **restoreDiskLunList** do [výše uvedeného textu žádosti](#example-request) , jak je uvedeno níže.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "RestoreDisks",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "encryptionDetails": {
          "encryptionEnabled": false
        },
        "restoreDiskLunList" : [0]
    }
}

```

Jakmile Vysledujete odpověď, jak je vysvětleno [výše](#responses), a je dokončena dlouho spuštěná úloha, budou na daném účtu úložiště k dispozici disky a konfigurace zálohovaného virtuálního počítače ("VMConfig.json").

### <a name="replace-disks-in-a-backed-up-virtual-machine"></a>Nahrazení disků v zálohovaném virtuálním počítači

I když obnovení disků vytvoří disky z bodu obnovení, nahradí disky aktuální disky zálohovaného virtuálního počítače disky z bodu obnovení. Jak je vysvětleno [výše](#restore-operations), je níže uveden příslušný text žádosti o nahrazení disků.

#### <a name="create-request-body"></a>Vytvořit text žádosti

Chcete-li aktivovat nahrazení disku ze zálohy virtuálního počítače Azure, je nutné, aby tyto součásti textu žádosti byly.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Úplný seznam definic těla žádosti a další podrobnosti najdete v tématu [Aktivace obnovení REST API dokumentu](/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Příklad požadavku

Následující text žádosti definuje vlastnosti vyžadované k aktivaci obnovení disku.

```json
{
    "properties": {
        "objectType": "IaasVMRestoreRequest",
        "recoveryPointId": "20982486783671",
        "recoveryType": "OriginalLocation",
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",  
        "region": "westus",
        "createNewCloudService": false,
        "originalStorageAccountOption": false,
        "affinityGroup": "",
        "diskEncryptionSetId": null,
        "subnetId": null,
        "targetDomainNameId": null,
        "targetResourceGroupId": null,
        "targetVirtualMachineId": null,
        "virtualNetworkId": null
     }
}

```

### <a name="restore-as-another-virtual-machine"></a>Obnovit jako jiný virtuální počítač

Jak je vysvětleno [výše](#restore-operations), následující text žádosti definuje vlastnosti vyžadované k aktivaci obnovení virtuálního počítače.

```json
{
  "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
     }
 }
```

Odpověď by měla být zpracována stejným způsobem, jak [je vysvětleno výše pro obnovení disků](#responses).

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních REST API Azure Backup najdete v následujících dokumentech:

- [Poskytovatel Azure Recovery Services REST API](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
