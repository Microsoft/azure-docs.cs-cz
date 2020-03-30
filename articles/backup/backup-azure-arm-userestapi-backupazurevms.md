---
title: Zálohování virtuálních počítačů Azure pomocí rozhraní REST API
description: V tomto článku se dozvíte, jak nakonfigurovat, inicializovat a spravovat operace zálohování zálohování virtuálních počítačích Azure pomocí rozhraní REST API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 4789ef1e0e09df521f8cab539d972e9e669e0a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248161"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Zálohování virtuálního počítače Azure pomocí Azure Backup přes rozhraní REST API

Tento článek popisuje, jak spravovat zálohy pro virtuální počítač Azure pomocí Azure Backup přes rozhraní REST API. Nakonfigurujte ochranu poprvé pro dříve nechráněný virtuální počítač Azure, aktivujte zálohu na vyžádání pro chráněný virtuální počítač Azure a upravte vlastnosti zálohování zálohovaného virtuálního počítače prostřednictvím rozhraní REST API, jak je vysvětleno zde.

Viz [vytvoření trezoru](backup-azure-arm-userestapi-createorupdatevault.md) a vytvoření kurzů rozhraní REST API [zásad](backup-azure-arm-userestapi-createorupdatepolicy.md) pro vytváření nových trezorů a zásad.

