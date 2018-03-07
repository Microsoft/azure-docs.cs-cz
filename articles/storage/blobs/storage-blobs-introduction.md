---
title: "Seznámení se službou Azure Blob Storage | Microsoft Docs"
description: "Seznámení se službou Azure Blob Storage"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 09/28/2017
ms.author: tamram
ms.openlocfilehash: aabc10b1c1c501e4fa0eb31835c2818cfb8942c6
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="introduction-to-blob-storage"></a>Seznámení se službou Blob Storage

Azure Blob Storage je služba, která slouží k ukládání velkých objemů nestrukturovaných dat objektů, například textu nebo binárních dat, která jsou přístupná odkudkoli na světě prostřednictvím protokolů HTTP nebo HTTPS. Službu Blob Storage můžete používat ke zveřejňování dat pro celý svět, nebo k soukromému ukládání dat aplikací.

Mezi běžná použití služby Blob Storage patří:

* poskytování obrázků nebo dokumentů přímo do prohlížeče
* ukládání souborů pro distribuovaný přístup
* streamování videa a zvuku
* ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
* ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba

## <a name="blob-service-concepts"></a>Koncepty služby Blob service

Služba Blob service obsahuje následující součásti:

![Architektura objektu blob](./media/storage-blobs-introduction/blob1.png)

* **Účet úložiště:** Veškerý přístup k úložišti Azure se provádí prostřednictvím účtu úložiště. Tento účet úložiště může být **Účet úložiště pro obecné účely (v1 nebo v2)** nebo **Účet úložiště objektů blob**. Další informace najdete v tématu [Účty Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

* **Kontejner:** Kontejner zajišťuje seskupení sady objektů blob. Všechny objekty blob musí být v kontejneru. Účet může obsahovat neomezený počet kontejnerů. Kontejner můžete pojmout neomezený počet objektů blob. Všimněte si, že název kontejneru musí být psaný malými písmeny.

* **Objekt blob:** Soubor libovolného typu a velikosti. Azure Storage nabízí tři typy objektů blob: objekty blob bloku, [objekty blob stránky](storage-blob-pageblob-overview.md) a doplňovací objekty blob.
  
    *Objekty blob bloků* jsou ideální pro ukládání textových nebo binárních souborů, například dokumentů a multimediálních souborů. *Doplňovací objekty blob* jsou podobné objektům blob bloku v tom, že je tvoří bloky, ale jsou optimalizované pro doplňovací operace, takže se hodí pro scénáře protokolování. Jeden objekt blob bloku může obsahovat až 50 000 bloků, každý o velikosti až 100 MB, v celkové velikosti o něco větší než 4,75 TB (100 MB × 50 000). Jeden doplňovací objekt blob může obsahovat až 50 000 bloků, každý o velikosti až 4 MB, v celkové velikosti o něco větší než 195 GB (4 MB × 50 000).
  
    *Objekty blob stránky* můžou mít velikost až 8 TB a jsou efektivnější pro časté operace čtení a zápisu. Služba Azure Virtual Machines používá objekty blob stránek jako disky pro operační systém a ukládání dat.
  
    Podrobnosti o vytváření názvů kontejnerů a objektů blob najdete v článku [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).

## <a name="next-steps"></a>Další kroky

* [vytvořit účet úložiště](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Začínáme se službou Blob Storage s použitím .NET](storage-dotnet-how-to-use-blobs.md)
* [Ukázky pro Azure Storage s použitím .NET](../common/storage-samples-dotnet.md)
* [Ukázky pro Azure Storage s použitím Javy](../common/storage-samples-java.md)
