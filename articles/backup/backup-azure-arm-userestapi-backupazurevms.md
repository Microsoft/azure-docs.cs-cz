---
title: 'Azure Backup: Zálohování virtuálních počítačů Azure pomocí rozhraní REST API'
description: Spravovat zálohy operace Azure zálohování virtuálního počítače pomocí rozhraní REST API
services: backup
author: pvrk
manager: shivamg
keywords: ROZHRANÍ REST API; Zálohování virtuálních počítačů Azure; Obnovení virtuálního počítače Azure;
ms.service: backup
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: pullabhk
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 8a47d3cf346d7961e9f8b1c4fa615a2faa6b1da0
ms.sourcegitcommit: 02ce0fc22a71796f08a9aa20c76e2fa40eb2f10a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51289782"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Zálohování virtuálního počítače Azure pomocí Azure Backup přes rozhraní REST API

Tento článek popisuje, jak spravovat zálohy virtuálního počítače Azure pomocí služby Azure Backup přes rozhraní REST API. Nakonfigurujte ochranu pro první dříve nechráněného virtuálního počítače Azure, spustit zálohu na vyžádání pro chráněné virtuální počítače Azure a upravit zálohování vlastnosti zálohovaných virtuálních počítačů přes rozhraní REST API, jak je popsáno zde.

Odkazovat na [vytvořit trezor](backup-azure-arm-userestapi-createorupdatevault.md) a [vytvořit zásadu](backup-azure-arm-userestapi-createorupdatepolicy.md) kurzy rozhraní REST API můžete vytvářet nové trezory a zásady.

