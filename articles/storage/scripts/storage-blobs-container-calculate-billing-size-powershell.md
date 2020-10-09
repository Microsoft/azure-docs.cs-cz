---
title: Ukázka skriptu Azure PowerShell – výpočet celkové fakturační velikosti kontejneru objektů BLOB | Microsoft Docs
description: Vypočítá celkovou velikost kontejneru v úložišti objektů BLOB v Azure pro účely fakturace.
services: storage
author: fhryo-msft
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: fryu
ms.openlocfilehash: f8bd47a8fde700382f9789ab1c77ff35e9b1f4f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999516"
---
# <a name="calculate-the-total-billing-size-of-a-blob-container"></a>Vypočítat celkovou fakturační velikost kontejneru objektů BLOB

Tento skript vypočítá velikost kontejneru v úložišti objektů BLOB v Azure za účelem odhadu nákladů na fakturaci. Skript sčítá velikost objektů BLOB v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Tento skript PowerShellu vypočítá velikost kontejneru pro účely fakturace. Pokud počítáte velikost kontejneru pro jiné účely, přečtěte si téma [Výpočet celkové velikosti kontejneru úložiště objektů BLOB](../scripts/storage-blobs-container-calculate-size-powershell.md) pro jednodušší skript, který poskytuje odhad.

## <a name="determine-the-size-of-the-blob-container"></a>Určení velikosti kontejneru objektů BLOB

Celková velikost kontejneru objektů BLOB zahrnuje velikost samotného kontejneru a velikost všech objektů BLOB v kontejneru.

V následujících částech se dozvíte, jak se počítá kapacita úložiště pro kontejnery objektů BLOB a objekty blob.V následujícím oddílu len (X) znamená počet znaků v řetězci.

### <a name="blob-containers"></a>Kontejnery objektů blob

Následující výpočet popisuje, jak odhadnout objem využitého úložiště na kontejner objektů BLOB:

```
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
```

Toto je rozpis:
* 48 bajtů režie pro každý kontejner zahrnuje čas poslední změny, oprávnění, veřejné nastavení a některá metadata systému.

* Název kontejneru je uložený jako Unicode, takže převezme počet znaků a vynásobí ho dvěma.

* Pro každý blok metadat kontejneru objektů blob, který je uložený, ukládáme délku názvu (ASCII) a také délku řetězcové hodnoty.

* 512 bajtů na podepsaný identifikátor zahrnuje název podepsaného identifikátoru, čas spuštění, čas vypršení platnosti a oprávnění.

### <a name="blobs"></a>Objekty blob

Následující výpočty ukazují, jak odhadnout objem využitého úložiště na objekt BLOB.

* Objekt blob bloku (základní objekt BLOB nebo snímek):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
   SizeInBytes(data in unique committed data blocks stored) +
   SizeInBytes(data in uncommitted data blocks)
   ```

* Objekt blob stránky (základní objekt BLOB nebo snímek):

   ```
   124 bytes + Len(BlobName) * 2 bytes +
   For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
   number of nonconsecutive page ranges with data * 12 bytes +
   SizeInBytes(data in unique pages stored)
   ```

Toto je rozpis:

* 124 bajtů režie pro objekt blob, včetně:
    - Čas poslední změny
    - Velikost
    - Cache-Control
    - Typ obsahu
    - Obsah – jazyk
    - Kódování obsahu
    - Obsah – MD5
    - Oprávnění
    - Informace o snímku
    - Pronajat
    - Některá systémová metadata

* Název objektu BLOB je uložený jako Unicode, takže převezme počet znaků a vynásobí ho dvěma.

* Pro každý blok metadat, který je uložen, přidejte délku názvu (uloženou jako ASCII) a délku řetězcové hodnoty.

* Pro objekty blob bloku:
  * 8 bajtů pro seznam blokovaných.
  * Počet bloků pokusů o velikost ID bloku v bajtech
  * Velikost dat ve všech potvrzených a nepotvrzených blocích.

    >[!NOTE]
    >Když se používají snímky, tato velikost zahrnuje jenom jedinečná data pro tento základní nebo objekt BLOB snímku. Pokud nejsou nepotvrzené bloky použity po týdnu, jsou uvolněny do paměti. Pak se nebudou počítat směrem k fakturaci.

* Pro objekty blob stránky:
  * Počet nepo sobě jdoucích rozsahů stránek s časy dat 12 bajtů. Toto je počet jedinečných rozsahů stránek, které vidíte při volání rozhraní **GetPageRanges** API.

  * Velikost dat v bajtech všech uložených stránek.

    >[!NOTE]
    >Když se používají snímky, tato velikost zahrnuje jenom jedinečné stránky základního objektu BLOB nebo objektu BLOB snímku, který se počítá.

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>Další kroky

- Přečtěte si část [Výpočet celkové velikosti kontejneru úložiště objektů BLOB](../scripts/storage-blobs-container-calculate-size-powershell.md) pro jednoduchý skript, který poskytuje odhad velikosti kontejneru.

- Další informace o fakturaci Azure Storage najdete v tématu [Principy fakturace Windows Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

- Další informace o modulu Azure PowerShell najdete v [dokumentaci k Azure PowerShell](https://docs.microsoft.com/powershell/azure/).

- Další ukázkové skripty PowerShellu pro úložiště najdete v [ukázkách PowerShellu pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).
