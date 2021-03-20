---
title: Zálohování virtuálních počítačů Azure pomocí REST API
description: V tomto článku se dozvíte, jak nakonfigurovat, iniciovat a spravovat operace zálohování pro zálohování virtuálních počítačů Azure pomocí REST API.
ms.topic: conceptual
ms.date: 08/03/2018
ms.assetid: b80b3a41-87bf-49ca-8ef2-68e43c04c1a3
ms.openlocfilehash: 9ba22c51c7a6c26a232ed20aec21fc83d2c54b37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92171454"
---
# <a name="back-up-an-azure-vm-using-azure-backup-via-rest-api"></a>Zálohování virtuálního počítače Azure pomocí Azure Backup přes REST API

Tento článek popisuje, jak spravovat zálohy pro virtuální počítač Azure pomocí Azure Backup přes REST API. Nakonfigurujte ochranu poprvé u dříve nechráněného virtuálního počítače Azure, aktivujte zálohování na vyžádání pro chráněný virtuální počítač Azure a upravte vlastnosti zálohy zálohovaného virtuálního počítače prostřednictvím REST API, jak je popsáno zde.

V tématu [Vytvoření trezoru](backup-azure-arm-userestapi-createorupdatevault.md) a [Vytvoření zásad](backup-azure-arm-userestapi-createorupdatepolicy.md) REST API výukových kurzů pro vytváření nových trezorů a zásad.

Předpokládejme, že chcete chránit virtuální počítač "testVM" v rámci skupiny prostředků "testRG" do trezoru Recovery Services "testVault", který je součástí skupiny prostředků "testVaultRG", s výchozími zásadami (s názvem "DefaultPolicy").

## <a name="configure-backup-for-an-unprotected-azure-vm-using-rest-api"></a>Konfigurace zálohování nechráněného virtuálního počítače Azure pomocí REST API

### <a name="discover-unprotected-azure-vms"></a>Zjišťování nechráněných virtuálních počítačů Azure

Nejdřív by měl být trezor schopný identifikovat virtuální počítač Azure. Tato operace se aktivuje pomocí [operace aktualizace](/rest/api/backup/protectioncontainers/refresh). Je to asynchronní operace *post*  , která zajišťuje, že trezor získá nejnovější seznam všech NECHRÁNĚNÝCH virtuálních počítačů v aktuálním předplatném a ukládá je do mezipaměti. Jakmile je virtuální počítač uložený v mezipaměti, služba Recovery Services bude moct přistupovat k virtuálnímu počítači a chránit ho.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01
```

Identifikátor URI příspěvku má `{subscriptionId}` , `{vaultName}` , `{vaultresourceGroupName}` , `{fabricName}` parametry. `{fabricName}`Je "Azure". Podle našeho příkladu `{vaultName}` je "testVault" a `{vaultresourceGroupName}` je "testVaultRG". Všechny požadované parametry jsou uvedeny v identifikátoru URI, takže není nutné, aby bylo samostatné tělo požadavku.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01
```

#### <a name="responses-to-refresh-operation"></a>Odpovědi na operaci aktualizace

Operace Refresh je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a po dokončení této operace 200 (OK).

|Název  |Typ  |Description  |
|---------|---------|---------|
|204 bez obsahu     |         |  OK bez vráceného obsahu      |
|202 přijato     |         |     Přijato    |

##### <a name="example-responses-to-refresh-operation"></a>Příklady odpovědí na operaci aktualizace

Po odeslání žádosti *post* se vrátí odpověď 202 (přijato).

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

