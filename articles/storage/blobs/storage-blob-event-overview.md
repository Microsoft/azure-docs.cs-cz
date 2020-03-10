---
title: Reakce na události služby Azure Blob Storage | Microsoft Docs
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 78ec5b6d330f03d78dcb4e798b23d588fd93398e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387189"
---
# <a name="reacting-to-blob-storage-events"></a>Reakce na události služby Blob Storage

Azure Storage události umožňují aplikacím reagovat na události, jako je vytváření a odstraňování objektů BLOB. Je to bez nutnosti složitosti složitého kódu nebo nákladných a neefektivních služeb cyklického dotazování.

Události jsou nabízeny pomocí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pro předplatitele jako Azure Functions, Azure Logic Apps nebo dokonce vlastní naslouchací proces http. Nejlepší část je jenom za to, co využijete.

Úložiště objektů BLOB odesílá události do Event Grid, které do vašich aplikací poskytují spolehlivé doručování událostí prostřednictvím zásad pro pokusy o opakování a nedoručených zpráv.

Mezi běžné scénáře událostí služby Blob Storage patří zpracování obrázků a videa, indexování vyhledávání nebo jakýkoli pracovní postup orientovaný na soubory. Asynchronní nahrávání souborů je velmi vhodné pro události. Pokud jsou změny nečasté, ale scénář vyžaduje okamžitou odezvu, může být velmi efektivní architektura založená na událostech.

Pokud si to chcete vyzkoušet hned teď, podívejte se na některý z těchto článků rychlý Start:

|Pokud chcete použít tento nástroj:    |Viz tento článek: |
|--|-|
|Azure Portal    |[Rychlý Start: směrování událostí služby Blob Storage do webového koncového bodu pomocí Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Rychlý Start: směrování událostí úložiště do webového koncového bodu pomocí PowerShellu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Rychlý Start: směrování událostí úložiště do webového koncového bodu pomocí Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Pokud má váš účet hierarchický obor názvů, v tomto kurzu se dozvíte, jak propojit dohromady Event Grid předplatné, funkci Azure a [úlohu](https://docs.azuredatabricks.net/user-guide/jobs.html) v Azure Databricks: [kurz: použití Azure Data Lake Storage Gen2ch událostí k aktualizaci rozdílové tabulky datacihly](data-lake-storage-events.md).

>[!NOTE]
> Integraci událostí podporují jenom účty úložiště typu **StorageV2 (obecné účely v2)** a **BlobStorage** . **Úložiště (Genral pro účely V1)** *nepodporuje integraci* s Event Grid.

## <a name="the-event-model"></a>Model události

Event Grid používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí předplatitelům. Tento obrázek znázorňuje vztah mezi vydavateli událostí, odběry událostí a obslužnými rutinami událostí.

![Model Event Grid](./media/storage-blob-event-overview/event-grid-functional-model.png)

Nejdřív se přihlaste k odběru koncového bodu události. Po aktivaci události pak služba Event Grid odešle data o této události do koncového bodu.

Podívejte se na článek o [schématu událostí služby Blob Storage](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) , který se zobrazí:

> [!div class="checklist"]
> * Úplný seznam událostí služby Blob Storage a způsob aktivace každé události.
> * Příklad dat, která Event Grid odeslat pro každou z těchto událostí.
> * Účel dvojice klíč-hodnota, která se zobrazí v datech.

## <a name="filtering-events"></a>Filtrování událostí

Události objektů BLOB [lze filtrovat](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) podle typu události, názvu kontejneru nebo názvu objektu, který byl vytvořen nebo odstraněn. Filtry v Event Grid se shodují na začátku nebo konci předmětu, aby události s odpovídajícím subjektem přešly na odběratele.

Další informace o tom, jak používat filtry, najdete v tématu [filtrování událostí pro Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Předmět událostí služby Blob Storage používá formát:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Aby se shodovaly všechny události pro účet úložiště, můžete nechat filtry předmětu prázdné.

Chcete-li spárovat události objektů BLOB vytvořených v sadě kontejnerů, které sdílí předponu, použijte `subjectBeginsWith` filtr jako:

```
/blobServices/default/containers/containerprefix
```

Pokud chcete párovat události z objektů BLOB vytvořených v konkrétním kontejneru, použijte `subjectBeginsWith` filtr jako:

```
/blobServices/default/containers/containername/
```

Chcete-li spárovat události objektů BLOB vytvořených v konkrétním kontejneru sdílením předpony názvu objektu blob, použijte `subjectBeginsWith` filtr jako:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Chcete-li spárovat události objektů BLOB vytvořených v konkrétním kontejneru sdílení přípony objektu blob, použijte `subjectEndsWith` filtr, například ". log" nebo ". jpg". Další informace najdete v tématu [Event Grid koncepty](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události

Aplikace, které zpracovávají události služby Blob Storage, by měly dodržovat několik doporučených postupů:
> [!div class="checklist"]
> * Vzhledem k tomu, že je možné nakonfigurovat více předplatných pro směrování událostí ke stejné obslužné rutině události, je důležité, abyste nepředpokládali události z konkrétního zdroje, ale pokud chcete zkontrolovat téma zprávy, abyste měli jistotu, že pochází z účtu úložiště, který očekáváte.
> * Podobně ověřte, zda je typ eventType, který je připraven ke zpracování, a nepředpokládá se, že všechny události, které obdržíte, budou takové typy, které očekáváte.
> * Vzhledem k tomu, že zprávy mohou docházet mimo pořadí a po nějaké prodlevě, použijte pole ETag k pochopení, zda jsou informace o objektech stále aktuální.  Pomocí polí Sequencer můžete také pochopit pořadí událostí u libovolného konkrétního objektu.
> * Pomocí pole blobType můžete pochopit, jaký typ operací je u objektu BLOB povolený, a jaké typy klientských knihoven byste měli použít pro přístup k objektu BLOB. Platné hodnoty jsou buď `BlockBlob`, nebo `PageBlob`. 
> * Pro přístup k objektu BLOB použijte pole URL s konstruktory `CloudBlockBlob` a `CloudAppendBlob`.
> * Ignorujte pole, která nerozumíte. Tento postup vám pomůže zajistit odolný přístup k novým funkcím, které se v budoucnu můžou přidat.
> * Chcete-li zajistit, aby se událost **Microsoft. Storage. BlobCreated** aktivovala pouze v případě, že je objekt blob bloku zcela potvrzen, vyfiltrujte událost pro `CopyBlob`volání `PutBlob`, `PutBlockList` nebo `FlushWithClose` REST API. Tato volání rozhraní API aktivují událost **Microsoft. Storage. BlobCreated** až po úplném potvrzení dat do objektu blob bloku. Další informace o tom, jak vytvořit filtr, najdete v tématu [filtrování událostí pro Event Grid](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a podělte se o události služby Blob Storage:

- [Informace o službě Event Grid](../../event-grid/overview.md)
- [Směrování událostí služby Blob Storage do vlastního webového koncového bodu](storage-blob-event-quickstart.md)
