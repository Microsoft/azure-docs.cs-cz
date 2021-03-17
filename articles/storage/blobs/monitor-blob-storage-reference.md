---
title: Referenční informace o datech monitorování služby Azure Blob Storage | Microsoft Docs
description: Referenční informace o protokolech a metrikách pro monitorování dat z úložiště objektů BLOB v Azure
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: b37586f66106e33b2a8dad034a6e7c131484be73
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571631"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Referenční informace k datům monitorování úložiště objektů BLOB v Azure

Podrobnosti o shromažďování a analýze dat monitorování pro Azure Storage najdete v tématu [monitorování Azure Storage](monitor-blob-storage.md) .

## <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro Azure Storage. 

### <a name="capacity-metrics"></a>Kapacitní metriky

Hodnoty metrik kapacity se aktualizují každý den (až 24 hodin). Časové rozpětí definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaná časová zrna pro všechny metriky kapacity je jedna hodina (PT1H).

Azure Storage poskytuje v Azure Monitor následující metriky kapacity.

#### <a name="account-level"></a>Úroveň účtu

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>Blob Storage

Tato tabulka zobrazuje [metriky služby Blob Storage](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices).

| Metric | Popis |
| ------------------- | ----------------- |
| BlobCapacity | Celkem úložiště objektů BLOB použité v účtu úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 <br/> Dimensions: **BlobType** a **BlobTier** ([definice](#metrics-dimensions)) |
| BlobCount    | Počet objektů BLOB uložených v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 <br/> Dimensions: **BlobType** a **BlobTier** ([definice](#metrics-dimensions)) |
| BlobProvisionedSize | Velikost úložiště zřízená v účtu úložiště. Tato metrika se vztahuje jenom na účty úložiště úrovně Premium. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr |
| ContainerCount    | Počet kontejnerů v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| IndexCapacity     | Velikost úložiště, kterou používá ADLS Gen2 hierarchický index <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |

### <a name="transaction-metrics"></a>Transakční metriky

Metriky transakcí jsou vydávány při každém požadavku na účet úložiště z Azure Storage do Azure Monitor. V případě, že váš účet úložiště neobsahuje žádné aktivity, nebudou v obdobích metrik transakcí žádná data. Všechny transakční metriky jsou dostupné na úrovni účtu i služby Blob Storage. Časové rozpětí definuje časový interval, po který jsou zobrazeny hodnoty metriky. Podporovaná časová zrna pro všechny transakční metriky jsou PT1H a PT1M.

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Storage podporuje pro metriky v Azure Monitor následující dimenze.

### <a name="dimensions-available-to-all-storage-services"></a>Dostupné dimenze pro všechny služby úložiště

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Dimenze specifické pro úložiště objektů BLOB

| Název dimenze | Description |
| ------------------- | ----------------- |
| **BlobType** | Typ objektu BLOB pouze pro metriky objektů BLOB Podporované hodnoty jsou **BlockBlob**, **PageBlob** a **Azure Data Lake Storage**. Doplňovací objekty blob jsou součástí **BlockBlob**. |
| **BlobTier** | Služba Azure Storage nabízí různé úrovně přístupu, které umožňují ukládat data objektů BLOB nejefektivnějším způsobem. Další informace najdete v [Azure Storage úrovni objektů BLOB](../blobs/storage-blob-storage-tiers.md). Mezi podporované hodnoty patří: <br/> <li>**Hot**: vrstva Hot</li> <li>**Studená**: studená vrstva</li> <li>**Archivace**: archivní vrstva</li> <li>**Premium**: úroveň Premium pro objekt blob bloku</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: typy vrstev pro objekt blob stránky úrovně Premium</li> <li>**Standard**: typ vrstvy pro objekt blob stránky úrovně Standard</li> <li>**Nevrstvený**: typ vrstvy pro účet úložiště pro obecné účely v1</li> |

Pro metriky podporující dimenze je nutné zadat hodnotu dimenze, aby se zobrazily odpovídající hodnoty metrik. Například pokud se podíváte na hodnotu  **transakce** pro úspěšné odpovědi, je nutné filtrovat dimenzi **ResponseType** s **úspěchem**. Pokud se podíváte na **BlobCount** hodnotu pro objekt blob bloku, musíte filtrovat dimenzi **BlobType** pomocí **BlockBlob**.

## <a name="resource-logs-preview"></a>Protokoly prostředků (Preview)

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek a účtů Premium Storage v účtech úložiště pro obecné účely V1 a obecné účely v2. Klasické účty úložiště se nepodporují.

V následující tabulce jsou uvedeny vlastnosti Azure Storagech protokolů prostředků při jejich shromažďování v protokolech Azure Monitor nebo Azure Storage. Vlastnosti popisují operaci, službu a typ autorizace, které byly použity k provedení operace.

### <a name="fields-that-describe-the-operation"></a>Pole, která popisují operaci

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Pole, která popisují, jak byla operace ověřena

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

### <a name="fields-that-describe-the-service"></a>Pole, která popisují službu

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>Viz také

- Popis Azure Storage monitorování najdete v tématu [monitorování Azure Storage](monitor-blob-storage.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md) .