Pomocí jednoduchého příkazu *Get* Sledujte výslednou operaci pomocí hlavičky Location (umístění).

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/operationResults/aad204aa-a5cf-4be2-a7db-a224819e5890?api-version=2019-05-13
```

Po zjištění všech virtuálních počítačů Azure vrátí příkaz GET odpověď 204 (bez obsahu). Trezor teď dokáže vyhledat libovolný virtuální počítač v rámci předplatného.

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

### <a name="selecting-the-relevant-azure-vm"></a>Výběr relevantního virtuálního počítače Azure

 Můžete potvrdit, že se ukládá do mezipaměti [výpisem všech chráněných položek](/rest/api/backup/backupprotectableitems/list) v rámci předplatného a vyhledat požadovaný virtuální počítač v odpovědi. [Odezva této operace](#example-responses-to-get-operation) také poskytuje informace o tom, jak Recovery Services identifikuje virtuální počítač.  Až budete s vzorem obeznámeni, můžete tento krok přeskočit a přímo přejít na [Povolení ochrany](#enabling-protection-for-the-azure-vm).

Tato operace je operace *Get* .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter=backupManagementType eq 'AzureIaasVM'
```

Identifikátor URI *Get* má všechny požadované parametry. Není potřeba žádný další text žádosti.

#### <a name="responses-to-get-operation"></a>Odpovědi na operaci získání

