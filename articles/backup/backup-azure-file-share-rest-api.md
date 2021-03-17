---
title: Zálohování sdílených složek Azure pomocí REST API
description: Naučte se používat REST API k zálohování sdílených složek Azure v trezoru Recovery Services.
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 8d2d8ed88da133986540a293185c8e37000ab87b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88824861"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Zálohování sdílené složky Azure pomocí Azure Backup přes REST API

Tento článek popisuje, jak zálohovat sdílenou složku Azure pomocí Azure Backup přes REST API.

V tomto článku se předpokládá, že jste už vytvořili Recovery Services trezor a zásady pro konfiguraci zálohování sdílené složky. Pokud jste to ještě neudělali, přečtěte si téma [Vytvoření trezoru](./backup-azure-arm-userestapi-createorupdatevault.md) a [Vytvoření zásad](./backup-azure-arm-userestapi-createorupdatepolicy.md) REST APIch výukových kurzů pro vytváření nových trezorů a zásad.

V tomto článku budeme používat následující zdroje:

- **RecoveryServicesVault**: *azurefilesvault*

- **Zásada:** *schedule1*

- **Skupina prostředků**: *azurefiles*

- **Účet úložiště**: *testvault2*

- **Sdílená složka**: *TestShare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Konfigurace zálohování pro nechráněnou sdílenou složku Azure pomocí REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Zjištění účtů úložiště s nechráněnými sdílenými složkami Azure

Trezor musí v předplatném zjistit všechny účty úložiště Azure, které se dají zálohovat do trezoru Recovery Services. Tato operace se aktivuje pomocí [operace aktualizace](/rest/api/backup/protectioncontainers/refresh). Je to asynchronní operace *post* , která zajišťuje, že trezor získá nejnovější seznam všech nechráněných sdílených složek Azure v aktuálním předplatném a ukládá je do mezipaměti. Jakmile je sdílená složka uložená v mezipaměti, služba Recovery Services může ke sdílené složce přistupovat a chránit ji.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

Identifikátor URI příspěvku obsahuje `{subscriptionId}` `{vaultName}` parametry,, `{vaultresourceGroupName}` a `{fabricName}` . V našem příkladu bude hodnota různých parametrů následující:

- `{fabricName}` je *Azure*

- `{vaultName}` je *azurefilesvault*

- `{vaultresourceGroupName}` je *azurefiles*

- $filter = backupManagementType EQ ' AzureStorage '

Vzhledem k tomu, že všechny požadované parametry jsou uvedeny v identifikátoru URI, není nutné, aby bylo samostatné tělo požadavku.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses-to-the-refresh-operation"></a>Odpovědi na operaci aktualizace

Operace Refresh je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Při vytvoření jiné operace vrátí dvě odpovědi: 202 (přijato) a po dokončení této operace 200 (OK).

##### <a name="example-responses-to-the-refresh-operation"></a>Příklady odpovědí na operaci aktualizace

Po odeslání žádosti *post* se vrátí odpověď 202 (přijato).

```http
HTTP/1.1 202 Accepted
'Pragma': 'no-cache'
'Expires': '-1'
'Location': ‘https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/ResourceGroups
/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/
cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01’
'Retry-After': '60'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-client-request-id': ‘3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61’
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11996'
'x-ms-correlation-request-id': '6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'x-ms-routing-request-id': CENTRALUSEUAP:20200203T091326Z:6cc12ceb-90a2-430d-a1ec-9b6b6fdea92b'
'Date': 'Mon, 03 Feb 2020 09:13:25 GMT'
```

Pomocí jednoduchého příkazu *Get* Sledujte výslednou operaci pomocí hlavičky Location (umístění).

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Po zjištění všech účtů Azure Storage vrátí příkaz GET odpověď 200 (bez obsahu). Trezor teď dokáže vyhledat libovolný účet úložiště se sdílenými složkami, které se dají zálohovat v rámci předplatného.

```http
HTTP/1.1 200 NoContent
Cache-Control  : no-cache
Pragma   : no-cache
X-Content-Type-Options  : nosniff
x-ms-request-id    : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security  : max-age=31536000; includeSubDomains
X-Powered-By    : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11933
x-ms-correlation-request-id   : d9bdb266-8349-4dbd-9688-de52f07648b2
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105304Z:d9bdb266-8349-4dbd-9688-de52f07648b2
Date   : Mon, 27 Jan 2020 10:53:04 GMT
```

