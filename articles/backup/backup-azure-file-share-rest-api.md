---
title: Zálohování sdílených složek Azure pomocí rozhraní REST API
description: Přečtěte si, jak pomocí rozhraní REST API zálohovat sdílené složky Azure v trezoru služby Recovery Services
ms.topic: conceptual
ms.date: 02/16/2020
ms.openlocfilehash: 2cf385830ec1be17cb62432e6ef9cba7d82a9db1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248096"
---
# <a name="backup-azure-file-share-using-azure-backup-via-rest-api"></a>Zálohování sdílené složky Azure pomocí azure backupu přes rozhraní Rest API

Tento článek popisuje, jak zálohovat sdílenou složku Azure pomocí Azure Backup přes rozhraní REST API.

Tento článek předpokládá, že jste již vytvořili trezor služeb pro obnovení a zásady pro konfiguraci zálohování pro sdílenou složku. Pokud jste tak neučinili, podívejte se do [úložiště vytvoření](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatevault) a [vytvořte](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy) kurzy rozhraní REST API zásad pro vytváření nových trezorů a zásad.

V tomto článku použijeme následující zdroje:

- **RecoveryServicesVault**: *azurefilesvault*

- **Zásady:** *plán1*

- **Skupina prostředků**: *azurefiles*

- **Účet úložiště**: *testvault2*

- **Sdílení souborů:** *testshare*

## <a name="configure-backup-for-an-unprotected-azure-file-share-using-rest-api"></a>Konfigurace zálohování pro nechráněnou sdílenou složku Azure pomocí rozhraní REST API

### <a name="discover-storage-accounts-with-unprotected-azure-file-shares"></a>Zjišťování účtů úložiště s nechráněnými sdílenými složkami Azure

Trezor potřebuje zjistit všechny účty úložiště Azure v předplatném se sdílenými složkami, které lze zálohovat do trezoru služby Recovery Services. Tato funkce se aktivuje pomocí [operace aktualizace](https://docs.microsoft.com/rest/api/backup/protectioncontainers/refresh). Jedná se o asynchronní operaci *POST,* která zajišťuje, že úložiště získá nejnovější seznam všech nechráněných sdílených složek Azure v aktuálním předplatném a "ukládá" je. Jakmile je sdílená složka "uložena v mezipaměti", mohou služby pro obnovení přistupovat ke sdílené složce a chránit ji.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupname}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/refreshContainers?api-version=2016-12-01&$filter={$filter}
```

Identifikátor URI `{subscriptionId}`post `{vaultName}` `{vaultresourceGroupName}`má `{fabricName}` , , a parametry. V našem příkladu by hodnota pro různé parametry byla následující:

- `{fabricName}`je *Azure*

- `{vaultName}`je *azurefilesvault*

- `{vaultresourceGroupName}`je *azurefiles*

- $filter=backupManagementType eq 'AzureStorage'

Vzhledem k tomu, že všechny požadované parametry jsou uvedeny v identifikátoru URI, není potřeba samostatné tělo požadavku.

```http
POST https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/refreshContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

#### <a name="responses"></a>Odezvy

Operace "aktualizace" je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a 200 (OK) po dokončení této operace.

##### <a name="example-responses"></a>Příklady odpovědí

Po odeslání požadavku *POST* je vrácena odpověď 202 (Přijato).

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

Sledování výsledné operace pomocí hlavičky "Umístění" pomocí jednoduchého příkazu *GET*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/operationResults/cca47745-12d2-42f9-b3a4-75335f18fdf6?api-version=2016-12-01
```

Po zjištění všech účtů azure storage příkaz GET vrátí odpověď 200 (žádný obsah). Trezor je nyní schopen zjistit jakýkoli účet úložiště se sdílenými složkami, které lze zálohovat v rámci předplatného.

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

### <a name="get-list-of-storage-accounts-that-can-be-protected-with-recovery-services-vault"></a>Získat seznam účtů úložiště, které lze chránit pomocí trezoru služby Recovery Services

Chcete-li potvrdit, že "ukládání do mezipaměti" je hotovo, seznam všech chránitelných účtů úložiště v rámci předplatného. Pak vyhledejte požadovaný účet úložiště v odpovědi. To se provádí pomocí [get protectablecontainers](https://docs.microsoft.com/rest/api/backup/protectablecontainers/list) operace.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectableContainers?api-version=2016-12-01&$filter=backupManagementType eq 'AzureStorage'
```

*IDENTIFIKÁTOR URI GET* má všechny požadované parametry. Není potřeba žádné další tělo požadavku.

Příklad těla odezvy:

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

Vzhledem k tomu, že můžeme najít *testvault2* účet úložiště v těle odpovědi s popisným názvem, operace aktualizace provedené výše byla úspěšná. Úložiště služeb pro obnovení nyní může úspěšně zjišťovat účty úložiště s nechráněnými sdílenými složkami souborů ve stejném předplatném.

### <a name="register-storage-account-with-recovery-services-vault"></a>Registrace účtu úložiště pomocí trezoru služby Recovery Services

Tento krok je potřeba pouze v případě, že jste účet úložiště nezaregistrovali dříve v trezoru. Trezor můžete zaregistrovat pomocí [operace ProtectionContainers-Register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register).

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}?api-version=2016-12-01
```

Proměnné pro identifikátor URI nastavte takto:

- {resourceGroupName} - *azurefiles*
- {fabricName} - *Azure*
- {vaultName} - *azurefilesvault*
- {containerName} - Toto je atribut name v těle odpovědi operace GET ProtectableContainers.
   V našem příkladu je *StorageContainer; Skladování; AzureFiles;testvault2*

>[!NOTE]
> Vždy vezměte atribut name odpovědi a vyplňte jej v tomto požadavku. Nepoužívejte pevný kód nebo vytvořte formát názvu kontejneru. Pokud jej vytvoříte nebo pevný kód, volání rozhraní API se nezdaří, pokud se formát názvu kontejneru změní v budoucnu.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2?api-version=2016-12-01
```

