---
title: Reaktivní události Azure Blob Storage | Microsoft Docs
description: Přihlásit k odběru událostí úložiště objektů Blob pomocí Azure událostí mřížky.
services: storage,event-grid
keywords: ''
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.openlocfilehash: ea2ec712c8d8b5f85f020535ab0544986f0da53a
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="reacting-to-blob-storage-events"></a>Reaktivní události úložiště objektů Blob

Událostí Azure Storage umožňují aplikacím reagovat na vytváření a odstraňování objektů BLOB pomocí moderní architektury bez serveru. Dělá to tak, bez nutnosti složitý kód nebo neefektivní a drahé dotazování služby.  Místo toho jsou události instaluje [mřížky událostí Azure](https://azure.microsoft.com/services/event-grid/) odběratelům jako [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), nebo dokonce vlastní naslouchací proces protokolu http vlastní a můžete pouze platí se používá. 

Běžné scénáře událostí úložiště objektů Blob zahrnovat zpracování obrazu nebo video, indexování pro hledání nebo žádný pracovní postup zaměřené na konkrétní soubor.  Nahrávání souborů asynchronní jsou skvělý přizpůsobit pro události.  Pokud změny jsou jen zřídka, ale váš scénář vyžaduje okamžité odezvy, může být zvlášť efektivní architektura založená na události.

Dostupnost pro události úložiště je vázaný na události mřížky [dostupnosti](../../event-grid/overview.md) a bude dostupná v jiných oblastech, stejně jako událost mřížky. Podívejte se na [události úložiště objektů Blob trasy pro vlastní web koncový bod - rozhraní příkazového řádku](storage-blob-event-quickstart.md) nebo [události úložiště objektů Blob trasy pro vlastní web koncový bod - PowerShell](storage-blob-event-quickstart-powershell.md) rychlé příklad. 

![Model událostí mřížky](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Účty služby Blob Storage
Nejsou k dispozici v objektu BLOB úložiště události [účty úložiště Blob](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) a v [účty úložiště v2 obecné účely](../common/storage-account-options.md#general-purpose-v2). **Obecné účely v2 (GPv2)** jsou účty úložiště, které podporují všechny funkce pro všechny služby úložiště, včetně objektů BLOB, soubory, fronty a tabulky. A **účtem služby Blob storage** je specializovaný účet úložiště pro ukládání nestrukturovaných dat jako blobů (objektů) ve službě Azure Storage. Účty úložiště BLOB jsou podobné účtům úložiště pro obecné účely a sdílejí všechny skvělé odolnost, dostupnost, škálovatelnost a výkon funkce používají. jednotlivá včetně 100 % konzistentnost rozhraní API pro objekty BLOB bloku a doplňovací objekty BLOB. V případě aplikací, které vyžadují jenom úložiště objektů blob bloku nebo objektů blob doporučujeme používat účty úložiště objektů blob. 

## <a name="available-blob-storage-events"></a>K dispozici události úložiště objektů Blob
Událost mřížky používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí odběratelům.  Odběry událostí úložiště objektů BLOB může zahrnovat dva typy událostí:  

> |Název události|Popis|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Aktivováno při vytvoření nebo nahradit prostřednictvím objektu blob `PutBlob`, `PutBlockList`, nebo `CopyBlob` operace|
> |`Microsoft.Storage.BlobDeleted`|Aktivována, jestliže je prostřednictvím odstranit objekt blob `DeleteBlob` operace|

## <a name="event-schema"></a>Schématu události
Události úložiště objektů BLOB obsahují všechny informace, které je potřeba reagovat na změny v datech.  O událost úložiště objektů Blob můžete identifikovat, protože vlastnost eventType začíná "Microsoft.Storage."  
Další informace o použití vlastností události mřížky událostí je popsána v [schématu události událostí mřížky](../../event-grid/event-schema.md).  

> |Vlastnost|Typ|Popis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |Téma|řetězec|Id účtu úložiště, který vysílá události úplné Azure Resource Manager.|
> |Předmět|řetězec|Prostředků relativní cesta k objektu, která je předmětem události, používající stejný rozšířený formát Azure Resource Manager, který používáme k popisu účty úložiště, služeb a kontejnery pro Azure RBAC.  Tento formát obsahuje název objektu blob zachována.|
> |eventTime|řetězec|Datum a čas, který byl vytvořen události ve formátu ISO 8601|
> |eventType|řetězec|"Microsoft.Storage.BlobCreated" nebo "Microsoft.Storage.BlobDeleted"|
> |ID|řetězec|Jedinečný identifikátor, pokud událost|
> |dataVersion|řetězec|Verze schématu datového objektu.|
> |metadataVersion|řetězec|Verze schématu vlastností nejvyšší úrovně.|
> |data|objekt|Kolekce dat události specifické pro úložiště objektů blob|
> |data.contentType|řetězec|Typ obsahu objektu blob, jako by vrácená v hlavičce Content-Type z objektu blob|
> |data.contentLength|číslo|Velikost objektu blob jako celé číslo představující počet bajtů, jako by vrácená v hlavičce Content-Length z objektu blob.  Odeslat s BlobCreated událostí, ale ne při BlobDeleted.|
> |data.url|řetězec|Adresa url objektu, který je předmětem události|
> |data.eTag|řetězec|Značka etag objektu, když se tato událost je aktivována.  Pro událost BlobDeleted není k dispozici.|
> |data.api|řetězec|Název operace rozhraní api, která aktivuje tuto událost.  Pro události BlobCreated tato hodnota je "PutBlob", "PutBlockList" nebo "CopyBlob".  Pro události BlobDeleted tato hodnota je "DeleteBlob".  Tyto hodnoty jsou stejné názvy rozhraní api, které se nacházejí v diagnostických protokolů Azure Storage.  V tématu [protokolovat stavové zprávy a operace](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|řetězec|Hodnotu neprůhledný řetězec představující logické posloupnost událostí pro libovolný název konkrétní objekt blob.  Uživatelé mohou používat standardní řetězec porovnání pochopit relativní pořadí dvou událostí na stejný název objektu blob.|
> |data.requestId|řetězec|Id žádosti generované služby pro operace úložiště rozhraní API.  Můžete použít ke korelaci do služby Azure Storage diagnostických protokolů pomocí pole "hlavička požadavku id" v protokolech a je vrácena z inicializaci volání rozhraní API v hlavičce "x-ms-request-id". V tématu [formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|řetězec|Id žádosti zadaný klienta pro ukládání operace rozhraní API.  Můžete použít ke korelaci do diagnostickým protokolům Azure Storage pomocí pole "client-request-id" v protokolech a lze zadat do požadavků klientů pomocí hlavičku "x-ms-client-request-id". V tématu [formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.storageDiagnostics|objekt|Diagnostická data příležitostně zahrnuté ve službě Azure Storage.  Pokud jsou k dispozici, třeba ji ignorovat příjemci událostí.|
|data.blobType|řetězec|Typ objektu blob. Platné hodnoty jsou "BlockBlob" nebo "PageBlob".| 

Tady je příklad BlobCreated události:
```json
[{
  "topic": "/subscriptions/319a9601-1ec0-0000-aebc-8fe82724c81e/resourceGroups/testrg/providers/Microsoft.Storage/storageAccounts/myaccount",
  "subject": "/blobServices/default/containers/testcontainer/blobs/file1.txt",
  "eventType": "Microsoft.Storage.BlobCreated",
  "eventTime": "2017-08-16T01:57:26.005121Z",
  "id": "602a88ef-0001-00e6-1233-1646070610ea",
  "data": {
    "api": "PutBlockList",
    "clientRequestId": "799304a4-bbc5-45b6-9849-ec2c66be800a",
    "requestId": "602a88ef-0001-00e6-1233-164607000000",
    "eTag": "0x8D4E44A24ABE7F1",
    "contentType": "text/plain",
    "contentLength": 447,
    "blobType": "BlockBlob",
    "url": "https://myaccount.blob.core.windows.net/testcontainer/file1.txt",
    "sequencer": "00000000000000EB000000000000C65A",
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]

```

Další informace najdete v tématu [schématu události úložiště objektů Blob](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtrování událostí
Je možné filtrovat závislosti na typu události a název kontejneru a název objektu blob objektu, který byl vytvořen nebo odstranit odběry událostí objektů BLOB.  Filtry lze použít pro odběry událostí buď během [vytvoření](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) odběru událostí nebo [později](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Filtry subjektu pracovního událostí mřížky na základě "začíná písmenem" a "končí textem" odpovídá, tak, aby doručí události s odpovídající předmětem do odběratele. 

Předmět události úložiště objektů Blob používá formát:

```json
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Tak, aby odpovídaly všechny události pro účet úložiště, můžete ponechat filtry subjektu prázdný.

Vyhledat události z vytvořit v sadě sdílení předponu kontejnery objektů BLOB, použijte `subjectBeginsWith` filtrovat jako:

```json
/blobServices/default/containers/containerprefix
```

Vyhledat události z vytvořené v určitém kontejneru objektů BLOB, použijte `subjectBeginsWith` filtrovat jako:

```json
/blobServices/default/containers/containername/
```

Vyhledat události z objektů BLOB, které jsou vytvořené v určitém kontejneru sdílení předponu názvu objektu blob, použijte `subjectBeginsWith` filtrovat jako:

```json
/blobServices/default/containers/containername/blobs/blobprefix
```

Vyhledat události z objektů BLOB, které jsou vytvořené v konkrétním kontejneru sdílení příponu objektů blob, použijte `subjectEndsWith` filtru jako ".log" nebo ".jpg"

Další informace najdete v tématu [událostí mřížky koncepty](../../event-grid/concepts.md#filters).

## <a name="practices-for-consuming-events"></a>Postupy pro použití události
Aplikace, které zpracování událostí úložiště objektů Blob postupujte několik doporučených postupů:
> [!div class="checklist"]
> * Události trasy na stejné obslužné rutiny události je možné nakonfigurovat více předplatných, je důležité se předpokládá, že události jsou z určitého zdroje, ale ke kontrole tématu zprávu, která se ujistěte, že pocházejí z účtu úložiště, které očekáváte.
> * Podobně zkontrolujte, jestli typ události je jedním jsou připravené informace o procesu a Nepředpokládejte, že všechny události, které vám bude typy, které očekáváte.
> * Můžete doručování zpráv mimo pořadí a po některé zpoždění, slouží k pochopení, pokud je vaše informace o objektech stále aktuální pole značka etag.  Pole aplikace sequencer také slouží k pochopení pořadí událostí na každý konkrétní objekt.
> * Pomocí pole blobType pochopit, jaký typ operace na objektu blob jsou povoleny a které klientské knihovny typů jste měli používat pro přístup k objektu blob. Platné hodnoty jsou buď `BlockBlob` nebo `PageBlob`. 
> * Do pole Adresa url se `CloudBlockBlob` a `CloudAppendBlob` konstruktory pro přístup k objektu blob.
> * Pole, která nerozumíte ignorujte.  Tento postup vám pomůže, abychom vás odolné nové funkce, které se může v budoucnu přidat.


## <a name="next-steps"></a>Další postup

Další informace o události mřížky a poskytněte události úložiště objektů Blob zkuste to:

- [Informace o službě Event Grid](../../event-grid/overview.md)
- [Koncový bod vlastní webové směrovat úložiště objektů Blob události](storage-blob-event-quickstart.md)