### <a name="get-list-of-storage-accounts-with-file-shares-that-can-be-backed-up-with-recovery-services-vault"></a>Získat seznam účtů úložiště se sdílenými složkami, které se dají zálohovat pomocí služby Recovery Services trezor

Pokud chcete potvrdit, že se ukládá do mezipaměti, uveďte všechny účty úložiště v předplatném se sdílenými složkami, které se dají zálohovat pomocí Recovery Services trezoru. Pak v odpovědi Najděte požadovaný účet úložiště. To se provádí pomocí operace [Get ProtectableContainers](/rest/api/backup/protectablecontainers/list) .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

Identifikátor URI *Get* má všechny požadované parametry. Není potřeba žádný další text žádosti.

Příklad těla odpovědi:

```json
{

  "value": [

    {

      "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers
 /Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/

protectableContainers/StorageContainer;Storage;AzureFiles;testvault2",

      "name": "StorageContainer;Storage;AzureFiles;testvault2",

      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectableContainers",

      "properties": {

        "friendlyName": "testvault2",

        "backupManagementType": "AzureStorage",

        "protectableContainerType": "StorageContainer",

        "healthStatus": "Healthy",

        "containerId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/
 AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2"

      }

    }

  ]

}
```

Vzhledem k tomu, že jsme v těle odpovědi našli *testvault2* účet úložiště s popisným názvem, operace aktualizace provedená výše byla úspěšná. Trezor Recovery Services teď může úspěšně zjistit účty úložiště se sdílenými složkami nechráněných souborů ve stejném předplatném.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registrace účtu úložiště pomocí Recovery Servicesového trezoru