Tělo požadavku vytvořit je následující:

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

Úplný seznam definic těla požadavku a další podrobnosti naleznete [v protectioncontainers-register](https://docs.microsoft.com/rest/api/backup/protectioncontainers/register#azurestoragecontainer).

Toto je asynchronní operace a vrátí dvě odpovědi: "202 Přijato", když je operace přijata a "200 OK" po dokončení operace.  Chcete-li sledovat stav operace, použijte záhlaví umístění k získání nejnovějšího stavu operace.

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/operationresults/1a3c8ee7-e0e5-43ed-b8b3-73cc992b6db9?api-version=2016-12-01
```

Příklad těla odezvy po dokončení operace:

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

Můžete ověřit, zda byla registrace úspěšná z hodnoty parametru *status registrace* v těle odpovědi. V našem případě se zobrazí stav jako registrovaný pro *testvault2*, takže registrace operace byla úspěšná.

### <a name="inquire-all-unprotected-files-shares-under-a-storage-account"></a>Dotázat se na všechny nechráněné sdílené složky pod účtem úložiště

Můžete se zeptat na chránitelné položky v účtu úložiště pomocí operace [Ochrana kontejnery-Inquire.](https://docs.microsoft.com/rest/api/backup/protectioncontainers/inquire) Je to asynchronní operace a výsledky by měly být sledovány pomocí hlavičky umístění.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/inquire?api-version=2016-12-01
```

Nastavte proměnné pro výše uvedený identifikátor URI následujícím způsobem:

- {vaultName} - *azurefilesvault*
- {fabricName} - *Azure*
- {containerName}- Viz atribut name v těle odpovědi operace GET ProtectableContainers. V našem příkladu je *StorageContainer; Skladování; AzureFiles;testvault2*

```http
https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/inquire?api-version=2016-12-01
```

Jakmile je požadavek úspěšný, vrátí stavový kód "OK"

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

Můžete vypsat všechny chránitelné položky v rámci předplatného a vyhledejte požadovanou sdílenou složku, která má být zálohována pomocí operace [GET backupprotectableItems.](https://docs.microsoft.com/rest/api/backup/backupprotectableitems/list)

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupProtectableItems?api-version=2016-12-01&$filter={$filter}
```

Vytvořte identifikátor URI následujícím způsobem:

- {vaultName} - *azurefilesvault*
- {$filter} - *backupManagementType eq 'AzureStorage'*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupProtectableItems?$filter=backupManagementType eq 'AzureStorage'&api-version=2016-12-01
```

Odpověď vzorku:

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

Odpověď obsahuje seznam všech nechráněných sdílených složek souborů a obsahuje všechny informace, které služba Azure Recovery Service potřebuje ke konfiguraci zálohy.

### <a name="enable-backup-for-the-file-share"></a>Povolení zálohování pro sdílenou složku

Po příslušné sdílené složky je "identifikován" s popisným názvem, vyberte zásady chránit. Další informace o existujících zásadách v úložišti naleznete v [seznamu rozhraní POLICY API](https://docs.microsoft.com/rest/api/backup/backuppolicies/list). Potom vyberte [příslušnou zásadu](https://docs.microsoft.com/rest/api/backup/protectionpolicies/get) odkazem na název zásady. Chcete-li vytvořit zásady, naleznete [v kurzu k vytvoření zásad](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-createorupdatepolicy).

Povolení ochrany je asynchronní *PUT* operace, která vytvoří "chráněnou položku".

```http
PUT https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{vaultresourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Nastavte **proměnné název_kontejneru** a **protecteditemname** pomocí atributu ID v těle odezvy operace GET backupprotectableitems.

V našem příkladu je ID sdílené složky, které chceme chránit,:

```output
"/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;testvault2/protectableItems/azurefileshare;testshare
```

- {containername} - *kontejner úložiště;úložiště;azurefiles;testvault2*
- {protectedItemName} - *azurefileshare;testshare*

Nebo můžete odkazovat na atribut **názvu** kontejneru ochrany a chránitelné odpovědi na položky.

>[!NOTE]
>Vždy vezměte atribut name odpovědi a vyplňte jej v tomto požadavku. NEPOUŽÍVEJTE pevný kód nebo vytvořte formát názvu kontejneru nebo formát názvu chráněné položky. Pokud jej vytvoříte nebo pevně zakódujete, volání rozhraní API se nezdaří, pokud se formát názvu kontejneru nebo název chráněné položky v budoucnu změní.

<br>

```http
PUT https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

Vytvoření těla požadavku:

Následující tělo požadavku definuje vlastnosti potřebné k vytvoření chráněné položky.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule1"
  }
}
```

**SourceResourceId** je **parentcontainerFabricID** v reakci na GET backupprotectableItems.

Ukázková odpověď

Vytvoření chráněné položky je asynchronní operace, která vytvoří další operaci, která je třeba sledovat. Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a 200 (OK) po dokončení této operace.

Po odeslání požadavku *PUT* pro vytvoření nebo aktualizaci chráněné položky je počáteční odpověď 202 (Přijato) s hlavičkou umístění.

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

Potom sledujte výslednou operaci pomocí hlavičky umístění nebo hlavičky Azure-AsyncOperation pomocí příkazu *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/c3a52d1d-0853-4211-8141-477c65740264?api-version=2016-12-01
```

Po dokončení operace vrátí 200 (OK) s chráněným obsahem položky v těle odpovědi.

Tělo odpovědi vzorku:

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

Tím se potvrdí, že je povolena ochrana pro sdílenou složku a první záloha se spustí podle plánu zásad.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Aktivace zálohy na vyžádání pro sdílenou složku

Jakmile je sdílená složka Azure nakonfigurovaná pro zálohování, zálohy se spustí podle plánu zásad. Můžete počkat na první naplánované zálohování nebo spustit zálohování na vyžádání kdykoli.

Spuštění zálohy na vyžádání je operace POST.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/backup?api-version=2016-12-01
```

{containerName} a {protectedItemName} jsou vytvořeny výše při povoluje zálohování. V našem příkladu se to promítá do:

```http
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/backup?api-version=2017-07-01
```

### <a name="create-request-body"></a>Vytvořit tělo požadavku

Chcete-li spustit zálohování na vyžádání, jsou následující součásti těla požadavku.

| Name (Název)       | Typ                       | Popis                       |
| ---------- | -------------------------- | --------------------------------- |
| Vlastnosti | AzurefilesharebackupReques | Vlastnosti BackupRequestResource |

Úplný seznam definic těla požadavku a další podrobnosti naleznete v [části Aktivace zálohování chráněných položek dokumentu rozhraní REST API](https://docs.microsoft.com/rest/api/backup/backups/trigger#request-body).

Příklad tělo požadavku

```json
{

  "properties":{

   "objectType":"AzureFileShareBackupRequest",
    "recoveryPointExpiryTimeInUTC":"2020-03-07T18:29:59.000Z"
}

}
```

### <a name="responses"></a>Odezvy

Spuštění zálohy na vyžádání je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). To znamená, že tato operace vytvoří další operaci, která je třeba sledovat samostatně.

Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a 200 (OK) po dokončení této operace.

### <a name="example-responses"></a>Příklady odpovědí

Po odeslání požadavku *POST* pro zálohování na vyžádání, počáteční odpověď je 202 (Přijato) s hlavičkou umístění nebo Azure-async-header.

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

Potom sledujte výslednou operaci pomocí hlavičky umístění nebo hlavičky Azure-AsyncOperation pomocí příkazu *GET.*

```http
GET https://management.azure.com/Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/dc62d524-427a-4093-968d-e951c0a0726e?api-version=2016-12-01
```

Po dokončení operace vrátí 200 (OK) s ID výsledné zálohovací úlohy v těle odpovědi.

#### <a name="sample-response-body"></a>Tělo odezvy vzorku

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

Vzhledem k tomu, že úloha zálohování je dlouhotrvající operace, je třeba ji sledovat, jak je vysvětleno v [úlohách monitorování pomocí dokumentu rozhraní REST API](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job).

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [obnovit sdílené složky Azure pomocí rozhraní Rest API](restore-azure-file-share-rest-api.md).
