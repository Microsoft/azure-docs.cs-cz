---
title: Referenční informace k datům monitorování Azure Storage | Microsoft Docs
description: Reference k protokolům a metrikám pro monitorování dat z Azure Storage.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.openlocfilehash: 7ba66441a87e3e02483ae27400f9900d2d052af4
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118154"
---
# <a name="azure-storage-monitoring-data-reference"></a>Referenční informace o monitorování Azure Storage dat

Podrobnosti o shromažďování a analýze dat monitorování pro Azure Storage najdete v tématu [monitorování Azure Storage](monitor-storage.md) .

## <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro Azure Storage. 

### <a name="capacity-metrics"></a>Metriky kapacity

Hodnoty metrik kapacity se odesílají do Azure Monitor každou hodinu. Hodnoty se aktualizují každý den. Časové rozpětí definuje časový interval, pro který jsou prezentovány hodnoty metrik. Podporovaná časová zrna pro všechny metriky kapacity je jedna hodina (PT1H).

Azure Storage poskytuje v Azure Monitor následující metriky kapacity.

#### <a name="account-level"></a>Úroveň účtu

| Metrika | Popis |
| ------------------- | ----------------- |
| UsedCapacity | Velikost úložiště využitého účtem úložiště U standardních účtů úložiště je to součet kapacity využité objektem blob, tabulkou, souborem a frontou. U účtů úložiště úrovně Premium a účtů služby Blob se rovná hodnotě BlobCapacity. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |

#### <a name="blob-storage"></a>Blob Storage

