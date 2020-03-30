---
title: Obnovení sdílených složek Azure pomocí rozhraní REST API
description: Zjistěte, jak pomocí rozhraní REST API obnovit sdílené složky Azure nebo konkrétní soubory z bodu obnovení vytvořeného službou Azure Backup
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 1c3160491ef92c62745af1468556e7d5c30437fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252503"
---
# <a name="restore-azure-file-shares-using-rest-api"></a>Obnovení sdílených složek Azure pomocí rozhraní REST API

Tento článek vysvětluje, jak obnovit celou sdílenou složku nebo konkrétní soubory z bodu obnovení vytvořeného [službou Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) pomocí rozhraní REST API.

Na konci tohoto článku se dozvíte, jak provádět následující operace pomocí rozhraní REST API:

* Zobrazte body obnovení pro zálohovanou sdílenou složku Azure.
* Obnovte úplnou sdílenou složku Azure.
* Obnovte jednotlivé soubory nebo složky.

## <a name="prerequisites"></a>Požadavky

Předpokládáme, že již máte zálohovanou sdílenou složku, kterou chcete obnovit. Pokud tak nechcete, zkontrolujte [zálohování sdílené složky Azure pomocí rozhraní REST API](backup-azure-file-share-rest-api.md) se dozvíte, jak ji vytvořit.

V tomto článku použijeme následující zdroje:

* **RecoveryServicesVault**: *azurefilesvault*
* **Skupina prostředků**: *azurefiles*
* **Účet úložiště**: *afsaccount*
* **Sdílení souborů**: *azurefiles*

## <a name="fetch-containername-and-protecteditemname"></a>Fetch ContainerName a ProtectedItemName

Pro většinu volání rozhraní API souvisejících s obnovením je třeba předat hodnoty parametrů IDENTIFIKÁTORU URI {containerName} a {protectedItemName}. Pomocí atributu ID v těle odezvy operace [GET backupprotectableitems](https://docs.microsoft.com/rest/api/backup/protecteditems/get) načtěte hodnoty pro tyto parametry. V našem příkladu je ID sdílené složky, kterou chceme chránit,:

`"/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/storagecontainer;storage;azurefiles;afsaccount/protectableItems/azurefileshare;azurefiles`

Takže hodnoty přeložit takto:

* {containername} - *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName} - *azurefileshare;azurefiles*

## <a name="fetch-recovery-points-for-backed-up-azure-file-share"></a>Načtení bodů obnovení pro zálohovanou sdílenou složku Azure