Tento krok je nutný jenom v případě, že jste předtím nezaregistrovali účet úložiště v trezoru. Trezor můžete zaregistrovat prostřednictvím [operace ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Proměnné pro identifikátor URI nastavte následujícím způsobem:

- {resourceGroupName} – *azurefiles*
- {Fabric} – *Azure*
- {Trezor} – *azurefilesvault*
- {Container} – Toto je atribut Name v těle odpovědi operace GET ProtectableContainers.
   V našem příkladu je to *StorageContainer; Pamì AzureFiles; testvault2*

>[!NOTE]
> Vždy přebírat atribut Name odpovědi a vyplnit v této žádosti. Nepoužívejte pevný kód nebo vytvořte formát názvu kontejneru. Pokud vytvoříte nebo pevně zadáte kód, volání rozhraní API se nezdaří, pokud se změní formát názvu kontejneru v budoucnu.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Text žádosti o vytvoření je následující:

```json
{

 "properties": {


  "containerType": "StorageContainer",


  "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",


  "resourceGroup": "AzureFiles",


  "friendlyName": "testvault2",


  "backupManagementType": "AzureStorage"


 }
```

Úplný seznam definic těla žádosti a další podrobnosti najdete v tématu [ProtectionContainers-Register](/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Jedná se o asynchronní operaci a vrátí dvě odpovědi: "202 přijatý" při přijetí operace a "200 OK" po dokončení operace.  Chcete-li sledovat stav operace, použijte hlavičku umístění k získání nejnovějšího stavu operace.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Příklad těla odpovědi po dokončení operace:

```json
{
    "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/
protectionContainers/StorageContainer;Storage;AzureFiles;testvault2",
    "name": "StorageContainer;Storage;AzureFiles;testvault2",
    "properties": {
        "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
        "protectedItemCount": 0,
        "friendlyName": "testvault2",
        "backupManagementType": "AzureStorage",
        "registrationStatus": "Registered",
        "healthStatus": "Healthy",
        "containerType": "StorageContainer",
        "protectableObjectType": "StorageContainer"
    }
}
```

Můžete ověřit, zda byla registrace úspěšná z hodnoty parametru *registrationstatus* v těle odpovědi. V našem případě zobrazuje stav registrovaný pro *testvault2*, takže operace registrace byla úspěšná.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Dotázat se na všechny nechráněné sdílené soubory v rámci účtu úložiště

V účtu úložiště se můžete dotazovat na chránitelné položky pomocí operace [prošetření kontejnerů ochrany](/rest/api/backup/protectioncontainers/inquire) . Je to asynchronní operace a výsledky by se měly sledovat pomocí hlavičky umístění.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Proměnné pro výše uvedený identifikátor URI nastavte následujícím způsobem:

- {Trezor} – *azurefilesvault*
- {Fabric} – *Azure*
- {Container} – odkaz na atribut Name v těle odpovědi operace GET ProtectableContainers V našem příkladu je to *StorageContainer; Pamì AzureFiles; testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Po úspěšném dokončení žádosti vrátí stavový kód "OK".

```http
Cache-Control : no-cache
Pragma   : no-cache
X-Content-Type-Options: nosniff
x-ms-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-client-request-id  : 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security: max-age=31536000; includeSubDomains
Server  : Microsoft-IIS/10.0
X-Powered-B : ASP.NET
x-ms-ratelimit-remaining-subscription-reads: 11932
x-ms-correlation-request-id  : 68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
x-ms-routing-request-id   : CENTRALUSEUAP:20200127T105305Z:68727f1e-b8cf-4bf1-bf92-8e03a9d96c46
Date  : Mon, 27 Jan 2020 10:53:05 GMT
```

### <a name="select-the-file-share-you-want-to-back-up"></a>Vyberte sdílenou složku, kterou chcete zálohovat.

V rámci předplatného můžete zobrazit seznam všech chráněných položek a vyhledat požadovanou sdílenou složku, která se bude zálohovat pomocí operace [získat backupprotectableItems](/rest/api/backup/backupprotectableitems/list) .

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Následujícím způsobem vytvořte identifikátor URI:

- {Trezor} – *azurefilesvault*
- {$filter} – *backupManagementType EQ ' AzureStorage '*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Ukázková odpověď:

```json
Status Code:200

{
    "value": [
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afaccount1/protectableItems/azurefileshare;azurefiles1",
            "name": "azurefileshare;azurefiles1",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1",
                "parentContainerFriendlyName": "afaccount1",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "azurefiles1",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;afsresource",
            "name": "azurefileshare;afsresource",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
                "parentContainerFriendlyName": "afsaccount",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "afsresource",
                "protectionState": "NotProtected"
            }
        },
        {
            "id": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare",
            "name": "azurefileshare;testshare",
            "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectableItems",
            "properties": {
                "parentContainerFabricId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
                "parentContainerFriendlyName": "testvault2",
                "azureFileShareType": "XSMB",
                "backupManagementType": "AzureStorage",
                "workloadType": "AzureFileShare",
                "protectableItemType": "AzureFileShare",
                "friendlyName": "testshare",
                "protectionState": "NotProtected"
            }
        }
    ]
}
```

Odpověď obsahuje seznam všech nechráněných sdílených složek a obsahuje všechny informace, které služba obnovení Azure vyžaduje ke konfiguraci zálohy.

### <a name="enable-backup-for-the-file-share"></a>Povolit zálohování pro sdílenou složku

Až bude příslušná sdílená složka "identifikovaná" s popisným názvem, vyberte zásadu, která se má chránit. Další informace o existujících zásadách v trezoru najdete v tématu věnovaném [rozhraní API zásad seznamu](/rest/api/backup/backuppolicies/list). Pak vyberte [příslušnou zásadu](/rest/api/backup/protectionpolicies/get) odkazem na název zásady. Pokud chcete vytvořit zásady, Projděte si [kurz vytvoření zásad](./backup-azure-arm-userestapi-createorupdatepolicy.md).

Povolení ochrany je asynchronní operace *Put* , která vytvoří chráněnou položku.

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Nastavte proměnné **Container** a **PROTECTEDITEMNAME** pomocí atributu ID v těle odpovědi operace Get backupprotectableitems.

V našem příkladu je ID sdílené složky, kterou chceme chránit:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {Container} – *storagecontainer; Storage; azurefiles; testvault2*
- {protectedItemName} – *azurefileshare; TestShare*

Nebo můžete použít odkaz na atribut **Name** kontejneru ochrany a chránitelné odpovědi na položky.

>[!NOTE]
>Vždy přebírat atribut Name odpovědi a vyplnit v této žádosti. Nepoužívejte pevný kód nebo vytvořte formát názvu kontejneru nebo chráněné položky. Pokud vytvoříte nebo pevně zadáte kód, volání rozhraní API se nezdaří, pokud se v budoucnu změní formát názvu kontejneru nebo chráněné položky.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Vytvořit text žádosti:

Následující text požadavku definuje vlastnosti vyžadované k vytvoření chráněné položky.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**Parametr sourceresourceid** je **parentcontainerFabricID** v reakci na Get backupprotectableItems.

Ukázková odpověď

Vytvoření chráněné položky je asynchronní operace, která vytvoří další operaci, kterou je třeba sledovat. Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a 200 (OK) po dokončení této operace.

Jakmile odešlete žádost o *vložení* pro vytvoření nebo aktualizaci chráněné položky, počáteční odpověď je 202 (přijato) s hlavičkou umístění.

```http
HTTP/1.1 202 Accepted
Cache-Control  : no-cache
Pragma  : no-cache
Location : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
Retry-Afte  : 60
Azure-AsyncOperation  : https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare/operationResults/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
X-Content-Type-Options : nosniff
x-ms-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-client-request-id: 3da383a5-d66d-4b7c-982a-bc8d94798d61,3da383a5-d66d-4b7c-982a-bc8d94798d61
Strict-Transport-Security : max-age=31536000; includeSubDomains
X-Powered-By  : ASP.NET
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id : b55527fa-f473-4f09-b169-9cc3a7a39065
x-ms-routing-request-id  : CENTRALUSEUAP:20200127T105412Z:b55527fa-f473-4f09-b169-9cc3a7a39065
Date : Mon, 27 Jan 2020 10:54:12 GMT
```

Pak Sledujte výslednou operaci pomocí záhlaví umístění nebo hlavičky Azure-AsyncOperation pomocí příkazu  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Po dokončení operace vrátí 200 (OK) k obsahu chráněné položky v těle odpovědi.

Tělo ukázkové odpovědi:

```json
{
    "id": "c3a52d1d-0853-4211-8141-477c65740264",
    "name": "c3a52d1d-0853-4211-8141-477c65740264",
    "status": "Succeeded",
    "startTime": "2020-02-03T18:10:48.296012Z",
    "endTime": "2020-02-03T18:10:48.296012Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b"
    }
}
```

Tím se potvrdí, že je pro sdílenou složku povolená ochrana a první záloha se spustí podle plánu zásad.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Aktivace zálohování na vyžádání pro sdílení souborů

Jakmile je sdílená složka Azure nakonfigurovaná pro zálohování, zálohování se spouští podle plánu zásad. Můžete počkat na první naplánovanou zálohu nebo kdykoli aktivovat zálohování na vyžádání.

Aktivace zálohování na vyžádání je operace POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{ContainerName} a {protectedItemName} jsou během povolování zálohování sestavené výše. V našem příkladu se to týká:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Vytvořit text žádosti

Chcete-li aktivovat zálohování na vyžádání, níže jsou uvedené součásti textu žádosti.

| Název       | Typ                       | Description                       |
| ---------- | -------------------------- | --------------------------------- |
| Vlastnosti | AzurefilesharebackupReques | Vlastnosti BackupRequestResource |

Úplný seznam definic těla žádosti a další podrobnosti najdete v tématu [spuštění zálohování chráněných položek REST API dokumentu](/rest/api/backup/backups/trigger#request-body).

Příklad textu žádosti

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses-to-the-on-demand-backup-operation"></a>Odpovědi na operaci zálohování na vyžádání

Aktivace zálohování na vyžádání je [asynchronní operace](../azure-resource-manager/management/async-operations.md). To znamená, že tato operace vytvoří další operaci, která musí být sledována samostatně.

Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a 200 (OK) po dokončení této operace.

### <a name="example-responses-to-the-on-demand-backup-operation"></a>Příklady odpovědí na operaci zálohování na vyžádání

Jakmile odešlete požadavek *post* pro zálohování na vyžádání, počáteční odpověď je 202 (přijato) s hlavičkou umístění nebo Azure-Async-Header.

```http
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'Retry-After': '60'
'Azure-AsyncOperation': https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2017-07-01
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040339Z:2e03b8d4-66b1-48cf-8094-aa8bff57e8fb'
'Date': 'Thu, 06 Feb 2020 04:03:38 GMT'
'Content-Length': '0'
```

Pak Sledujte výslednou operaci pomocí záhlaví umístění nebo hlavičky Azure-AsyncOperation pomocí příkazu  *Get* .

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Po dokončení operace vrátí 200 (OK) ID výsledné úlohy zálohování v těle odpovědi.

#### <a name="sample-response-body"></a>Ukázka textu odpovědi

```json
{
    "id": "dc62d524-427a-4093-968d-e951c0a0726e",
    "name": "dc62d524-427a-4093-968d-e951c0a0726e",
    "status": "Succeeded",
    "startTime": "2020-02-06T11:06:02.1327954Z",
    "endTime": "2020-02-06T11:06:02.1327954Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "39282261-cb52-43f5-9dd0-ffaf66beeaef"
    }
}
```

Vzhledem k tomu, že úloha zálohování je dlouhodobě spuštěná operace, je nutné ji sledovat, jak je vysvětleno v tématu [Monitorování úloh pomocí REST APIho dokumentu](./backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [obnovit sdílené složky Azure pomocí rozhraní REST API](restore-azure-file-share-rest-api.md).