| Metrika | Popis |
| ------------------- | ----------------- |
| BlobCapacity | Celkem úložiště objektů BLOB použité v účtu úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 <br/> Dimensions: **BlobType**a **BlobTier** ([definice](#metrics-dimensions)) |
| BlobCount    | Počet objektů BLOB uložených v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 <br/> Dimensions: **BlobType**a **BlobTier** ([definice](#metrics-dimensions)) |
| ContainerCount    | Počet kontejnerů v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| IndexCapacity     | Velikost úložiště, kterou používá ADLS Gen2 hierarchický index <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |

#### <a name="table-storage"></a>Table Storage

| Metrika | Popis |
| ------------------- | ----------------- |
| TableCapacity | Velikost úložiště tabulek, kterou používá účet úložiště <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| TableCount   | Počet tabulek v účtu úložiště <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| TableEntityCount | Počet entit tabulky v účtu úložiště <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |

#### <a name="queue-storage"></a>Queue Storage

| Metrika | Popis |
| ------------------- | ----------------- |
| QueueCapacity | Velikost úložiště fronty, kterou účet úložiště používá <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| QueueCount   | Počet front v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| QueueMessageCount | Počet zpráv ve frontě s neplatným vypršením platnosti v účtu úložiště <br/><br/>Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |

#### <a name="file-storage"></a>File Storage

| Metrika | Popis |
| ------------------- | ----------------- |
| Kapacita zařízení | Velikost úložiště souborů, kterou používá účet úložiště. <br/><br/> Jednotka: bajtů <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| FileCount   | Počet souborů v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |
| FileShareCount | Počet sdílených složek v účtu úložiště. <br/><br/> Jednotka: počet <br/> Typ agregace: průměr <br/> Příklad hodnoty: 1024 |

### <a name="transaction-metrics"></a>Transakční metriky

Metriky transakcí jsou vydávány při každém požadavku na účet úložiště z Azure Storage do Azure Monitor. V případě, že váš účet úložiště neobsahuje žádné aktivity, nebudou v obdobích metrik transakcí žádná data. Všechny transakční metriky jsou k dispozici na úrovni účtu i služby (úložiště objektů blob, úložiště tabulek, soubory Azure a úložiště front). Časové rozpětí definuje časový interval, po který jsou zobrazeny hodnoty metriky. Podporovaná časová zrna pro všechny transakční metriky jsou PT1H a PT1M.

Azure Storage poskytuje následující metriky transakcí v Azure Monitor.

| Metrika | Popis |
| ------------------- | ----------------- |
| Transakce | Počet požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Toto číslo zahrnuje úspěšné i neúspěšné požadavky a také požadavky, u kterých došlo k chybě. <br/><br/> Jednotka: počet <br/> Typ agregace: celkem <br/> Použitelné dimenze: ResponseType, typ, ApiName a ověřování ([definice](#metrics-dimensions))<br/> Příklad hodnoty: 1024 |
| Příchozí přenos dat | Množství příchozích dat. Toto číslo zahrnuje příchozí přenos dat z externího klienta do služby Azure Storage i příchozí přenos dat v rámci Azure. <br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Výchozí přenos dat | Množství výchozích dat. Toto číslo zahrnuje výchozí přenos dat z externího klienta do služby Azure Storage i výchozí přenos dat v rámci Azure. Kvůli tomu toto číslo nepředstavuje fakturovatelný výchozí přenos dat. <br/><br/> Jednotka: bajtů <br/> Typ agregace: celkem <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessServerLatency | Průměrná doba zpracování úspěšného požadavku službou Azure Storage. Tato hodnota nezahrnuje latenci sítě zadanou v metrice Celková latence při úspěchu. <br/><br/> Jednotka: milisekundy <br/> Typ agregace: průměr <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| SuccessE2ELatency | Průměrná celková latence úspěšných požadavků provedených na službu úložiště nebo zadanou operaci rozhraní API. Tato hodnota zahrnuje čas zpracování ve službě Azure Storage potřebný k přečtení požadavku, odeslání odpovědi a přijetí potvrzení dané odpovědi. <br/><br/> Jednotka: milisekundy <br/> Typ agregace: průměr <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 1024 |
| Dostupnost | Procento dostupnosti pro službu úložiště nebo zadanou operaci rozhraní API. Dostupnost se počítá tak, že se hodnota celkových fakturovatelných požadavků vydělí počtem příslušných požadavků včetně těch, u kterých došlo k neočekávaným chybám. Všechny neočekávané chyby mají pro službu úložiště nebo zadanou operaci rozhraní API za následek sníženou dostupnost. <br/><br/> Jednotka: procenta <br/> Typ agregace: průměr <br/> Použitelné dimenze: typ, ApiName a ověřování ([definice](#metrics-dimensions)) <br/> Příklad hodnoty: 99,99 |

<a id="metrics-dimensions" />

## <a name="metrics-dimensions"></a>Dimenze metrik

Azure Storage podporuje pro metriky v Azure Monitor následující dimenze.

| Název dimenze | Popis |
| ------------------- | ----------------- |
| **BlobType** | Typ objektu BLOB pouze pro metriky objektů BLOB Podporované hodnoty jsou **BlockBlob**, **PageBlob**a **Azure Data Lake Storage**. Doplňovací objekt BLOB je zahrnutý v BlockBlob. |
| **BlobTier** | Služba Azure Storage nabízí různé úrovně přístupu, které umožňují ukládat data objektů BLOB nejefektivnějším způsobem. Další informace najdete v [Azure Storage úrovni objektů BLOB](../blobs/storage-blob-storage-tiers.md). Mezi podporované hodnoty patří: <br/> <li>**Hot**: vrstva Hot</li> <li>**Studená**: studená vrstva</li> <li>**Archivace**: archivní vrstva</li> <li>**Premium**: úroveň Premium pro objekt blob bloku</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**: typy vrstev pro objekt blob stránky úrovně Premium</li> <li>**Standard**: typ vrstvy pro objekt blob stránky úrovně Standard</li> <li>**Nevrstvený**: typ vrstvy pro účet úložiště pro obecné účely v1</li> |
| **Typ** | Transakce z primárního nebo sekundárního clusteru. Dostupné hodnoty zahrnují **Primary** a **Secondary**. Vztahuje se na geograficky redundantní úložiště s přístupem pro čtení (RA-GRS) při čtení objektů ze sekundárního tenanta. |
| **ResponseType** | Typ odpovědi transakce. Dostupné hodnoty zahrnují: <br/><br/> <li>**ServerOtherError**: všechny ostatní chyby na straně serveru s výjimkou těch popsaných </li> <li>**ServerBusyError**: ověřený požadavek, který vrátil stavový kód HTTP 503. </li> <li>**ServerTimeoutError**: vypršel časový limit ověřené žádosti, která vrátila stavový kód HTTP 500. Časový limit vypršel kvůli chybě serveru. </li> <li>**AuthorizationError**: ověřený požadavek, který se nezdařil z důvodu neoprávněného přístupu k datům nebo selhání autorizace. </li> <li>**NetworkError**: ověřený požadavek, který se nezdařil z důvodu chyb sítě. K tomu nejčastěji dochází, když klient předčasně ukončí spojení před vypršením časového limitu. </li><li>**ClientAccountBandwidthThrottlingError**: požadavek se omezuje na šířku pásma pro překročení [limitů škálovatelnosti účtu úložiště](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError**: požadavek se omezuje na míru požadavků pro překročení [limitů škálovatelnosti účtu úložiště](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: Další chyba omezování na straně klienta. ClientAccountBandwidthThrottlingError a ClientAccountRequestThrottlingError jsou vyloučené.</li> <li>**ClientTimeoutError**: vypršel časový limit ověřené žádosti, která vrátila stavový kód HTTP 500. Pokud je časový limit sítě klienta nebo časový limit žádosti nastavený na hodnotu nižší, než služba úložiště očekávala, jde o očekávané vypršení časového limitu. V opačném případě bude ohlášeno jako ServerTimeoutError. </li> <li>**ClientOtherError**: všechny ostatní chyby na straně klienta s výjimkou těch popsaných. </li> <li>**Úspěch**: úspěšná žádost</li> <li> **SuccessWithThrottling**: požadavek byl úspěšný, když se klient SMB omezí při prvním pokusu, ale po opakování dojde k úspěšnému dokončení.</li> |
| **ApiName** | Název operace. Příklad: <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Všechny názvy operací naleznete v [dokumentu](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Authentication** | Typ ověřování používaný v transakcích. Dostupné hodnoty zahrnují: <br/> <li>**AccountKey**: transakce se ověřuje pomocí klíče účtu úložiště.</li> <li>**SAS**: transakce je ověřena pomocí sdílených přístupových podpisů.</li> <li>**OAuth**: transakce je ověřená pomocí přístupových tokenů OAuth.</li> <li>**Anonymní**: transakce se požaduje anonymně. Neobsahuje požadavky na kontrolu před výstupem.</li> <li>**AnonymousPreflight**: transakce je požadavkem na kontrolu před výstupem.</li> |

Pro metriky podporující dimenze je nutné zadat hodnotu dimenze, aby se zobrazily odpovídající hodnoty metrik. Například pokud se podíváte na hodnotu **transakce** pro úspěšné odpovědi, je nutné filtrovat dimenzi **ResponseType** s **úspěchem**. Nebo pokud se podíváte na **BlobCount** hodnotu pro objekt blob bloku, musíte filtrovat dimenzi **BlobType** pomocí **BlockBlob**.

## <a name="resource-logs-preview"></a>Protokoly prostředků (Preview)

> [!NOTE]
> Protokoly Azure Storage v Azure Monitor jsou ve verzi Public Preview a jsou dostupné pro testování ve verzi Preview ve všech oblastech veřejného cloudu. Pokud se chcete zaregistrovat ve verzi Preview, podívejte se na [tuto stránku](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Tato verze Preview umožňuje protokoly objektů BLOB (včetně Azure Data Lake Storage Gen2), souborů, front a tabulek a účtů Premium Storage v účtech úložiště pro obecné účely V1 a obecné účely v2. Klasické účty úložiště se nepodporují.

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

| Vlastnost | Popis |
|:--- |:---|
|**interval** | Světový čas koordinovaný (UTC), kdy se požadavek přijal úložištěm. Například: `2018/11/08 21:09:36.6900118`.|
|**Prostředku** | ID prostředku účtu úložiště. Příklad: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**kategorií** | Kategorie požadované operace. Například: `StorageRead` , `StorageWrite` , nebo `StorageDelete` .|
|**operationName** | Typ operace REST, která byla provedena. <br> Úplný seznam operací najdete v tématu [Analýza úložiště protokolovaných operací a zpráv o stavu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Verze služby úložiště, která byla určena při podání žádosti. To je ekvivalentní hodnotě v hlavičce **x-MS-Version** . Například: `2017-04-17`.|
|**schemaVersion** | Verze schématu protokolu Například: `1.0`.|
|**statusCode** | Stavový kód protokolu HTTP pro požadavek. Pokud je požadavek přerušený, může být tato hodnota nastavena na `Unknown` . <br> Příklad: `206` |
|**statusText** | Stav požadované operace.  Úplný seznam stavových zpráv najdete v tématu [Analýza úložiště protokolovaných operací a zpráv o stavu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Ve verzi 2017-04-17 a novější se stavová zpráva `ClientOtherError` nepoužívá. Místo toho obsahuje toto pole kód chyby. Příklad: `SASSuccess`  |
|**durationMs** | Celková doba, vyjádřená v milisekundách k provedení požadované operace. To zahrnuje čas ke čtení příchozího požadavku a k odeslání odpovědi žadateli. Například: `12`.|
|**callerIpAddress** | IP adresa žadatele včetně čísla portu. Například: `192.100.0.102:4362`. |
|**correlationId** | ID, které se používá ke korelaci protokolů napříč prostředky. Například: `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**oblasti** | Umístění účtu úložiště. Například: `North Europe`. |
|**protokol**|Protokol, který se používá v operaci. Například: `HTTP` , `HTTPS` , `SMB` nebo`NFS`|
| **identifikátor URI** | Požadovaný identifikátor Uniform Resource. Například: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

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

| Vlastnost | Popis |
|:--- |:---|
|**Identita/typ** | Typ ověřování, které se použilo k vytvoření žádosti. Například: `OAuth` , `SAS Key` , `Account Key` nebo`Anonymous` |
|**Identita/tokenHash**|Toto pole je rezervováno pouze pro interní použití. |
|**autorizace/akce** | Akce, která je přiřazena k žádosti. Příklad: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**autorizace/roleAssignmentId** | ID přiřazení role Například: `4e2521b7-13be-4363-aeda-111111111111`.|
|**autorizace/roleDefinitionId** | ID definice role Například: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**objekty zabezpečení/ID** | ID objektu zabezpečení Například: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**objekty zabezpečení/typ** | Typ objektu zabezpečení Například: `ServicePrincipal`. |
|**žadatel/appID** | ID aplikace Open Authorization (OAuth), které se používá jako žadatel. <br> Například: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**žadatel/cílová skupina** | Cílová skupina OAuth žádosti. Například: `https://storage.azure.com`. |
|**žadatel/objectId** | ID objektu OAuth žadatele. V případě ověřování protokolem Kerberos představuje identifikátor objektu ověřeného uživatele protokolu Kerberos. Například: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**žadatel/tenantId** | ID tenanta OAuth identity. Například: `72f988bf-86f1-41af-91ab-222222222222`.|
|**žadatel/tokenIssuer** | Vystavitel tokenu OAuth. Například: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**žadatel nebo hlavní název uživatele** | Hlavní název uživatele (UPN) žadatele. Například: `someone@contoso.com`. |
|**žadatel/uživatelské jméno** | Toto pole je rezervováno pouze pro interní použití.|

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

| Vlastnost | Popis |
|:--- |:---|
|**accountName** | Název účtu úložiště Například: `mystorageaccount`.  |
|**requestUrl** | Adresa URL, která je požadována. Například: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | Hodnota **hlavičky User-Agent** v uvozovkách. Například: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Hodnota hlavičky **odkazujícího** serveru Například: `http://contoso.com/about.html`.|
|**ID žádosti klienta** | Hodnota hlavičky **x-MS-Client-Request-ID** žádosti. Například: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**značk** | Identifikátor ETag vráceného objektu v uvozovkách Například: `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Celkový čas vyjádřený v milisekundách k provedení požadované operace. Tato hodnota nezahrnuje latenci sítě (čas pro čtení příchozího požadavku a odeslání odpovědi žadateli). Například: `22`. |
|**serviceType** | Služba přidružená k této žádosti Například: `blob` , `table` , `files` nebo `queue` . |
|**operationCount** | Počet všech protokolovaných operací, které jsou součástí žádosti. Tento počet začíná indexem `0` . Některé požadavky vyžadují více než jednu operaci, například požadavek na zkopírování objektu BLOB. Většina požadavků provádí pouze jednu operaci. Například: `1`. |
|**requestHeaderSize** | Velikost hlavičky požadavku vyjádřená v bajtech Například: `578`. <br>Pokud je žádost neúspěšná, tato hodnota může být prázdná. |
|**requestBodySize** | Velikost paketů požadavků vyjádřených v bajtech, které jsou čteny službou úložiště. <br> Například: `0`. <br>Pokud je žádost neúspěšná, tato hodnota může být prázdná.  |
|**responseHeaderSize** | Velikost hlavičky odpovědi vyjádřená v bajtech Například: `216`. <br>Pokud je žádost neúspěšná, tato hodnota může být prázdná.  |
|**responseBodySize** | Velikost paketů odpovědí zapsaných službou úložiště (v bajtech). Pokud je žádost neúspěšná, tato hodnota může být prázdná. Například: `216`.  |
|**requestMd5** | Hodnota buď v hlavičce **Content-MD5** , nebo v hlavičce **x-MS-Content-MD5** v žádosti. Hodnota hash MD5 zadaná v tomto poli představuje obsah v žádosti. Například: `788815fd0198be0d275ad329cafd1830`. <br>Toto pole může být prázdné.  |
|**serverMd5** | Hodnota hash MD5 vypočítaná službou úložiště Například: `3228b3cf1069a5489b298446321f8521`. <br>Toto pole může být prázdné.  |
|**Časposledníúpravy** | Čas poslední změny (LMT) vráceného objektu.  Například: `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Toto pole je prázdné pro operace, které mohou vracet více objektů. |
|**conditionsUsed** | Středníkem oddělený seznam párů klíč-hodnota, které reprezentují podmínku. Podmínky mohou být následující: <li> If-Modified – od <li> Pokud-nezměněno – od <li> If-Match <li> If-None-Match  <br> Například: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Hodnota záhlaví Content-Length pro požadavek odeslanou službě úložiště. Pokud byla žádost úspěšná, tato hodnota se rovná requestBodySize. Pokud je žádost neúspěšná, tato hodnota nesmí být rovna requestBodySize nebo může být prázdná. |
|**tlsVersion** | Verze TLS použitá v souvislosti s připojením žádosti. Například: `TLS 1.2`. |
|**smbTreeConnectID** | Protokol SMB (Server Message Block) **treeConnectId** byl vytvořen v době připojení ke stromu. Příklad: `0x3` |
|**smbPersistentHandleID** | ID trvalého popisovače z žádosti o vytvoření SMB2, která je trvale připojená k síti.  Odkazováno v [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Trvalé**. Příklad: `0x6003f` |
|**smbVolatileHandleID** | Nestálé ID popisovače z žádosti o vytvoření SMB2, která se recykluje při opětovném připojení k síti.  Odkazováno v [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 jako **SMB2_FILEID. Volatile**. Příklad: `0xFFFFFFFF00000065` |
|**smbMessageID** | Relativní připojení **MessageID**. Příklad: `0x3b165` |
|**smbCreditsConsumed** | Příchozí nebo odchozí výstup spotřebovaný žádostí v jednotkách 64 KB. Příklad: `0x3` |
|**smbCommandDetail** | Další informace o této konkrétní žádosti místo obecného typu požadavku. Příklad: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | Identifikátor **FileID** přidružený k souboru nebo adresáři.  Zhruba se podobá systému souborů NTFS FileId. Příklad: `0x9223442405598953` |
|**smbSessionID** | SMB2 **SessionID** vytvořená v čase nastavení relace. Příklad: `0x8530280128000049` |
|**smbCommandMajor UInt32** | Hodnota v **příkazu SMB2_HEADER.** V současné době je to číslo mezi 0 a 18 včetně. Příklad: `0x6` |
|**smbCommandMinor** | Podtřída **SmbCommandMajor**, kde je to vhodné. Příklad: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Viz také

- Popis Azure Storage monitorování najdete v tématu [monitorování Azure Storage](monitor-storage.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../../azure-monitor/insights/monitor-azure-resource.md) .