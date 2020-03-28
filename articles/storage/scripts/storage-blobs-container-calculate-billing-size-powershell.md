---
title: Ukázka skriptu Azure PowerShell – výpočet celkové velikosti fakturace kontejneru objektů blob | Dokumenty společnosti Microsoft
description: Vypočítejte celkovou velikost kontejneru v úložišti objektů Blob Azure pro účely fakturace.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: 12b32256c91dfcf93ca55eeb348cc78613ba860e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067104"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Výpočet celkové velikosti fakturace kontejneru objektů blob

Tento skript vypočítá velikost kontejneru v úložišti objektů Blob Azure pro účely odhadu fakturačních nákladů. Skript stotals velikost objektů BLOB v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Tento skript prostředí PowerShell vypočítá velikost kontejneru pro účely fakturace. Pokud počítáte velikost kontejneru pro jiné účely, [přečtěte si téma Výpočet celkové velikosti kontejneru úložiště objektů Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) pro jednodušší skript, který poskytuje odhad.

## <a name="determine-the-size-of-the-blob-container"></a>Určení velikosti kontejneru objektů blob

Celková velikost kontejneru objektů blob zahrnuje velikost samotného kontejneru a velikost všech objektů BLOB pod kontejnerem.

Následující části popisují, jak se vypočítá kapacita úložiště pro kontejnery objektů blob a objekty BLOB.V následující části Len(X) znamená počet znaků v řetězci.

### <a name="blob-containers"></a>Kontejnery s objekty blob

Následující výpočet popisuje, jak odhadnout množství úložiště, které je spotřebováno na kontejner objektů blob:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Níže je rozdělení:
* 48 bajtů režie pro každý kontejner zahrnuje čas poslední změny, oprávnění, veřejná nastavení a některá systémová metadata.

* Název kontejneru je uložen jako Unicode, takže se počet znaků a vynásobte dvěma.

* Pro každý blok metadat kontejneru objektů blob, který je uložen, ukládáme délku názvu (ASCII) plus délku hodnoty řetězce.

* 512 bajtů na podepsaný identifikátor zahrnuje název podepsaného identifikátoru, čas zahájení, čas vypršení platnosti a oprávnění.

### <a name="blobs"></a>Objekty blob

Následující výpočty ukazují, jak odhadnout množství úložiště spotřebovaného na objekt blob.

* Objekt blob bloku (základní objekt blob nebo snímek):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Objekt blob stránky (základní objekt blob nebo snímek):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Níže je rozdělení:

* 124 bajtů režie pro objekt blob, který zahrnuje:
    - Čas poslední změny
    - Velikost
    - Řízení mezipaměti
    - Typ obsahu
    - Obsah-jazyk
    - Kódování obsahu
    - Obsah-MD5
    - Oprávnění
    - Informace o snímku
    - Pronájem
    - Některá systémová metadata

* Název objektu blob je uložen jako Unicode, takže vezměte počet znaků a vynásobte dvěma.

* Pro každý blok metadat, který je uložen, přidejte délku názvu (uložené jako ASCII) plus délku hodnoty řetězce.

* Pro objekty BLOB bloku:
  * 8 bajtů pro seznam blokování.
  * Počet bloků krát velikost ID bloku v bajtů.
  * Velikost dat ve všech potvrzených a nepotvrzených bloků.

    >[!NOTE]
    >Při použití snímků tato velikost zahrnuje pouze jedinečná data pro tento základní nebo snímek objektu blob. Pokud nepotvrzené bloky nejsou použity po týdnu, jsou uvolněny. Poté se nezapočítávají do fakturace.

* Pro objekty BLOB stránky:
  * Počet nepo sobě jdoucích rozsahů stránek s daty krát 12 bajtů. Toto je počet jedinečných rozsahů stránek, které se zobrazí při volání rozhraní **API GetPageRanges.**

  * Velikost dat v bajtů všech uložených stránek.

    >[!NOTE]
    >Při použití snímků tato velikost zahrnuje pouze jedinečné stránky pro základní objekt blob nebo snímek objektu blob, který se počítá.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Další kroky

- Viz [Výpočet celkové velikosti kontejneru úložiště objektů Blob](../scripts/storage-blobs-container-calculate-size-powershell.md) pro jednoduchý skript, který poskytuje odhad velikosti kontejneru.

- Další informace o fakturaci služby Azure Storage najdete [v tématu Principy fakturace úložiště Windows Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Další informace o modulu Azure PowerShell najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview).

- Další ukázky skriptů Prostředí PowerShell najdete ve [ukázkách Prostředí PowerShell pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).
