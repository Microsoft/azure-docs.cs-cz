---
title: Reakce na události úložiště objektů Blob v Azure | Dokumentace Microsoftu
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.component: blobs
ms.openlocfilehash: 6f3afa22a50728070c42cd6e2eff0cc148815fbc
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262680"
---
# <a name="reacting-to-blob-storage-events"></a>Reakce na události služby Blob storage

Události služby Azure Storage umožňují aplikacím reagovat na vytváření a odstraňování objektů BLOB pomocí moderní architektury bez serveru. Dělá to bez nutnosti složité kódu nebo nákladné a neefektivní cyklického dotazování služby.  Místo toho události se nasdílejí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pro předplatitele, jako [Azure Functions](https://azure.microsoft.com/services/functions/), [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/), nebo dokonce vlastní naslouchací proces protokolu http vlastní a pouze Plaťte za to co používáte. 

Běžné scénáře události úložiště objektů Blob obsahovat obrázek nebo video zpracování, indexování nebo jakýkoli pracovní postup souboru objektově orientovaný.  Nahrávání souborů asynchronní se skvěle hodí k události.  Pokud jsou málo časté změny, ale vaše situace vyžaduje okamžitou odezvu, může být zvláště efektivní architektury založené na události.

Dostupnost událostí služby Storage závisí na [dostupnosti](../../event-grid/overview.md) služby Event Grid a v dalších oblastech bude zpřístupněna společně se službou Event Grid. Podívejte se na [událostí služby směrování Blob storage do vlastního webového koncového bodu – rozhraní příkazového řádku](storage-blob-event-quickstart.md) nebo [událostí služby směrování Blob storage do vlastního webového koncového bodu - PowerShell](storage-blob-event-quickstart-powershell.md) rychlý příklad. 

![Event Grid modelu](./media/storage-blob-event-overview/event-grid-functional-model.png)

## <a name="blob-storage-accounts"></a>Účty úložiště Blob
Události služby BLOB storage jsou k dispozici v [účty Blob storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-storage-accounts) a [účty úložiště pro obecné účely v2](../common/storage-account-options.md#general-purpose-v2-accounts). Účty pro **obecné účely v2 (GPv2)** jsou účty úložiště, které podporují všechny funkce všech služeb úložiště, včetně objektů blob, souborů, front a tabulek. **Účet úložiště objektů blob** je specializovaný účet úložiště pro ukládání nestrukturovaných dat v podobě objektů blob do služby Azure Storage. Účty úložiště objektů blob jsou podobné účtům úložiště pro obecné účely a mají stejně vysokou odolnost, dostupnost, škálovatelnost a výkonnost, a navíc mají 100% konzistentnost rozhraní API pro objekty blob bloku a doplňovací objekty blob. V případě aplikací, které vyžadují jenom úložiště objektů blob bloku nebo objektů blob doporučujeme používat účty úložiště objektů blob. 

## <a name="available-blob-storage-events"></a>K dispozici události služby Blob storage
Pomocí služby Event grid [odběry událostí](../../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí pro předplatitele.  Odběry událostí pro úložiště objektů BLOB může obsahovat dva typy událostí:  

> |Název události|Popis|
> |----------|-----------|
> |`Microsoft.Storage.BlobCreated`|Je aktivována při vytvoření nebo nahradit prostřednictvím objektu blob `PutBlob`, `PutBlockList`, nebo `CopyBlob` operace|
> |`Microsoft.Storage.BlobDeleted`|Je aktivována při odstranění objektu blob prostřednictvím `DeleteBlob` operace|

## <a name="event-schema"></a>Schéma událostí
Události služby BLOB storage obsahovat všechny informace, které je potřeba reagovat na změny ve vašich datech.  Události úložiště objektů Blob můžete identifikovat, protože vlastnost Typ události začíná řetězcem "Microsoft.Storage". Další informace o použití vlastností událostí služby Event Grid je popsána v [schéma událostí služby Event Grid](../../event-grid/event-schema.md).  

> |Vlastnost|Typ|Popis|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |téma|řetězec|Úplné id Azure Resource Manageru z účtu úložiště, který vysílá události.|
> |Předmět|řetězec|Prostředků relativní cesta k objektu, který je předmětem události ve formátu stejné rozšířené Azure Resource Manageru, který jsme použili pro popis účtů úložiště, služby a kontejnerů pro Azure RBAC.  Tento formát obsahuje název objektu blob zachování případ.|
> |čas události|řetězec|Datum a čas, byla událost vygenerována ve formátu ISO 8601|
> |Typ události|řetězec|"Microsoft.Storage.BlobCreated" nebo "Microsoft.Storage.BlobDeleted"|
> |ID|řetězec|Jedinečný identifikátor, pokud se tato událost|
> |dataVersion|řetězec|Verze schématu datového objektu|
> |verze metadataVersion|řetězec|Verze schématu vlastnosti nejvyšší úrovně.|
> |data|objekt|Kolekce dat událostí specifické pro úložiště objektů blob|
> |data.contentType|řetězec|Typ obsahu objektu blob, protože by být vrácená v hlavičce Content-Type z objektu blob|
> |data.contentLength|číslo|Velikost objektu blob jako celé číslo představující počet bajtů, protože by být vrácená v hlavičce Content-Length z objektu blob.  Odeslané s BlobCreated událostí, ale ne s BlobDeleted.|
> |data.url|řetězec|Adresa url objektu, který je předmětem události|
> |data.eTag|řetězec|Značka etag objektu, když se tato událost aktivuje.  Pro událost BlobDeleted není k dispozici.|
> |data.api|řetězec|Název operace rozhraní api, který aktivuje tuto událost. Pro události BlobCreated tato hodnota je "PutBlob", "PutBlockList" nebo "CopyBlob". Pro události BlobDeleted tato hodnota je "DeleteBlob". Tyto hodnoty jsou stejné názvy rozhraní api, které se nacházejí v diagnostické protokoly služby Azure Storage. Zobrazit [protokoluje operace a stavové zprávy](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).|
> |data.sequencer|řetězec|Hodnota typu neprůhledný řetězec představující logickou posloupnost událostí pro jakýkoli název konkrétního objektu blob.  Uživatele můžete použít standardní porovnání řetězců k pochopení relativního pořadí dvou událostí na stejný název objektu blob.|
> |data.requestId|řetězec|Id generovaných službou požadavku pro operaci úložiště rozhraní API. Můžete použít ke korelaci do služby Azure Storage diagnostické protokoly pomocí pole "hlavička požadavku id" v protokolech a vrátí se v inicializaci volání rozhraní API v hlavičce "x-ms-request-id". Zobrazit [formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format).|
> |data.clientRequestId|řetězec|Id žádosti klienta – Pokud pro ukládání operace rozhraní API. Je možné korelovat diagnostické protokoly služby Azure Storage pomocí pole "client-request-id" v protokolech a lze zadat pomocí "x-ms klienta request-id" záhlaví žádosti klientů. Zobrazit [formát protokolu](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-log-format). |
> |data.storageDiagnostics|objekt|Diagnostická data v některých součástí služby Azure Storage. Pokud je k dispozici, by měl být ignorován příjemci událostí.|
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

Další informace najdete v tématu [schématu událostí úložiště objektů Blob](../../event-grid/event-schema-blob-storage.md).

## <a name="filtering-events"></a>Filtrování událostí
Odběry událostí objektu BLOB je možné filtrovat podle typu události a tak, že název kontejneru a název objektu blob objektu, který byl vytvořen nebo odstranit.  Filtry lze použít u odběrů událostí buď během [vytváření](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_create) odběru události nebo [později](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest#az_eventgrid_event_subscription_update). Předmět filtry ve službě Event Grid práci na základě "začíná řetězcem" a "končí řetězcem" shody, takže události s odpovídající předmětem doručovaly do odběratele. 

Předmět události služby Blob storage používá formát:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Tak, aby odpovídaly všechny události pro účet úložiště, můžete nechat filtry předmětu prázdný.

Vyhledání události z objekty BLOB vytvořené v sadě kontejnery předponu pro sdílení obsahu `subjectBeginsWith` filtrovat stejně jako:

```
/blobServices/default/containers/containerprefix
```

Vyhledání události z objektů BLOB v kontejneru pro konkrétní vytvoří `subjectBeginsWith` filtrovat stejně jako:

```
/blobServices/default/containers/containername/
```

Vyhledání události z objektů BLOB v kontejneru pro konkrétní sdílení předponu názvu objektu blob vytvoří, `subjectBeginsWith` filtrovat stejně jako:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Vyhledání události z objektů BLOB v kontejneru pro konkrétní příponu objektů blob pro sdílení obsahu vytvoří, `subjectEndsWith` filtr jako ".log" nebo ".jpg". Další informace najdete v tématu [koncepty Event Grid](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Postupy pro spotřebovávajících událostí
Aplikace, které zpracovávají události služby Blob storage postupujte podle několik doporučených postupů:
> [!div class="checklist"]
> * Směrování událostí na stejný ovladač událostí dá nakonfigurovat několik předplatných, je důležité se předpokládá, že události pocházejí z určitého zdroje, ale ke kontrole tématu zprávy, která se ujistěte, že pocházejí z účtu úložiště, které jste očekávali.
> * Podobně zkontrolujte, že typ události jsou připraveny k procesu, a Nepředpokládejte, že všechny události, které se zobrazí typy, které očekáváte, že bude.
> * Jak můžete doručování zpráv mimo pořadí a po určité prodlevě, použijte pole etag pochopit, pokud je stále aktuální informace o objektech.  Také použijte pole aplikace sequencer k vysvětlení pořadí událostí pro jakékoli konkrétní objekt.
> * Použijte pole blobType pochopit, jaký typ operace jsou povolené pro objekt blob a která Klientská knihovna typů by měl používat pro přístup k objektu blob. Platné hodnoty jsou buď `BlockBlob` nebo `PageBlob`. 
> * Použijte pole Adresa url se `CloudBlockBlob` a `CloudAppendBlob` konstruktory pro přístup k objektu blob.
> * Pole, která nevíte, ignorujte. Tento postup vám pomůže pokračovat je odolné k novým funkcím, které mohou být přidány v budoucnu.


## <a name="next-steps"></a>Další postup

Další informace o službě Event Grid a vyzkoušejte událostí služby Blob storage:

- [Informace o službě Event Grid](../../event-grid/overview.md)
- [Úložiště objektů Blob události směrovat do vlastního webového koncového bodu](storage-blob-event-quickstart.md)