Chcete-li obnovit zálohovanou sdílenou složku nebo soubory, nejprve vyberte bod obnovení, který chcete provést operaci obnovení. Dostupné body obnovení zálohované položky mohou být uvedeny pomocí volání rozhraní REST API [seznamu bodů obnovení.](https://docs.microsoft.com/rest/api/site-recovery/recoverypoints/listbyreplicationprotecteditems) Je to operace GET se všemi příslušnými hodnotami.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2019-05-13&$filter={$filter}
```

Hodnoty URI nastavte takto:

* {fabricName}: *Azure*
* {vaultName}: *azurefilesvault*
* {containername}: *storagecontainer;storage;azurefiles;afsaccount*
* {protectedItemName}: *azurefileshare;azurefiles*
* {ResourceGroupName}: *azurefiles*

IDENTIFIKÁTOR URI GET má všechny požadované parametry. Není třeba dalšího těla žádostí.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints?api-version=2019-05-13
```

### <a name="example-response"></a>Příklad odpovědi

Po odeslání identifikátoru URI get je vrácena odpověď 200:

```http
HTTP/1.1" 200 None
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-client-request-id': '4edb5a58-47ea-11ea-a27a-0a580af41908, 4edb5a58-47ea-11ea-a27a-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': 'd68d7951-7d97-4c49-9a2d-7fbaab55233a'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T073708Z:d68d7951-7d97-4c49-9a2d-7fbaab55233a'
'Date': 'Wed, 05 Feb 2020 07:37:08 GMT'
{
“value”:[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932881138555802864",
    "location": null,
    "name": "932881138555802864",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-04T08:01:35.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-04T08:01:35+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932878582606969225",
    "location": null,
    "name": "932878582606969225",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-03T08:05:30.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-03T08:05:30+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/recoveryPoints/932890167574511261",
    "location": null,
    "name": "932890167574511261",
    "properties": {
      "fileShareSnapshotUri": "https://afsaccount.file.core.windows.net/azurefiles?sharesnapshot=2020-02-02T08:03:50.0000000Z",
      "objectType": "AzureFileShareRecoveryPoint",
      "recoveryPointSizeInGb": 1,
      "recoveryPointTime": "2020-02-02T08:03:50+00:00",
      "recoveryPointType": "FileSystemConsistent"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints"
  },
```

Bod obnovení je identifikován polem {name} ve výše uvedené odpovědi.

## <a name="full-share-recovery-using-rest-api"></a>Úplné obnovení sdílení pomocí rozhraní REST API

Tuto možnost obnovení použijte k obnovení úplné sdílené složky v původním nebo alternativním umístění.
Aktivace obnovení je požadavek POST a tuto operaci můžete provést pomocí rozhraní REST API [pro obnovení aktivační události.](https://docs.microsoft.com/rest/api/backup/restores/trigger)

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Hodnoty {containerName} a {protectedItemName} jsou [zde nastaveny](#fetch-containername-and-protecteditemname) a pole RecoveryPointID je pole {name} výše uvedeného bodu obnovení.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Vytvořit tělo požadavku

Chcete-li aktivovat obnovení sdílené složky Azure, jsou následující součásti těla požadavku:

Name (Název) |  Typ   |   Popis
--- | ---- | ----
Vlastnosti | Požadavek AzureFileShareRestoreRequest | Vlastnosti RestoreRequestResource

Úplný seznam definic těla požadavku a další podrobnosti naleznete v [dokumentu rozhraní REST API aktivační události .](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)

### <a name="restore-to-original-location"></a>Obnovit původní umístění

#### <a name="request-body-example"></a>Příklad tělo požadavku

Následující tělo požadavku definuje vlastnosti potřebné k aktivaci obnovení sdílené složky Azure:

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"OriginalLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore"
}
}
```

### <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

Zadejte následující parametry pro obnovení alternativního umístění:

* **targetResourceId**: Účet úložiště, do kterého je obnoven zálohovaný obsah. Cílový účet úložiště musí být ve stejném umístění jako trezor.
* **Název**: Sdílená složka v rámci cílového účtu úložiště, do kterého se obnoví zálohovaný obsah.
* **targetFolderPath**: Složka pod sdílenou složkou souboru, do které jsou data obnovena.

#### <a name="request-body-example"></a>Příklad tělo požadavku

Následující tělo požadavku obnoví sdílenou složku *azurefiles* v účtu *úložiště afsaccount* do sdílené složky *azurefiles1* v účtu úložiště *afaccount1.*

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"FullShareRestore",
      "restoreFileSpecs":[
         {
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

### <a name="response"></a>Odpověď

Spuštění operace obnovení je [asynchronní operace](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Tato operace vytvoří další operaci, která je třeba sledovat samostatně.
Vrátí dvě odpovědi: 202 (Přijato) při vytvoření jiné operace a 200 (OK) po dokončení této operace.

#### <a name="response-example"></a>Příklad odpovědi

Po odeslání *identifikátoru URI POST* pro aktivaci obnovení je počáteční odpověď 202 (Přijato) s hlavičkou umístění nebo hlavičkou Azure-async-header.

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationResults/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'Retry-After': '60'
'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare;azurefiles/operationsStatus/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1198'
'x-ms-correlation-request-id': '2426777d-c5ec-44b6-a324-384f8947460c'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074347Z:2426777d-c5ec-44b6-a324-384f8947460c'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
```

Potom sledujte výslednou operaci pomocí hlavičky umístění nebo hlavičky Azure-AsyncOperation pomocí příkazu GET.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupOperations/68ccfbc1-a64f-4b29-b955-314b5790cfa9?api-version=2016-12-01
```

Po dokončení operace vrátí 200 (OK) s ID výsledné úlohy obnovení v těle odpovědi.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-client-request-id': '3c743096-47eb-11ea-ae90-0a580af41908, 3c743096-47eb-11ea-ae90-0a580af41908'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'Server': 'Microsoft-IIS/10.0'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-reads': '11998'
'x-ms-correlation-request-id': '41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'x-ms-routing-request-id': 'WESTEUROPE:20200205T074348Z:41ee89b2-3be4-40d8-8ff6-f5592c2571e3'
'Date': 'Wed, 05 Feb 2020 07:43:47 GMT'
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "location": null,
  "name": "a7e97e42-4e54-4d4b-b449-26fcf946f42c",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "3c743096-47eb-11ea-ae90-0a580af41908",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:01.863098",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {},
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T07:43:47.144961+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "Storage"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Pro obnovení alternativní umístění, tělo odezvy bude takto:

```http
{
  "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "location": null,
  "name": "7e0ee41e-6e31-4728-a25c-98ff6b777641",
  "properties": {
    "actionsInfo": [
      "Cancellable"
    ],
    "activityId": "6077be6e-483a-11ea-a915-0a580af4ad72",
    "backupManagementType": "AzureStorage",
    "duration": "0:00:02.171965",
    "endTime": null,
    "entityFriendlyName": "azurefiles",
    "errorDetails": null,
    "extendedInfo": {
      "dynamicErrorMessage": null,
      "propertyBag": {
        "Data Transferred (in MB)": "0",
        "Job Type": "Recover to an alternate file share",
        "Number Of Failed Files": "0",
        "Number Of Restored Files": "0",
        "Number Of Skipped Files": "0",
        "RestoreDestination": "afaccount1/azurefiles1/restoredata",
        "Source File Share Name": "azurefiles",
        "Source Storage Account Name": "afsaccount",
        "Target File Share Name": "azurefiles1",
        "Target Storage Account Name": "afaccount1"
      },
      "tasksList": []
    },
    "jobType": "AzureStorageJob",
    "operation": "Restore",
    "startTime": "2020-02-05T17:10:18.106532+00:00",
    "status": "InProgress",
    "storageAccountName": "afsaccount",
    "storageAccountVersion": "ClassicCompute"
  },
  "resourceGroup": "azurefiles",
  "tags": null,
  "type": "Microsoft.RecoveryServices/vaults/backupJobs"
}
```

Vzhledem k tomu, že úloha zálohování je dlouhotrvající operace, měla by být sledována, jak je vysvětleno v [úlohách monitorování pomocí dokumentu rozhraní REST API](https://docs.microsoft.com/azure/backup/backup-azure-arm-userestapi-managejobs#tracking-the-job).

## <a name="item-level-recovery-using-rest-api"></a>Obnovení na úrovni položky pomocí rozhraní REST API

Tuto možnost obnovení můžete použít k obnovení jednotlivých souborů nebo složek v původním nebo alternativním umístění.

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2019-05-13
```

Hodnoty {containerName} a {protectedItemName} jsou [zde nastaveny](#fetch-containername-and-protecteditemname) a pole RecoveryPointID je pole {name} výše uvedeného bodu obnovení.

```http
POST https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;afsaccount/protectedItems/AzureFileShare%3Bazurefiles/recoveryPoints/932886657837421071/restore?api-version=2019-05-13'
```

### <a name="create-request-body"></a>Vytvořit tělo požadavku

Chcete-li aktivovat obnovení sdílené složky Azure, jsou následující součásti těla požadavku:

Name (Název) |  Typ   |   Popis
--- | ---- | ----
Vlastnosti | Požadavek AzureFileShareRestoreRequest | Vlastnosti RestoreRequestResource

Úplný seznam definic těla požadavku a další podrobnosti naleznete v [dokumentu rozhraní REST API aktivační události .](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body)

### <a name="restore-to-original-location"></a>Obnovit původní umístění

Následující tělo požadavku je obnovit soubor *Restoretest.txt* ve sdílené složce *azurefiles* v účtu úložiště *afsaccount.*

Vytvořit tělo požadavku

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "copyOptions":"Overwrite",
      "recoveryType":"OriginalLocation",
      "restoreFileSpecs":[
         {
            "fileSpecType":"File",
            "path":"RestoreTest.txt",
            "targetFolderPath":null
}
],
      "restoreRequestType":"ItemLevelRestore",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.storage/storageAccounts/afsaccount",
      "targetDetails":null
}
}
```

### <a name="restore-to-alternate-location"></a>Obnovit do alternativního umístění

Následující tělo požadavku je obnovit soubor *Restoretest.txt* v *azurefiles* sdílené složky v účtu *úložiště afsaccount* do složky *restoredata* sdílené složky *azurefiles1* v účtu úložiště *afaccount1.*

Vytvořit tělo požadavku

```json
{
   "properties":{
      "objectType":"AzureFileShareRestoreRequest",
      "recoveryType":"AlternateLocation",
      "sourceResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afsaccount",
      "copyOptions":"Overwrite",
      "restoreRequestType":"ItemLevelRestore",
      "restoreFileSpecs":[
         {
            "path":"Restore/RestoreTest.txt",
            "fileSpecType":"File",
            "targetFolderPath":"restoredata"
}
],
      "targetDetails":{
         "name":"azurefiles1",
         "targetResourceId":"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/afaccount1"
}
}
}
```

Odpověď by měla být zpracována stejným způsobem, jak je vysvětleno výše pro [úplné obnovení podílu](#full-share-recovery-using-rest-api).

## <a name="next-steps"></a>Další kroky

* Zjistěte, jak [spravovat zálohování sdílených složek Azure pomocí rozhraní Rest API](manage-azure-file-share-rest-api.md).
