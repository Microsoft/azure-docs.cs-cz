---
title: Reakce na události úložiště objektů Blob Azure | Dokumenty společnosti Microsoft
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
author: normesta
ms.author: normesta
ms.date: 04/06/2020
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: d9c666fd6fcf020908b6fc5bdd639261853ad9c6
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811539"
---
# <a name="reacting-to-blob-storage-events"></a>Reakce na události služby Blob Storage

Události Azure Storage umožňují aplikacím reagovat na události, jako je například vytváření a odstraňování objektů BLOB. Činí tak bez nutnosti složitého kódu nebo nákladné a neefektivní volební služby. Nejlepší na tom je, že platíte jen za to, co používáte.

Události úložiště objektů blob se předkládají pomocí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) předplatitelům, jako jsou azure functions, Azure Logic Apps nebo dokonce na vlastní http listener. Event Grid poskytuje spolehlivé doručování událostí do vašich aplikací prostřednictvím bohatých zásad opakování a nedoručených nápisů.

Úplný seznam událostí, které podporuje úložiště objektů blob, najdete v článku schéma [událostí úložiště objektů blob.](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Mezi běžné scénáře událostí úložiště objektů blob patří zpracování obrázků nebo videa, indexování hledání nebo jakýkoli pracovní postup orientovaný na soubor. Asynchronní nahrávání souborů se skvěle hodí pro události. Pokud změny nejsou časté, ale váš scénář vyžaduje okamžitou odezvu, architektura založená na událostech může být obzvláště efektivní.

Pokud chcete vyzkoušet události úložiště objektů blob, podívejte se na některé z těchto článků rychlého startu:

|Pokud chcete použít tento nástroj:    |Viz tento článek: |
|--|-|
|portál Azure    |[Úvodní příručka: Směrování událostí úložiště objektů blob do koncového bodu webu pomocí portálu Azure](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Úvodní příručka: Směrování událostí úložiště do koncového bodu webu pomocí PowerShellu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Úvodní příručka: Směrování událostí úložiště do koncového bodu webu pomocí azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Podrobné příklady reakce na události úložiště objektů Blob pomocí funkcí Azure najdete v těchto článcích:

- [Kurz: Pomocí událostí Azure Data Lake Storage Gen2 aktualizujte tabulku Delta Databricks](data-lake-storage-events.md).
- [Kurz: Automatizace změna velikosti nahraných obrázků pomocí mřížky událostí](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Pouze účty úložiště druhu **StorageV2 (pro obecné účely v2)**, **BlockBlobStorage**a Integrace událostí podpory **BlobStorage.** **Úložiště (genral účel v1)** *nepodporuje* integraci s Event Grid.

## <a name="the-event-model"></a>Model události

Event Grid používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Tento obrázek znázorňuje vztah mezi vydavateli událostí, odběry událostí a obslužnými rutinami událostí.

![Model mřížky událostí](./media/storage-blob-event-overview/event-grid-functional-model.png)

Nejprve se přihlaste ke koncovému bodu události. Potom při aktivaci události, služba Event Grid odešle data o této události do koncového bodu.

V článku schéma [událostí úložiště objektů blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) se podívejte na:

> [!div class="checklist"]
> * Úplný seznam událostí úložiště objektů blob a jak se jednotlivé události aktivují.
> * Příklad dat, která by grid událostí odeslal pro každou z těchto událostí.
> * Účel každého páru hodnot klíče, který se zobrazí v datech.

## <a name="filtering-events"></a>Filtrování událostí

Události [objektu](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) Blob lze filtrovat podle typu události, názvu kontejneru nebo názvu objektu, který byl vytvořen nebo odstraněn. Filtry v event gridu odpovídají začátku nebo konci předmětu, takže události s odpovídajícím předmětem přejdou odběrateli.

Další informace o použití filtrů najdete v [tématu Filtrování událostí pro mřížku událostí](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

Předmět událostí úložiště objektů Blob používá formát:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Chcete-li porovnat všechny události pro účet úložiště, můžete nechat filtry předmětu prázdné.

Chcete-li porovnat události z objektů BLOB vytvořených v `subjectBeginsWith` sadě kontejnerů sdílejících předponu, použijte filtr, jako je:

```
/blobServices/default/containers/containerprefix
```

Chcete-li porovnat události z objektů BLOB `subjectBeginsWith` vytvořených v určitém kontejneru, použijte filtr, jako je:

```
/blobServices/default/containers/containername/
```

Chcete-li porovnat události z objektů BLOB vytvořených v konkrétním `subjectBeginsWith` kontejneru, který sdílí předponu názvu objektu BLOB, použijte filtr, jako je:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Chcete-li porovnat události z objektů BLOB vytvořených v konkrétním `subjectEndsWith` kontejneru sdílejícím příponu objektu blob, použijte filtr jako ".log" nebo ".jpg". Další informace naleznete v [tématu Koncepty mřížky událostí](../../event-grid/concepts.md#event-subscriptions).

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události

Aplikace, které zpracovávají události úložiště objektů Blob, by měly dodržovat několik doporučených postupů:
> [!div class="checklist"]
> * Jako více odběry lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události, je důležité nepředpokládat, že události jsou z určitého zdroje, ale zkontrolovat téma zprávy, aby bylo zajištěno, že pochází z účtu úložiště, který očekáváte.
> * Podobně zkontrolujte, zda eventType je ten, který jste připraveni ke zpracování a nepředpokládejte, že všechny události, které obdržíte, budou typy, které očekáváte.
> * Vzhledem k tomu, že zprávy mohou být doručeny po určité prodlevě, použijte pole etag, abyste zjistili, zda jsou informace o objektech stále aktuální. Informace o tom, jak používat pole etag, najdete v [tématu Správa souběžnosti v úložišti objektů Blob](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage). 
> * Jako zprávy mohou být doručeny mimo pořadí, použijte pole sekvenceru pochopit pořadí událostí na konkrétní objekt. Pole sekvenceru je řetězcová hodnota, která představuje logickou posloupnost událostí pro libovolný konkrétní název objektu blob. Standardní porovnání řetězců můžete použít k pochopení relativní posloupnosti dvou událostí na stejném názvu objektu blob.
> * Události úložiště zaručuje alespoň jednou doručení předplatitelům, což zajišťuje, že jsou výstupy všech zpráv. Však z důvodu opakování nebo dostupnost odběrů, může příležitostně dojít k duplicitní zprávy. Další informace o doručování zpráv a opakování naleznete v [tématu Message Grid události doručení a opakování](../../event-grid/delivery-and-retry.md).
> * Pomocí pole blobType zjistěte, jaký typ operací je povolen v objektu blob a které typy klientských knih, které byste měli použít pro přístup k objektu blob. Platné hodnoty `BlockBlob` jsou `PageBlob`buď nebo . 
> * Použijte pole url `CloudBlockBlob` s `CloudAppendBlob` konstruktory a pro přístup k objektu blob.
> * Ignorujte pole, kterým nerozumíte. Tento postup vám pomůže udržet odolnost vůči novým funkcím, které by mohly být přidány v budoucnu.
> * Pokud chcete zajistit, že **Microsoft.Storage.BlobCreated** událost se aktivuje pouze v případě, že `CopyBlob`objekt `PutBlob` `PutBlockList` blob bloku je zcela potvrzena, filtrovat událost pro volání rozhraní API , nebo `FlushWithClose` REST. Tato volání rozhraní API aktivují událost **Microsoft.Storage.BlobCreated** až poté, co jsou data plně potvrzena pro objekt blob bloku. Informace o vytvoření filtru naleznete v tématu [Filtrování událostí pro mřížku událostí](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o Event Grid a vyzkoušejte události úložiště objektů Blob:

- [Informace o službě Event Grid](../../event-grid/overview.md)
- [Schéma událostí úložiště objektů blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Směrování událostí úložiště objektů blob do vlastního koncového bodu webu](storage-blob-event-quickstart.md)