Předpokládejme, že chcete chránit virtuální počítač "testVM" v prostředku skupiny "testRG" do trezoru služby Recovery Services "testVault", k dispozici v rámci skupiny prostředků "testVaultRG", s výchozími zásadami (s názvem "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurace zálohování pro nechráněného virtuálního počítače Azure pomocí rozhraní REST API

### <a name="discover-unprotected-azure-vms"></a>Vyhledat nechráněné virtuální počítače Azure

Trezor nejprve měli být schopni identifikovat virtuální počítač Azure. Tím se aktivuje pomocí [operace aktualizace](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Je asynchronní *příspěvek* operace, která zajišťuje, že trezor získá nejnovější seznam všech nechráněného virtuálního počítače v rámci aktuálního předplatného a "je uloží do mezipaměti". Po virtuálního počítače je "do mezipaměti", bude mít přístup k virtuálnímu počítači a chránit služby Recovery services.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Identifikátor URI POST má `{subscriptionId}`, `{vaultName}`, `{vaultresourceGroupName}`, `{fabricName}` parametry. `{fabricName}` "Azure". Podle v našem příkladu `{vaultName}` je "testVault" a `{vaultresourceGroupName}` je "testVaultRG". Protože všechny požadované parametry jsou uvedeny v identifikátoru URI, není nutné pro samostatnou žádost subjektu.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Odpovědi

Operaci 'aktualizovat' je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Znamená to, že tato operace vytvoří jiná operace, která je třeba sledovat samostatně.

Vrátí dva odpovědi: 202 (přijato), když se vytvoří jiná operace a potom 200 (OK) po dokončení této operace.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|204 žádný obsah.     |         |  OK bez obsahu vrátil      |
|202 přijato     |         |     Přijato    |

##### <a name="example-responses"></a>Ukázkové odpovědi

Jednou *příspěvek* odeslání žádosti se vrátí odpovědi 202 (přijato).

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
X-Content-Type-Options: nosniff
x-ms-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-client-request-id: 4910609f-bb9b-4c23-8527-eb6fa2d3253f; 4910609f-bb9b-4c23-8527-eb6fa2d3253f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 43cf550d-e463-421c-8922-37e4766db27d
x-ms-routing-request-id: SOUTHINDIA:20180521T105701Z:43cf550d-e463-421c-8922-37e4766db27d
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:57:00 GMT
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Sledovat výsledné operaci pomocí hlavičky "Umístění" Díky jednoduché *získat* příkaz

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2016-12-01
```

Jakmile jsou zjištěny všechny virtuální počítače Azure, vrátí příkaz GET 204 (žádný obsah) odpověď. Trezor je teď možné ke zjišťování všech virtuálních počítačů v rámci předplatného.

```http
HTTP/1.1 204 NoContent
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-client-request-id: 25bb6345-f9fc-4406-be1a-dc6db0eefafe; 25bb6345-f9fc-4406-be1a-dc6db0eefafe
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14997
x-ms-correlation-request-id: cf6cd73b-9189-4942-a61d-878fcf76b1c1
x-ms-routing-request-id: SOUTHINDIA:20180521T105825Z:cf6cd73b-9189-4942-a61d-878fcf76b1c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 10:58:25 GMT
X-Powered-By: ASP.NET
```

### <a name="selecting-the-relevant-azure-vm"></a>Výběrem příslušné virtuální počítač Azure

 Můžete potvrdit, že se provádí "ukládání do mezipaměti" [seznam chránitelných položek](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) v rámci předplatného a vyhledejte požadovaný virtuální počítač v odpovědi. [Odpověď na tuto operaci](#example-responses-1) také nabízí informace o způsobu, jakým služby Recovery services identifikuje virtuální počítač.  Jakmile jste se seznámili se vzorem, můžete tento krok přeskočit a přejít přímo k [povoluje se ochrana](#enabling-protection-for-the-azure-vm).

Tato operace je *získat* operace.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*Získat* identifikátor URI má všechny požadované parametry. Je potřeba žádné další žádosti subjekt.

#### <a name="responses"></a>Odpovědi

|Název  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

##### <a name="example-responses"></a>Ukázkové odpovědi

Jakmile *získat* odeslání žádosti se vrátí odpověď 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-client-request-id: 40c8601a-c217-4c68-87da-01db8dac93dd; 40c8601a-c217-4c68-87da-01db8dac93dd
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14979
x-ms-correlation-request-id: 7c2cf56a-e6be-4345-96df-c27ed849fe36
x-ms-routing-request-id: SOUTHINDIA:20180521T071408Z:7c2cf56a-e6be-4345-96df-c27ed849fe36
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:14:08 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainerv2;testRG;testVM/protectableItems/vm;iaasvmcontainerv2;testRG;testVM",
      "name": "iaasvmcontainerv2;testRG;testVM",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
      "properties": {
        "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
        "virtualMachineVersion": "Compute",
        "resourceGroup": "testRG",
        "backupManagementType": "AzureIaasVM",
        "protectableItemType": "Microsoft.Compute/virtualMachines",
        "friendlyName": "testVM",
        "protectionState": "NotProtected"
      }
    },……………..

```

> [!TIP]
> Počet hodnot v *získat* odpovědi je omezená na 200 pro danou stránku. Pole "nextLink" použijte k získání adresy URL pro další sadu odpovědi.

Odpověď obsahuje seznam všech nechráněných virtuálních počítačů Azure a každý `{value}` obsahuje všechny informace požadované pro konfiguraci zálohování pomocí služby Azure Recovery. Chcete-li nakonfigurovat zálohování, pamatujte `{name}` pole a `{virtualMachineId}` v `{properties}` části. Vytvořte dvě proměnné z těchto hodnot pole, jak je uvedeno níže.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "virtuální počítač;" + `{name}`
- `{virtualMachineId}` je potřebovat později v [textu požadavku](#example-request-body)

V příkladu výše uvedených hodnot přeložit do:

- containerName = "iaasvmcontainer; iaasvmcontainerv2 testRG; testVM"
- protectedItemName = "virtuální počítač; iaasvmcontainerv2 testRG; testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Povolení ochrany pro virtuální počítač Azure

Po relevantní virtuálního počítače je "do mezipaměti" a "identifikovat", vyberte zásady k ochraně. Pokud chcete získat další informace o existujících zásad v trezoru, přečtěte si [seznamu rozhraní API zásad](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Vyberte [příslušné zásady](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) ve odkazující na název zásady. Vytvoření zásady, najdete [vytvořit zásady kurzu](backup-azure-arm-userestapi-createorupdatepolicy.md). "DefaultPolicy" je vybrán v následujícím příkladu.

Povoluje se ochrana je asynchronní *UMÍSTIT* operace, která vytváří chráněné položky.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

`{containerName}` a `{protectedItemName}` jsou vytvořený výše. `{fabricName}` "Azure". V našem příkladu to se přeloží na:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

#### <a name="create-the-request-body"></a>Vytvořit datovou část požadavku

K vytvoření chráněných položek, jsou následující komponenty datovou část požadavku.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|vlastnosti     | AzureIaaSVMProtectedItem        |Vlastnosti prostředku ProtectedItem         |

Úplný seznam definic z textu požadavku a další podrobnosti najdete v tématu [vytvořit dokument rozhraní REST API pro chráněnou položku](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Text požadavku na příkladu

Následující text požadavku definuje vlastnosti potřebné k vytvoření chráněné položky.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}` Je `{virtualMachineId}` uvedených výše z [odpovědi chránitelné položky seznamu](#example-responses-1).

#### <a name="responses"></a>Odpovědi

Vytvoření chráněné položky se zobrazí [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Znamená to, že tato operace vytvoří jiná operace, která je třeba sledovat samostatně.

Vrátí dva odpovědi: 202 (přijato), když se vytvoří jiná operace a potom 200 (OK) po dokončení této operace.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 přijato     |         |     Přijato    |

##### <a name="example-responses"></a>Ukázkové odpovědi

Jakmile odešlete *UMÍSTIT* žádost pro chráněnou položku Vytvoření nebo aktualizace, první odezvy je 202 (přijato) s hlavička umístění nebo Azure asynchronní záhlaví.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Poté můžete sledovat pomocí hlavičky location nebo Azure-AsyncOperation záhlaví s jednoduchou výsledný operace *získat* příkazu.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Po dokončení této operace vrátí 200 (OK) s obsahem chráněné položky v textu odpovědi.

```json
{
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM",
  "name": "VM;testRG;testVM",
  "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems",
  "properties": {
    "friendlyName": "testVM",
    "virtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "protectionStatus": "Healthy",
    "protectionState": "IRPending",
    "healthStatus": "Passed",
    "lastBackupStatus": "",
    "lastBackupTime": "2001-01-01T00:00:00Z",
    "protectedItemDataId": "17592691116891",
    "extendedInfo": {
      "recoveryPointCount": 0,
      "policyInconsistent": false
    },
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "backupManagementType": "AzureIaasVM",
    "workloadType": "VM",
    "containerName": "iaasvmcontainerv2;testRG;testVM",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "policyName": "DefaultPolicy"
  }
}
```

Tím potvrdíte, že je pro virtuální počítač povolit ochranu a aktivuje se první zálohování podle plánu zásady.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Spustit zálohu na vyžádání pro chráněné virtuální počítače Azure

Jakmile virtuálního počítače Azure je nakonfigurovaná pro zálohování, zálohy probíhat podle plánu zásady. Můžete počkat je první plánovanou zálohou nebo kdykoli spustit zálohu na vyžádání. Doba uchování záloh na vyžádání je oddělená od uchovávání informací zásady zálohování a je možné zadat pro konkrétní datum a čas. Pokud není zadán, předpokládá se, že se 30 dnů ode dne triggeru zálohování na vyžádání.

Aktivace zálohu na vyžádání se *příspěvek* operace.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}` a `{protectedItemName}` jsou konstruovány [nad](#responses-1). `{fabricName}` "Azure". V našem příkladu to se přeloží na:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Vytvořit datovou část požadavku

Spustit zálohu na vyžádání, jsou následující komponenty z textu požadavku.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|vlastnosti     | [IaaSVMBackupRequest](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Vlastnosti BackupRequestResource         |

Úplný seznam definic z textu požadavku a další podrobnosti najdete v tématu [aktivovat zálohování chráněných položek rozhraní REST API dokumentu](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Text požadavku na příkladu

Následující text požadavku definuje vlastnosti potřebné k aktivaci zálohování pro chráněnou položku. Pokud zachování není zadán, bude zachován po dobu 30 dnů od okamžiku aktivace úlohy zálohování.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Odpovědi

Aktivace zálohu na vyžádání je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Znamená to, že tato operace vytvoří jiná operace, která je třeba sledovat samostatně.

Vrátí dva odpovědi: 202 (přijato), když se vytvoří jiná operace a potom 200 (OK) po dokončení této operace.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|202 přijato     |         |     Přijato    |

#### <a name="example-responses"></a>Ukázkové odpovědi

Jakmile odešlete *příspěvek* žádost o zálohu na vyžádání, první odezvy je 202 (přijato) se hlavička umístění nebo Azure asynchronní záhlaví.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2016-12-01
X-Powered-By: ASP.NET
```

Poté můžete sledovat pomocí hlavičky location nebo Azure-AsyncOperation záhlaví s jednoduchou výsledný operace *získat* příkazu.

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2016-12-01
```

Po dokončení této operace vrátí 200 (OK) s ID se výsledná úloha zálohování v textu odpovědi.

```json
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-client-request-id: 5a63209d-3708-4e69-a75f-9499f4c8977c; 5a63209d-3708-4e69-a75f-9499f4c8977c
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14995
x-ms-correlation-request-id: a8b13524-2c95-445f-b107-920806f385c1
x-ms-routing-request-id: SOUTHINDIA:20180521T083723Z:a8b13524-2c95-445f-b107-920806f385c1
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:37:22 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "00000000-0000-0000-0000-000000000000",
  "status": "Succeeded",
  "startTime": "2018-05-21T08:35:40.9488967Z",
  "endTime": "2018-05-21T08:35:40.9488967Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "7ddead57-bcb9-4269-ac31-6a1b57588700"
  }
}
```

Protože úloha zálohování je dlouho běžící operace, je potřeba sledovat, jak je vysvětleno v [monitorování úloh pomocí rozhraní REST API dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Upravit konfiguraci zálohování pro chráněný virtuální počítač Azure

### <a name="changing-the-policy-of-protection"></a>Změna zásad ochrany

Chcete-li změnit zásady, které virtuální počítač je chráněný, můžete použít stejný formát jako [povoluje se ochrana](#enabling-protection-for-the-azure-vm). Stačí zadat nové ID zásad v [textu požadavku](#example-request-body) a odešlete žádost. Pro např: Chcete-li změnit zásady testVM z 'DefaultPolicy' k "ProdPolicy", zadejte id "ProdPolicy" v textu požadavku.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Odpověď bude následovat stejný formát, jak je uvedeno [pro povolení ochrany](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Zastavte ochranu při zachování stávajících dat

K odebrání ochrany na chráněném virtuálním počítači, ale zachovat data již zálohovali, odebrat zásady v textu požadavku a odeslat žádost. Po odebrání přidružení se zásadami zálohování již nebude spuštěná a jsou vytvořeny žádné nové body obnovení.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Odpověď bude následovat stejný formát, jak je uvedeno [spustit zálohu na vyžádání](#example-responses-3). Výsledná úloha musí sledovat, jak je vysvětleno v [monitorování úloh pomocí rozhraní REST API dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Zastavení ochrany a odstranění dat

K odebrání ochrany na chráněném virtuálním počítači a odstranit zálohovaná data, provést operaci odstranění, jak je uvedeno [tady](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Zastavení ochrany a odstranění dat je *odstranit* operace.

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2016-12-01
```

`{containerName}` a `{protectedItemName}` jsou konstruovány [nad](#responses-1). `{fabricName}` je "Azure". V našem příkladu to se přeloží na:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2016-12-01
```

### <a name="responses"></a>Odpovědi

*Odstranit* ochrana je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Znamená to, že tato operace vytvoří jiná operace, která je třeba sledovat samostatně.

Vrátí dva odpovědi: 202 (přijato), když se vytvoří jiná operace a potom 204 (NoContent) po dokončení této operace.

|Název  |Typ  |Popis  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 přijato     |         |     Přijato    |

## <a name="next-steps"></a>Další postup

[Obnovení dat ze zálohy Azure Virtual machine](backup-azure-arm-userestapi-restoreazurevms.md).

Další informace o rozhraní REST API služby Azure Backup najdete v následujících dokumentech:

- [Rozhraní REST API poskytovatele Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