|Název  |Typ  |Description  |
|---------|---------|---------|
|200 OK     | [WorkloadProtectableItemResourceList](/rest/api/backup/backupprotectableitems/list#workloadprotectableitemresourcelist)        |       OK |

#### <a name="example-responses-to-get-operation"></a>Příklady odpovědí na operaci get

Po odeslání žádosti o *získání* se vrátí odpověď 200 (ok).

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
> Počet hodnot v odpovědi *Get* je omezený na 200 pro ' Page '. K získání adresy URL pro další sadu odpovědí použijte pole ' nextLink '.

Odpověď obsahuje seznam všech nechráněných virtuálních počítačů Azure a každý z nich `{value}` obsahuje všechny informace, které služba Azure Recovery vyžaduje ke konfiguraci zálohování. Pokud chcete nakonfigurovat zálohování, poznamenejte si `{name}` pole a `{virtualMachineId}` pole v `{properties}` části. Sestavte dvě proměnné z těchto hodnot polí, jak je uvedeno níže.

- ContainerName = "iaasvmcontainer;" +`{name}`
- protectedItemName = "VM;" + `{name}`
- `{virtualMachineId}`se používá později v [textu žádosti](#example-request-body) .

V příkladu jsou výše uvedené hodnoty přeloženy na:

- ContainerName = "iaasvmcontainer; iaasvmcontainerv2; testRG; testVM"
- protectedItemName = "VM; iaasvmcontainerv2; testRG; testVM"

### <a name="enabling-protection-for-the-azure-vm"></a>Povolení ochrany pro virtuální počítač Azure

Až bude příslušný virtuální počítač uložený v mezipaměti a identifikovaný, vyberte zásadu, která se má chránit. Pokud chcete získat další informace o existujících zásadách v trezoru, přečtěte si téma [rozhraní API zásad seznamu](/rest/api/backup/backuppolicies/list). Pak vyberte [příslušnou zásadu](/rest/api/backup/protectionpolicies/get) odkazem na název zásady. Pokud chcete vytvořit zásady, Projděte si [kurz vytvoření zásad](backup-azure-arm-userestapi-createorupdatepolicy.md). V následujícím příkladu je vybrán "DefaultPolicy".

Povolení ochrany je asynchronní operace *Put* , která vytvoří chráněnou položku.

```http
https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}`A `{protectedItemName}` jsou sestaveny výše. `{fabricName}`Je "Azure". V našem příkladu se to týká:

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="create-the-request-body"></a>Vytvoření textu žádosti

Chcete-li vytvořit chráněnou položku, níže jsou uvedené součásti textu žádosti.

|Název  |Typ  |Description  |
|---------|---------|---------|
|properties     | AzureIaaSVMProtectedItem        |Vlastnosti prostředku ProtectedItem         |

Úplný seznam definic těla žádosti a další podrobnosti najdete v tématu [Vytvoření chráněné položky REST API dokumentu](/rest/api/backup/protecteditems/createorupdate#request-body).

##### <a name="example-request-body"></a>Příklad textu žádosti

Následující text požadavku definuje vlastnosti vyžadované k vytvoření chráněné položky.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy"
  }
}
```

`{sourceResourceId}`Je `{virtualMachineId}` výše zmíněná [odpověď na seznam položek](#example-responses-to-get-operation), které jsou v seznamu.

#### <a name="responses-to-create-protected-item-operation"></a>Odpovědi na operaci vytvoření chráněné položky

Vytvoření chráněné položky je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a po dokončení této operace 200 (OK).

|Název  |Typ  |Description  |
|---------|---------|---------|
|200 OK     |    [ProtectedItemResource](/rest/api/backup/protecteditemoperationresults/get#protecteditemresource)     |  OK       |
|202 přijato     |         |     Přijato    |

##### <a name="example-responses-to-create-protected-item-operation"></a>Příklady odpovědí na vytvoření operace chráněné položky

Jakmile odešlete žádost o *vložení* pro vytvoření nebo aktualizaci chráněné položky, počáteční odpověď je 202 (přijato) s hlavičkou umístění nebo Azure-Async-Header.

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

Pak Sledujte výslednou operaci pomocí záhlaví umístění nebo hlavičky Azure-AsyncOperation jednoduchým příkazem *Get* .

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) k obsahu chráněné položky v těle odpovědi.

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

Tím se potvrdí, že je pro virtuální počítač povolená ochrana, a první záloha se spustí podle plánu zásad.

### <a name="excluding-disks-in-azure-vm-backup"></a>Vyloučení disků v zálohování virtuálních počítačů Azure

Azure Backup taky nabízí způsob, jak selektivně zálohovat podmnožinu disků na virtuálním počítači Azure. Další podrobnosti najdete [tady](selective-disk-backup-restore.md). Pokud chcete selektivně zálohovat málo disků během povolování ochrany, měl by být [během povolování ochrany](#example-request-body)v následujícím fragmentu kódu tento text žádosti.

```json
{
"properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/DefaultPolicy",
    "extendedProperties":  {
      "diskExclusionProperties":{
          "diskLunList":[0,1],
          "isInclusionList":true
        }
    }
}
}
```

V textu žádosti výše se seznam disků, které mají být zálohovány, uvádí v části rozšířené vlastnosti.

|Vlastnost  |Hodnota  |
|---------|---------|
|diskLunList     | Seznam LOGICKÝch jednotek disku je seznam *logických jednotek datových disků*. **Disk s operačním systémem je vždycky zálohovaný a není potřeba ho zmínit**.        |
|IsInclusionList     | Měla by být **true** , aby logické jednotky LUN byly zahrnuty během zálohování. Pokud je **hodnota false**, budou se výše uvedené logické jednotky LUN vyloučit.         |

Pokud tedy požadavek zálohuje jenom disk s operačním systémem, _všechny_ datové disky by se měly vyloučit. Jednodušším způsobem je říci, že by neměly být zahrnuté žádné datové disky. Takže seznam logických jednotek disku bude prázdný a **IsInclusionList** bude **pravda**. Podobně můžete si představit, jaký je jednodušší způsob výběru podmnožiny: několik disků by se měly vždycky vyloučit nebo by se měly vždy zahrnout i některé disky. Vyberte seznam logických jednotek a hodnotu logické proměnné odpovídajícím způsobem.

## <a name="trigger-an-on-demand-backup-for-a-protected-azure-vm"></a>Aktivace zálohování na vyžádání pro chráněný virtuální počítač Azure

Jakmile je virtuální počítač Azure nakonfigurovaný pro zálohování, zálohování proběhne podle plánu zásad. Můžete počkat na první naplánovanou zálohu nebo kdykoli aktivovat zálohování na vyžádání. Uchovávání záloh na vyžádání je oddělené od uchování zásad zálohování a je možné je zadat na konkrétní datum a čas. Pokud tento parametr nezadáte, předpokládá se, že se jedná o 30 dní od data triggeru zálohování na vyžádání.

Aktivace zálohování na vyžádání je operace *post* .

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

`{containerName}`A `{protectedItemName}` jsou sestaveny [výše](#responses-to-get-operation). `{fabricName}`Je "Azure". V našem příkladu se to týká:

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM/backup?api-version=2016-12-01
```

### <a name="create-the-request-body-for-on-demand-backup"></a>Vytvoření textu žádosti pro zálohování na vyžádání