Předpokládejme, že chcete chránit virtuální ho virtuálního virtuálního zařízení "testVM" v rámci skupiny prostředků "testRG" do trezoru služby recovery Services "testVault", který je přítomen ve skupině prostředků "testVaultRG", s výchozí zásadou (s názvem "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurace zálohování pro nechráněný virtuální počítač Azure pomocí rozhraní REST API

### <a name="discover-unprotected-azure-vms"></a>Objevte nechráněné virtuální počítače Azure

Za prvé, trezor by měl být schopen identifikovat virtuální počítač Azure. Tato funkce se aktivuje pomocí [operace aktualizace](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Jedná se o asynchronní *operaci POST,* která zajišťuje, že úložiště získá nejnovější seznam všech nechráněných virtuálních počítačů v aktuálním předplatném a "ukládá" je. Jakmile je virtuální ms "uložendo mezipaměti", služby obnovení budou mít přístup k virtuálnímu jevu a chránit ho.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Identifikátor URI `{subscriptionId}`post `{vaultName}` `{vaultresourceGroupName}`má `{fabricName}` parametry , , . Je `{fabricName}` "Azure". Podle našeho `{vaultName}` příkladu je "testVault" a `{vaultresourceGroupName}` je "testVaultRG". Vzhledem k tomu, že všechny požadované parametry jsou uvedeny v identifikátoru URI, není potřeba samostatnétělo požadavku.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses"></a>Odezvy

Operace "aktualizace" je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a potom 200 (OK) po dokončení této operace.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|204 Žádný obsah     |         |  OK s žádný obsah vrácen      |
|202 Přijato     |         |     Accepted    |

##### <a name="example-responses"></a>Příklady odpovědí

Po odeslání požadavku *POST* je vrácena odpověď 202 (Přijato).

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
Location: https://management.azure.com/subscriptions//00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Sledování výsledné operace pomocí hlavičky "Umístění" pomocí jednoduchého příkazu *GET*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Po zjištění všech virtuálních počítačů Azure příkaz GET vrátí odpověď 204 (žádný obsah). Trezor teď může zjistit všechny virtuální ms v rámci předplatného.

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

### <a name="selecting-the-relevant-azure-vm"></a>Výběr příslušného virtuálního počítače Azure

 Můžete potvrdit, že "ukládání do mezipaměti" se provádí [výpisem všech chránitelných položek](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list) v rámci předplatného a vyhledejte požadovaný virtuální virtuální virtuální hod v odpovědi. [Odpověď na tuto operaci](#example-responses-1) také poskytuje informace o tom, jak služby recovery services identifikuje virtuální počítač.  Jakmile jste obeznámeni se vzorem, můžete tento krok přeskočit a přímo přejít k [povolení ochrany](#enabling-protection-for-the-azure-vm).

Tato operace je operace *GET.*

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

*IDENTIFIKÁTOR URI GET* má všechny požadované parametry. Není potřeba žádné další tělo požadavku.

#### <a name="responses"></a><a name="responses-1"></a>Odezvy

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     | [Seznam prostředků WorkloadProtectableItemResourceList](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses"></a><a name="example-responses-1"></a>Příklady odpovědí

Po odeslání požadavku *GET* je vrácena odpověď 200 (OK).

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
> Počet hodnot v odpovědi *GET* je omezen na 200 pro 'stránku'. Pomocí pole nextLink získáte adresu URL pro další sadu odpovědí.

Odpověď obsahuje seznam všech nechráněných virtuálních `{value}` počítačů Azure a každý obsahuje všechny informace, které služba Azure Recovery Service potřebuje ke konfiguraci zálohování. Chcete-li nakonfigurovat `{name}` zálohování, `{virtualMachineId}` poznamenejte si pole a pole v `{properties}` části. Vytvořte dvě proměnné z těchto hodnot polí, jak je uvedeno níže.

- containerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "vm;" +`{name}`
- `{virtualMachineId}`se používá [později](#example-request-body) v těle požadavku

V příkladu výše uvedené hodnoty přeložit na:

- containerName = "iaasvmcontainer;iaasvmcontainerv2;testRG;testVM"
- protectedItemName = "vm;iaasvmcontainerv2;testRG;testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Povolení ochrany virtuálního počítače Azure

Po příslušného virtuálního virtuálního zařízení je "cache" a "identifikovány", vyberte zásady k ochraně. Další informace o existujících zásadách v úložišti naleznete v [seznamu rozhraní POLICY API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Potom vyberte [příslušnou zásadu](/rest/api/backup/protectionpolicies/get) odkazem na název zásady. Chcete-li vytvořit zásady, naleznete [v kurzu k vytvoření zásad](backup-azure-arm-userestapi-createorupdatepolicy.md). V níže uvedeném příkladu je vybrána možnost "DefaultPolicy".

Povolení ochrany je asynchronní *put* operace, která vytvoří "chráněné položky".

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` jsou konstruovány výše. Je `{fabricName}` "Azure". V našem příkladu se to promítá do:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Vytvoření těla požadavku

Chcete-li vytvořit chráněnou položku, jsou následující součásti těla požadavku.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|properties     | Chráněná položka AzureIaaSVM        |Vlastnosti prostředku Chráněné položky         |

Úplný seznam definic těla požadavku a další podrobnosti naleznete v [části vytvoření dokumentu rozhraní REST API chráněné položky](https://docs.microsoft.com/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Ukázkové tělo požadavku

Následující tělo požadavku definuje vlastnosti potřebné k vytvoření chráněné položky.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

Výše `{sourceResourceId}` uvedené `{virtualMachineId}` je z [odpovědi na seznam chránitelných položek](#example-responses-1).

#### <a name="responses"></a>Odezvy

Vytvoření chráněné položky je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a potom 200 (OK) po dokončení této operace.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](https://docs.microsoft.com/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 Přijato     |         |     Accepted    |

##### <a name="example-responses"></a>Příklady odpovědí

Po odeslání *put* žádost o vytvoření chráněné položky nebo aktualizace, počáteční odpověď je 202 (Přijato) s hlavičkou umístění nebo Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-client-request-id: e1f94eef-9b2d-45c4-85b8-151e12b07d03; e1f94eef-9b2d-45c4-85b8-151e12b07d03
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: db785be0-bb20-4598-bc9f-70c9428b170b
x-ms-routing-request-id: SOUTHINDIA:20180521T073907Z:db785be0-bb20-4598-bc9f-70c9428b170b
Cache-Control: no-cache
Date: Mon, 21 May 2018 07:39:06 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Potom sledujte výslednou operaci pomocí hlavičky umístění nebo hlavičky Azure-AsyncOperation pomocí jednoduchého příkazu *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) s chráněným obsahem položky v těle odpovědi.

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

Tím se potvrdí, že ochrana je povolena pro virtuální počítače a první záloha se aktivuje podle plánu zásad.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Aktivace zálohy na vyžádání pro chráněný virtuální počítač Azure

Jakmile je virtuální počítač Azure nakonfigurovaný pro zálohování, zálohy se dějí podle plánu zásad. Můžete počkat na první naplánované zálohování nebo spustit zálohování na vyžádání kdykoli. Uchovávání informací pro zálohy na vyžádání je oddělené od uchovávání zásad zálohování a lze jej zadat na konkrétní datum a čas. Pokud není zadán, předpokládá se, že je 30 dní ode dne aktivační události zálohování na vyžádání.

Spuštění zálohy na vyžádání je operace *POST.*

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

A `{containerName}` `{protectedItemName}` jsou konstruovány [výše](#responses-1). Je `{fabricName}` "Azure". V našem příkladu se to promítá do:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body"></a>Vytvoření těla požadavku

Chcete-li spustit zálohování na vyžádání, jsou následující součásti těla požadavku.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|properties     | [Požadavek na zálohování IaaSVM](https://docs.microsoft.com/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Vlastnosti BackupRequestResource         |

Úplný seznam definic těla požadavku a další podrobnosti naleznete v [části Aktivace zálohování chráněných položek dokumentu rozhraní REST API](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body"></a>Ukázkové tělo požadavku

Následující tělo požadavku definuje vlastnosti potřebné k aktivaci zálohy pro chráněnou položku. Pokud není zadáno uchovávání informací, bude zachováno po dobu 30 dnů od okamžiku spuštění úlohy zálohování.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses"></a>Odezvy

Spuštění zálohy na vyžádání je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a potom 200 (OK) po dokončení této operace.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|202 Přijato     |         |     Accepted    |

#### <a name="example-responses"></a><a name="example-responses-3"></a>Příklady odpovědí

Po odeslání požadavku *POST* pro zálohování na vyžádání, počáteční odpověď je 202 (Přijato) s hlavičkou umístění nebo Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Content-Type-Options: nosniff
x-ms-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-client-request-id: 7df8e874-1d66-4f81-8e91-da2fe054811d; 7df8e874-1d66-4f81-8e91-da2fe054811d
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1199
x-ms-correlation-request-id: 7885ca75-c7c6-43fb-a38c-c0cc437d8810
x-ms-routing-request-id: SOUTHINDIA:20180521T083541Z:7885ca75-c7c6-43fb-a38c-c0cc437d8810
Cache-Control: no-cache
Date: Mon, 21 May 2018 08:35:41 GMT
Location: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testVaultRG;testVM/protectedItems/vm;testRG;testVM/operationResults/b8daecaa-f8f5-44ed-9f18-491a9e9ba01f?api-version=2019-05-13
X-Powered-By: ASP.NET
```

Potom sledujte výslednou operaci pomocí hlavičky umístění nebo hlavičky Azure-AsyncOperation pomocí jednoduchého příkazu *GET.*

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) s ID výsledné zálohovací úlohy v těle odpovědi.

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

Vzhledem k tomu, že úloha zálohování je dlouhotrvající operace, je třeba ji sledovat, jak je vysvětleno v [úlohách monitorování pomocí dokumentu rozhraní REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Úprava konfigurace zálohování pro chráněný virtuální počítač Azure

### <a name="changing-the-policy-of-protection"></a>Změna politiky ochrany

Chcete-li změnit zásady, pomocí kterých je virtuální počítač chráněný, můžete použít stejný formát jako [povolení ochrany](#enabling-protection-for-the-azure-vm). Stačí zadat nové ID zásad v [textu požadavku](#example-request-body) a odeslat žádost. Například: Chcete-li změnit zásady testVM z "DefaultPolicy" na "ProdPolicy", zadejte ID ProdPolicy v těle požadavku.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Odpověď bude mít stejný formát, v kterém je uvedeno [pro umožnění ochrany](#responses-2)

### <a name="stop-protection-but-retain-existing-data"></a>Zastavit ochranu, ale zachovat existující data

Chcete-li odebrat ochranu na chráněném virtuálním počítači, ale zachovat data, která jsou již zálohována, odeberte zásady v textu žádosti a odešlete žádost. Po odebrání přidružení k zásadám se již nespustí zálohování a nebudou vytvořeny žádné nové body obnovení.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Odpověď bude následovat ve stejném formátu, jak je uvedeno [pro spuštění zálohy na vyžádání](#example-responses-3). Výsledná úloha by měla být sledována, jak je vysvětleno v [úlohách monitorování pomocí dokumentu rozhraní REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Zastavení ochrany a odstranění dat

Chcete-li odebrat ochranu na chráněném virtuálním počítači a odstranit také záložní data, proveďte operaci odstranění, jak [je podrobně popsáno zde](https://docs.microsoft.com/rest/api/backup/protecteditems/delete).

Zastavení ochrany a odstranění dat je operace *DELETE.*

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

A `{containerName}` `{protectedItemName}` jsou konstruovány [výše](#responses-1). `{fabricName}`je "Azure". V našem příkladu se to promítá do:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses"></a><a name="responses-2"></a>Odezvy

*Ochrana DELETE* je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Accepted) při vytvoření jiné operace a potom 204 (NoContent) po dokončení této operace.

|Name (Název)  |Typ  |Popis  |
|---------|---------|---------|
|204 NoContent     |         |  NoContent       |
|202 Přijato     |         |     Accepted    |

> [!IMPORTANT]
> Z důvodu ochrany před náhodným odstraněním scénáře, je [funkce obnovitelného odstranění k dispozici](use-restapi-update-vault-properties.md#soft-delete-state) pro trezor služeb obnovení. Pokud je stav obnovitelného odstranění úložiště nastaven na povoleno, operace odstranění nebude okamžitě odstranit data. Bude uchováván po dobu 14 dnů a poté trvale vyčištěn. Zákazníkovi není účtován poplatek za skladování po dobu 14 dnů. Chcete-li operaci odstranění vrátit zpět, podívejte se do [oddílu zpět v části .](#undo-the-stop-protection-and-delete-data)

### <a name="undo-the-stop-protection-and-delete-data"></a>Zrušení ochrany stop a odstranění dat

Zrušení nechtěnéodstranění je podobné vytvoření položky zálohování. Po zrušení odstranění je položka zachována, ale nebudou spuštěny žádné budoucí zálohy.

Odstranění zpět je operace *PUT,* která je velmi podobná [změně zásad](#changing-the-policy-of-protection) y a/nebo [povolení ochrany](#enabling-protection-for-the-azure-vm). Stačí poskytnout záměr vrátit zpět odstranění s proměnnou *isRehydrate* v [těle požadavku](#example-request-body) a odeslat požadavek. Příklad: Chcete-li vrátit zpět odstranění pro testVM, následující tělo požadavku by měl být použit.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "protectionState": "ProtectionStopped",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "isRehydrate": true
  }
}
```

Odpověď bude následovat ve stejném formátu, jak je uvedeno [pro spuštění zálohy na vyžádání](#example-responses-3). Výsledná úloha by měla být sledována, jak je vysvětleno v [úlohách monitorování pomocí dokumentu rozhraní REST API](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Další kroky

[Obnovení dat ze zálohy virtuálního počítače Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Další informace o rozhraních API Azure Backup REST najdete v následujících dokumentech:

- [Rozhraní REST ROZHRANÍ REST ZPROSTŘEDKOVATELE Služby Azure Recovery Services](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)
