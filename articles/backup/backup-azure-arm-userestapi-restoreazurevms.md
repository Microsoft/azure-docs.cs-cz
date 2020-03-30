---
title: Obnovení virtuálních počítačů Azure pomocí rozhraní REST API
description: V tomto článku se dozvíte, jak spravovat operace obnovení zálohování virtuálního počítače Azure pomocí rozhraní REST API.
ms.topic: conceptual
ms.date: 09/12/2018
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4990d815721ddbdde8e6eb6ebf8d6d3b49adc700
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173388"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Obnovení virtuálních počítačů Azure pomocí rozhraní REST API

Po dokončení zálohování virtuálního počítače Azure pomocí azure backupu je možné obnovit celé virtuální počítače Azure nebo disky nebo soubory ze stejné záložní kopie. Tento článek popisuje, jak obnovit virtuální počítač Azure nebo disky pomocí rozhraní REST API.

Pro každou operaci obnovení je třeba nejprve identifikovat příslušný bod obnovení.

## <a name="select-recovery-point"></a>Vybrat bod obnovení

Dostupné body obnovení položky zálohy mohou být uvedeny pomocí [bodu obnovení seznamu ROZHRANÍ REST API](https://docs.microsoft.com/rest/api/backup/recoverypoints/list). Jedná se o jednoduchou operaci *GET* se všemi příslušnými hodnotami.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` jsou zde [postaveny.](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1) `{fabricName}`je "Azure".

*IDENTIFIKÁTOR URI GET* má všechny požadované parametry. Není nutné další tělo požadavku

### <a name="responses"></a>Odezvy

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [Seznam prostředků RecoveryPoint](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Příklad odpovědi

Po odeslání *identifikátoru* URI get je vrácena odpověď 200 (OK).

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

Bod obnovení je identifikován `{name}` s polem ve výše uvedené odpovědi.

## <a name="restore-disks"></a>Obnovení disků

Pokud je potřeba přizpůsobit vytvoření virtuálního počítače ze záložních dat, stačí obnovit disky do vybraného účtu úložiště a vytvořit virtuální hod z těchto disků podle jejich požadavků. Účet úložiště by měl být ve stejné oblasti jako trezor služeb pro obnovení a neměl by být zónový redundantní. Disky i konfigurace zálohovaného virtuálního počítače ("vmconfig.json") budou uloženy v daném účtu úložiště.

Spuštění obnovení disků je požadavek *POST.* Další informace o operaci Obnovení disků naleznete v [rozhraní REST API "obnovení aktivační události"](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` jsou zde [postaveny.](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1) `{fabricName}`je "Azure" `{recoveryPointId}` a `{name}` je pole bodu obnovení [výše](#example-response)uvedené .

### <a name="create-request-body"></a>Vytvořit tělo požadavku

Chcete-li aktivovat obnovení disku ze zálohy virtuálního počítače Azure, jsou následující součásti těla požadavku.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|properties     | [Požadavek iaaSVMRestore](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    Vlastnosti RestoreRequestResourceProperties     |

Úplný seznam definic těla požadavku a další podrobnosti naleznete v [části aktivace dokumentu rozhraní REST API .](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)

#### <a name="example-request"></a>Příklad požadavku

Následující tělo požadavku definuje vlastnosti potřebné ke spuštění obnovení disku.

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

### <a name="response"></a>Odpověď

Spuštění disku obnovení je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a potom 200 (OK) po dokončení této operace.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|202 Přijato     |         |     Accepted    |

#### <a name="example-responses"></a>Příklady odpovědí

Po odeslání *identifikátoru URI POST* pro aktivaci disků obnovení je počáteční odpověď 202 (Přijato) s hlavičkou umístění nebo hlavičkou Azure-async-header.

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

Potom sledujte výslednou operaci pomocí hlavičky umístění nebo hlavičky Azure-AsyncOperation pomocí jednoduchého příkazu *GET.*

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) s ID výsledné úlohy obnovení v těle odpovědi.

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

Vzhledem k tomu, že úloha zálohování je dlouhotrvající operace, měla by být sledována, jak je vysvětleno v [úlohách monitorování pomocí dokumentu rozhraní REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

Po dokončení dlouho běžící úlohy budou disky a konfigurace zálohovaného virtuálního počítače ("VMConfig.json") k dispozici v daném účtu úložiště.

## <a name="restore-as-another-virtual-machine"></a>Obnovení jako jiný virtuální počítač

[Vyberte bod obnovení](#select-recovery-point) a vytvořte tělo požadavku, jak je uvedeno níže, abyste vytvořili další virtuální počítač Azure s daty z bodu obnovení.

Následující tělo požadavku definuje vlastnosti potřebné k aktivaci obnovení virtuálního počítače.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2019-05-13",
      "parameters": {
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
      }
    }
}
```

Odpověď by měla být zpracována stejným způsobem, jak [je vysvětleno výše pro obnovení disků](#response).

## <a name="next-steps"></a>Další kroky

Další informace o rozhraních API Azure Backup REST najdete v následujících dokumentech:

- [Rozhraní REST ROZHRANÍ REST ZPROSTŘEDKOVATELE Služby Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