Chcete-li aktivovat zálohování na vyžádání, níže jsou uvedené součásti textu žádosti.

|Název  |Typ  |Description  |
|---------|---------|---------|
|properties     | [IaaSVMBackupRequest](/rest/api/backup/backups/trigger#iaasvmbackuprequest)        |Vlastnosti BackupRequestResource         |

Úplný seznam definic těla žádosti a další podrobnosti najdete v tématu [spuštění zálohování chráněných položek REST API dokumentu](/rest/api/backup/backups/trigger#request-body).

#### <a name="example-request-body-for-on-demand-backup"></a>Příklad textu žádosti o zálohování na vyžádání

Následující text žádosti definuje vlastnosti vyžadované k aktivaci zálohy chráněné položky. Pokud se uchování nezadá, bude se uchovávat po dobu 30 dnů od aktivace úlohy zálohování.

```json
{
   "properties": {
    "objectType": "IaasVMBackupRequest",
    "recoveryPointExpiryTimeInUTC": "2018-12-01T02:16:20.3156909Z"
  }
}
```

### <a name="responses-for-on-demand-backup"></a>Odpovědi na zálohování na vyžádání

Aktivace zálohování na vyžádání je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a po dokončení této operace 200 (OK).

|Název  |Typ  |Description  |
|---------|---------|---------|
|202 přijato     |         |     Přijato    |

#### <a name="example-responses-for-on-demand-backup"></a>Příklady odpovědí na vyžádání zálohování

Jakmile odešlete požadavek *post* pro zálohování na vyžádání, počáteční odpověď je 202 (přijato) s hlavičkou umístění nebo Azure-Async-Header.

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

Pak Sledujte výslednou operaci pomocí záhlaví umístění nebo hlavičky Azure-AsyncOperation jednoduchým příkazem *Get* .

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/a0866047-6fc7-4ac3-ba38-fb0ae8aa550f?api-version=2019-05-13
```

Po dokončení operace vrátí 200 (OK) ID výsledné úlohy zálohování v těle odpovědi.

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

Vzhledem k tomu, že úloha zálohování je dlouhodobě spuštěná operace, je nutné ji sledovat, jak je vysvětleno v tématu [Monitorování úloh pomocí REST APIho dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="modify-the-backup-configuration-for-a-protected-azure-vm"></a>Úprava konfigurace zálohování chráněného virtuálního počítače Azure

### <a name="changing-the-policy-of-protection"></a>Změna zásad ochrany

Pokud chcete změnit zásadu, se kterou je virtuální počítač chráněný, můžete použít stejný formát jako [Povolení ochrany](#enabling-protection-for-the-azure-vm). V [textu žádosti](#example-request-body) jenom zadejte nové ID zásady a odešlete žádost. Příklad: Chcete-li změnit zásadu testVM z ' DefaultPolicy ' na ' ProdPolicy ', zadejte v textu žádosti ID ' ProdPolicy '.

```json
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupPolicies/ProdPolicy"
  }
}
```

Odpověď bude následovat po stejném formátu, jak je uvedeno [pro povolení ochrany](#responses-to-create-protected-item-operation) .

#### <a name="excluding-disks-during-azure-vm-protection"></a>Vyloučení disků během ochrany virtuálních počítačů Azure

Pokud je virtuální počítač Azure již zálohovaný, můžete změnit zásadu ochrany a zadat seznam disků, které mají být zálohovány nebo vyloučeny. Stačí jenom připravit žádost ve stejném formátu jako při [povolování ochrany s výjimkou disků](#excluding-disks-in-azure-vm-backup) .

> [!IMPORTANT]
> Výše uvedený text žádosti je vždycky poslední kopie datových disků, které se mají vyloučit nebo zahrnout. Nepřidá  se do předchozí konfigurace. Příklad: Pokud nejprve aktualizujete ochranu jako "vyloučit datový disk 1" a pak se znovu pokusíte "vyloučit datový disk 2", v následujících zálohách se vyloučí *jenom datový disk 2* , který obsahuje datový disk 1. Toto je vždy konečný seznam, který bude zahrnut nebo vyloučen v následujících zálohách.

Pokud chcete získat aktuální seznam disků, které jsou vyloučené nebo zahrnuté, Získejte informace o chráněných položkách, jak je uvedeno [zde](/rest/api/backup/protecteditems/get). Odpověď poskytne seznam logických jednotek dat a označuje, zda jsou zahrnuty nebo vyloučeny.

### <a name="stop-protection-but-retain-existing-data"></a>Zastavit ochranu, ale zachovat existující data

Pokud chcete odebrat ochranu na chráněném virtuálním počítači, ale zachovat již zálohovaná data, odeberte zásadu v textu požadavku a odešlete žádost. Jakmile se přidružení k zásadám odebere, zálohy se už nespouštějí a nevytvoří se žádné nové body obnovení.

```http
{
  "properties": {
    "protectedItemType": "Microsoft.Compute/virtualMachines",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "policyId": ""
  }
}
```

Odpověď bude následovat po stejném formátu, jak je uvedeno [pro aktivaci zálohování na vyžádání](#example-responses-for-on-demand-backup). Výsledná úloha by měla být sledována, jak je vysvětleno v [úlohách monitorování pomocí REST API dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

### <a name="stop-protection-and-delete-data"></a>Zastavení ochrany a odstranění dat

Chcete-li odebrat ochranu na chráněném virtuálním počítači a odstranit také data záloh, proveďte operaci odstranění, jak je [zde](/rest/api/backup/protecteditems/delete)popsáno.

Zastavení ochrany a odstranění dat je operace *odstranění* .

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

`{containerName}`A `{protectedItemName}` jsou sestaveny [výše](#responses-to-get-operation). `{fabricName}` je "Azure". V našem příkladu se to týká:

```http
DELETE https://management.azure.com//Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;iaasvmcontainerv2;testRG;testVM?api-version=2019-05-13
```

#### <a name="responses-for-delete-protection"></a>Odpovědi na odstranění ochrany

*Odstranění* ochrany je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a až 204 (obsah) po dokončení této operace.

|Název  |Typ  |Description  |
|---------|---------|---------|
|204. obsah     |         |  Obsah       |
|202 přijato     |         |     Přijato    |

> [!IMPORTANT]
> Aby byla zajištěna ochrana před náhodnými scénáři odstranění, je [k dispozici funkce obnovitelného odstranění](use-restapi-update-vault-properties.md#soft-delete-state) pro Recovery Services trezor. Pokud je stav obnovitelného odstranění trezoru nastavený na povoleno, operace odstranění data okamžitě neodstraní. Bude se uchovávat 14 dní a pak se trvale vyprázdní. Za tento 14 dní se vám neúčtují žádné poplatky za úložiště. Chcete-li operaci odstranění vrátit zpět, přečtěte si [část věnované vrácení zpět a odstranění](#undo-the-deletion).

### <a name="undo-the-deletion"></a>Zrušit odstranění

Zrušení nechtěného odstranění je podobné jako vytvoření zálohované položky. Po odstranění se položka zachová, ale nespustí se žádné budoucí zálohy.

Zrušení odstranění je operace *Put* , která je velmi podobná [změně zásady](#changing-the-policy-of-protection) nebo [Povolení ochrany](#enabling-protection-for-the-azure-vm). Stačí poskytnout záměr vrátit odstranění pomocí proměnné *isRehydrate*  v [textu požadavku](#example-request-body) a odeslat žádost. Například: Chcete-li zrušit odstranění pro testVM, je třeba použít následující text žádosti.

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

Odpověď bude následovat po stejném formátu, jak je uvedeno [pro aktivaci zálohování na vyžádání](#example-responses-for-on-demand-backup). Výsledná úloha by měla být sledována, jak je vysvětleno v [úlohách monitorování pomocí REST API dokumentu](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Další kroky

[Obnovte data ze zálohy virtuálního počítače Azure](backup-azure-arm-userestapi-restoreazurevms.md).

Další informace o rozhraních REST API Azure Backup najdete v následujících dokumentech:

- [Poskytovatel Azure Recovery Services REST API](/rest/api/recoveryservices/)
- [Začínáme s Azure REST API](/rest/api/azure/)