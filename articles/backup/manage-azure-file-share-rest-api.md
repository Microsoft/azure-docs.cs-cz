---
title: Správa zálohování sdílených složek Azure pomocí rozhraní REST API
description: Naučte se, jak pomocí REST API spravovat a monitorovat sdílené složky Azure, které jsou zálohované pomocí Azure Backup.
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 5e2823472c6a7bdd6b3f9819db3079d7efa78c4e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88892843"
---
# <a name="manage-azure-file-share-backup-with-rest-api"></a>Správa zálohování sdílených složek Azure pomocí REST API

Tento článek vysvětluje, jak provádět úlohy pro správu a monitorování sdílených složek Azure, které jsou zálohované pomocí [Azure Backup](./backup-overview.md).

## <a name="monitor-jobs"></a>Monitorování úloh

Služba Azure Backup aktivuje úlohy, které běží na pozadí. To zahrnuje scénáře, jako je například aktivace zálohování, operace obnovení a vypnutí zálohování. Tyto úlohy je možné sledovat pomocí jejich ID.

### <a name="fetch-job-information-from-operations"></a>Načíst informace o úloze z operací

Operace, jako je například aktivace zálohování, vrátí v odpovědi identifikátor jobID.

Například konečná odpověď [REST API operace zálohování aktivační události](backup-azure-file-share-rest-api.md#trigger-an-on-demand-backup-for-file-share) je následující:

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

Úloha zálohování sdílené složky Azure je identifikovaná polem **jobId** a lze ji sledovat tak, jak je uvedeno [zde](/rest/api/backup/jobdetails/) , pomocí žádosti o získání.

### <a name="tracking-the-job"></a>Sledování úlohy

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupJobs/{jobName}?api-version=2019-05-13
```

{JobName} je výše uvedená "jobId". Odpověď je vždy "200 OK" s polem **stav** udávajícím stav úlohy. Až bude dokončená nebo CompletedWithWarnings, oddíl **extendedInfo** odhalí další podrobnosti o úloze.

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b?api-version=2019-05-13'
```

#### <a name="response"></a>Odpověď

Název  | Typ  |  Description
--- | --- | ----
200 OK |  JobResource  | OK

#### <a name="response-example"></a>Příklad odpovědi

Po odeslání identifikátoru URI *Get* se vrátí odpověď 200.

```http
HTTP/1.1" 200
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Transfer-Encoding': 'chunked'
'Content-Type': 'application/json'
'Content-Encoding': 'gzip'
'Expires': '-1'
'Vary': 'Accept-Encoding'
'Server': 'Microsoft-IIS/10.0, Microsoft-IIS/10.0'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-client-request-id': 'a644712a-4895-11ea-ba57-0a580af42708, a644712a-4895-11ea-ba57-0a580af42708'
'X-Powered-By': 'ASP.NET'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'x-ms-ratelimit-remaining-subscription-reads': '11999'
'x-ms-correlation-request-id': 'dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T040341Z:dba43f00-5cdb-43b1-a9ec-23e419db67c5'
'Date': 'Thu, 06 Feb 2020 04:03:40 GMT'
{
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "name": "e2ca2cf4-2eb9-4d4b-b16a-8e592d2a658b",
    "type": "Microsoft.RecoveryServices/vaults/backupJobs",
    "properties": {
        "jobType": "AzureStorageJob",
        "duration": "00:00:43.1809140",
        "storageAccountName": "testvault2",
        "storageAccountVersion": "Storage",
        "extendedInfo": {
            "tasksList": [],
            "propertyBag": {
                "File Share Name": "testshare",
                "Storage Account Name": "testvault2",
                "Policy Name": "schedule1"
            }
        },
        "entityFriendlyName": "testshare",
        "backupManagementType": "AzureStorage",
        "operation": "ConfigureBackup",
        "status": "Completed",
        "startTime": "2020-02-03T18:10:48.296012Z",
        "endTime": "2020-02-03T18:11:31.476926Z",
        "activityId": "3677cec0-942d-4eac-921f-8f3c873140d7"
    }
}
```

## <a name="modify-policy"></a>Upravit zásadu

Chcete-li změnit zásadu, se kterou je sdílená složka chráněná, můžete použít stejný formát jako povolení ochrany. Pouze v zásadách žádosti zadejte nové ID zásady a odešlete žádost.

Například: Chcete-li změnit zásady ochrany *TestShare* z *schedule1* na *schedule2*, zadejte ID *schedule2* v textu žádosti.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupPolicies/schedule2"
  }
}
```

## <a name="stop-protection-but-retain-existing-data"></a>Zastavit ochranu, ale zachovat existující data

Ochranu chráněné sdílené složky můžete odebrat, ale zachovejte data, která jsou už zálohovaná. Provedete to tak, že odeberete zásadu v textu požadavku, který jste použili k[Povolení zálohování](backup-azure-file-share-rest-api.md#enable-backup-for-the-file-share) , a odešlete žádost. Po odebrání přidružení se zásadou se už nebudou spouštět zálohy a nevytvoří se žádné nové body obnovení.

```json
{
  "properties": {
    "protectedItemType": "AzureFileShareProtectedItem",
    "sourceResourceId": "/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/AzureFiles/providers/Microsoft.Storage/storageAccounts/testvault2",
    "policyId": “" ,
“protectionState”:”ProtectionStopped”
  }
}
```

### <a name="sample-response"></a>Ukázková odpověď

Zastavení ochrany sdílené složky je asynchronní operace. Operace vytvoří další operaci, kterou je třeba sledovat. Při vytvoření jiné operace vrátí dvě odpovědi: 202 (přijato) a po dokončení této operace 200.

Hlavička odpovědi po úspěšném přijetí operace:

```http
HTTP/1.1" 202
'Cache-Control': 'no-cache'
'Pragma': 'no-cache'
'Expires': '-1'
'Location': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationResults/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'Retry-After': '60'
msrest.http_logger :     'Azure-AsyncOperation': 'https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;azurefiles;testvault2/protectedItems/AzureFileShare;testshare/operationsStatus/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2019-05-13'
'X-Content-Type-Options': 'nosniff'
'x-ms-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-client-request-id': 'd331c15e-48ab-11ea-84c0-0a580af46a50, d331c15e-48ab-11ea-84c0-0a580af46a50'
'Strict-Transport-Security': 'max-age=31536000; includeSubDomains'
'X-Powered-By': 'ASP.NET'
'x-ms-ratelimit-remaining-subscription-writes': '1199'
'x-ms-correlation-request-id': '3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'x-ms-routing-request-id': 'WESTEUROPE:20200206T064224Z:3895e8a1-e4b9-4da5-bec7-2cf0266405f8'
'Date': 'Thu, 06 Feb 2020 06:42:24 GMT'
'Content-Length': '0'
```

Pak Sledujte výslednou operaci pomocí záhlaví umístění nebo hlavičky Azure-AsyncOperation pomocí příkazu GET:

```http
GET https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupoperations/b300922a-ad9c-4181-b4cd-d42ea780ad77?api-version=2016-12-01
```

### <a name="response-body"></a>Text odpovědi

```json
{
    "id": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "name": "b300922a-ad9c-4181-b4cd-d42ea780ad77",
    "status": "Succeeded",
    "startTime": "2020-02-06T06:42:24.4001299Z",
    "endTime": "2020-02-06T06:42:24.4001299Z",
    "properties": {
        "objectType": "OperationStatusJobExtendedInfo",
        "jobId": "7816fca8-d5be-4c41-b911-1bbd922e5826"
    }
}
```

## <a name="stop-protection-and-delete-data"></a>Zastavení ochrany a odstranění dat

Pokud chcete ochranu v chráněné sdílené složce odebrat a odstranit taky tato data, proveďte operaci odstranění, jak je popsáno [zde](/rest/api/backup/protecteditems/delete).

```http
DELETE https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}?api-version=2019-05-13
```

Parametry {Containers} a {protectedItemName} jsou [tady](restore-azure-file-share-rest-api.md#fetch-containername-and-protecteditemname)nastavené.

Následující příklad aktivuje operaci zastavení ochrany pro sdílenou složku *TestShare* chráněnou pomocí *azurefilesvault*.

```http
DELETE https://management.azure.com/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupFabrics/Azure/protectionContainers/StorageContainer;Storage;AzureFiles;testvault2/protectedItems/azurefileshare;testshare?api-version=2016-12-01
```

### <a name="responses"></a>Odpovědi

Odstranění ochrany je asynchronní operace. Operace vytvoří další operaci, která musí být sledována samostatně.
Vrátí dvě odpovědi: 202 (přijato) při vytvoření jiné operace a až 204 (obsah) po dokončení této operace.

## <a name="next-steps"></a>Další kroky

* Naučte se [řešit problémy při konfiguraci zálohování sdílených složek Azure](troubleshoot-azure-files.md).
