---
title: Reakce na události úložiště objektů Blob v Azure | Dokumentace Microsoftu
description: Pomocí služby Azure Event Grid se můžete přihlásit k odběru událostí služby Blob Storage.
services: storage,event-grid
author: cbrooksmsft
ms.author: cbrooks
ms.date: 01/30/2018
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: c0655d02fd5d0d64c22db286236b2a26f9e70619
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444684"
---
# <a name="reacting-to-blob-storage-events"></a>Reakce na události služby Blob storage

Události služby Azure Storage umožňují aplikacím reagovat na události, jako je například vytváření a odstraňování objektů BLOB s využitím moderní architektury bez serveru. Dělá to bez nutnosti složité kódu nebo nákladné a neefektivní cyklického dotazování služby.

Místo toho události se nasdílejí [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pro předplatitele, třeba Azure Functions, Azure Logic Apps, nebo dokonce vlastní naslouchací proces protokolu http a Plaťte jenom za to můžete použít.

Události služby BLOB storage se spolehlivě odesílají do služby Event Grid, která nabízí spolehlivé doručování aplikací prostřednictvím zásad opakování bohaté a doručování onta nedoručených zpráv.

Běžné scénáře události úložiště objektů Blob obsahovat obrázek nebo video zpracování, indexování nebo jakýkoli pracovní postup souboru objektově orientovaný. Nahrávání souborů asynchronní se skvěle hodí k události. Pokud jsou málo časté změny, ale vaše situace vyžaduje okamžitou odezvu, může být zvláště efektivní architektury založené na události.

Pokud chcete to teď vyzkoušejte, přečtěte si téma některého z těchto článků rychlý start:

|Pokud chcete tento nástroj použijte:    |Najdete v tomto článku: |
|--|-|
|Azure Portal    |[Rychlé zprovoznění: Směrování událostí služby Blob storage do webového koncového bodu pomocí webu Azure portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure CLI    |[Rychlé zprovoznění: Směrování událostí služby storage do webového koncového bodu pomocí Powershellu](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Rychlé zprovoznění: Směrování událostí služby úložiště na webový koncový bod pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="the-event-model"></a>Model událostí

Pomocí služby Event Grid [odběry událostí](../../event-grid/concepts.md#event-subscriptions) pro směrování zpráv událostí pro předplatitele. Tento obrázek ukazuje vztah mezi zdroji událostí, odběry událostí a obslužných rutin událostí.

![Event Grid modelu](./media/storage-blob-event-overview/event-grid-functional-model.png)

Nejprve odběru koncový bod na událost. Potom když se aktivuje událost, služby Event Grid bude odesílat data o této události do koncového bodu.

Zobrazit [schématu událostí úložiště objektů Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) článku znalostní báze:

> [!div class="checklist"]
> * Úplný seznam událostí služby Blob storage a jak se aktivuje každá událost.
> * Příklad dat služby Event Grid bude posílat pro každou z těchto událostí.
> * Účel každé dvojici klíče a hodnoty, které se zobrazí v datech.

## <a name="filtering-events"></a>Filtrování událostí

Odběry událostí objektu BLOB je možné filtrovat podle typu události a tak, že název kontejneru a název objektu blob objektu, který byl vytvořen nebo odstranit.  Filtry lze použít u odběrů událostí buď během [vytváření](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) odběru události nebo [později](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). Předmět filtry ve službě Event Grid práci na základě "začíná řetězcem" a "končí řetězcem" shody, takže události s odpovídající předmětem doručovaly do odběratele.

Další informace o tom, jak používat filtry, naleznete v tématu [pro Event Grid umožňuje filtrovat události](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).

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
> * Pokud chcete zajistit, aby **Microsoft.Storage.BlobCreated** událost se aktivuje, jenom když je objekt Blob bloku zcela potvrzeny, filtrovat události pro `CopyBlob`, `PutBlob`, `PutBlockList` nebo `FlushWithClose` REST Volání rozhraní API. Tato aktivační událost volání rozhraní API **Microsoft.Storage.BlobCreated** událostí až po data jsou zcela potvrzeny do objektu Blob bloku. Zjistěte, jak vytvořit filtr, najdete v článku [pro Event Grid umožňuje filtrovat události](https://docs.microsoft.com/azure/event-grid/how-to-filter-events).


## <a name="next-steps"></a>Další postup

Další informace o službě Event Grid a vyzkoušejte událostí služby Blob storage:

- [Informace o službě Event Grid](../../event-grid/overview.md)
- [Úložiště objektů Blob události směrovat do vlastního webového koncového bodu](storage-blob-event-quickstart.md)
