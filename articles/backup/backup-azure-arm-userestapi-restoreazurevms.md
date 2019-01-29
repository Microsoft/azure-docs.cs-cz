---
title: 'Azure Backup: Obnovení virtuálních počítačů Azure pomocí rozhraní REST API'
description: Spravovat operace obnovení zálohování virtuálních počítačů Azure pomocí rozhraní REST API
services: backup
author: pvrk
manager: shivamg
keywords: ROZHRANÍ REST API; Zálohování virtuálních počítačů Azure; Obnovení virtuálního počítače Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: pullabhk
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: 4a65e8a855b9be797c1ceeacf4b74fea74697d00
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100190"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Obnovení pomocí rozhraní REST API služby Azure Virtual machines

Po dokončení zálohování Azure virtuální počítače pomocí Azure Backup jeden obnovit celý Azure Virtual machines nebo disky nebo souborů ze stejné záložní kopie. Tento článek popisuje, jak obnovit virtuální počítač Azure nebo disky pomocí rozhraní REST API.

Pro žádné operace obnovení, je nutné nejprve identifikovat bod obnovení relevantní.

## <a name="select-recovery-point"></a>Vyberte bod obnovení

Dostupné body obnovení záložní položky je možné uvést pomocí [seznamu bod obnovení rozhraní REST API](https://docs.microsoft.com/rest/api/backup/recoverypoints/list). Je jednoduchý *získat* operace se všemi příslušnými hodnotami.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

`{containerName}` a `{protectedItemName}` jsou konstruovány [tady](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` je "Azure".

*Získat* identifikátor URI má všechny požadované parametry. Není nutné pro obsahu další žádosti

### <a name="responses"></a>Odezvy

|Name  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Příklad odpovědi

Jednou *získat* identifikátoru URI je odeslán, se vrátí odpověď 200 (OK).

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

Bod obnovení se určuje podle `{name}` pole v odpovědi výše.

## <a name="restore-disks"></a>Obnovit disky

Pokud je potřeba k přizpůsobení vytvoření virtuálního počítače z zálohovaná data, jeden pouze obnovení disků do účtu zvoleného úložiště a vytvoření virtuálního počítače z těchto disků podle svých požadavků. Účet úložiště by měl být ve stejné oblasti jako trezor služby recovery services a by neměl být zónově redundantní. Disky, jakož i konfigurace zálohovaného virtuálního počítače ("vmconfig.json") se uloží v daný účet úložiště.

Aktivuje obnovení disků je *příspěvek* požadavku. Chcete-li získat další informace o obnovení disků zopakovat, přečtěte si ["aktivovat obnovení" rozhraní REST API](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

`{containerName}` a `{protectedItemName}` jsou konstruovány [tady](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` "Azure" a `{recoveryPointId}` je `{name}` pole bodu obnovení uvedeného [nad](#example-response).

### <a name="create-request-body"></a>Vytvoření textu požadavku

Aktivovat disk obnovení ze zálohy virtuálního počítače Azure, jsou následující komponenty z textu požadavku.

|Name  |Typ  |Popis  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Úplný seznam definic z textu požadavku a další podrobnosti najdete v tématu [aktivovat obnovení rozhraní REST API dokumentu](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Příklad požadavku

Následující text požadavku definuje vlastnosti požadované pro aktivaci obnovení disku.

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

Aktivuje obnovení disků se [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Znamená to, že tato operace vytvoří jiná operace, která je třeba sledovat samostatně.

Vrátí dva odpovědi: 202 (přijato), když se vytvoří jiná operace a pak 200 (OK) po dokončení této operace.

|Name  |Typ  |Popis  |
|---------|---------|---------|
|202 přijato     |         |     Přijato    |

#### <a name="example-responses"></a>Ukázkové odpovědi

Jakmile odešlete *příspěvek* identifikátor URI pro aktivaci obnovení disků, první odezvy je 202 (přijato) se hlavička umístění nebo Azure asynchronní záhlaví.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Poté můžete sledovat pomocí hlavičky location nebo Azure-AsyncOperation záhlaví s jednoduchou výsledný operace *získat* příkazu.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

Po dokončení této operace vrátí 200 (OK) s ID v textu odpovědi se výsledná úloha obnovení.

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

Úlohy zálohování je dlouho běžící operace, ho mají být sledovány jak je vysvětleno v [monitorování úloh pomocí rozhraní REST API dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

Po dokončení dlouho běžící úlohy se disky a konfigurace zálohovaného virtuálního počítače ("VMConfig.json") bude v daný účet úložiště.

## <a name="restore-as-another-virtual-machine"></a>Obnovit jako jiný virtuální počítač

[Vyberte bod obnovení](#select-recovery-point) a vytvořit datovou část požadavku uvedená níže vytvořte další Azure virtuální počítač s daty z bodu obnovení.

Následující text požadavku definuje vlastnosti požadované pro aktivaci obnovení virtuálního počítače.

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
        "api-version": "2016-12-01",
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

Odpověď by měla být zpracovány v stejným způsobem jako [bylo vysvětleno výše pro obnovení disků](#response).

## <a name="next-steps"></a>Další postup

Další informace o rozhraní REST API služby Azure Backup najdete v následujících dokumentech:

- [Rozhraní REST API poskytovatele Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)