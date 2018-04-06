---
title: Seznámení se službou Blob Storage – Úložiště objektů v Azure | Microsoft Docs
description: Služba Azure Blob Storage je navržená k ukládání velkých objemů nestrukturovaných dat objektů, jako jsou textová nebo binární data. Vaše aplikace můžou k objektům ve službě Blob Storage přistupovat z PowerShellu nebo Azure CLI, z kódu prostřednictvím klientských knihoven pro Azure Storage nebo přes REST.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: overview
ms.date: 03/27/2018
ms.author: tamram
ms.openlocfilehash: 0fff0032ec2452413bcd1df3175634b14a64208f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-blob-storage"></a>Seznámení se službou Blob Storage

Azure Blob Storage je řešení cloudového úložiště Microsoftu pro datové objekty. Blob Storage dokáže ukládat velké objemy nestrukturovaných dat objektů, jako jsou textová nebo binární data. Přístup k datům ve službě Blob Storage je možný odkudkoli na světě přes protokol HTTP nebo HTTPS. Službu Blob Storage můžete používat ke zveřejňování dat pro celý svět, nebo k soukromému ukládání dat aplikací.

Mezi běžná použití služby Blob Storage patří:

* poskytování obrázků nebo dokumentů přímo do prohlížeče
* ukládání souborů pro distribuovaný přístup
* streamování videa a zvuku
* ukládání dat pro zálohování a obnovování, zotavení po havárii a pro archivaci
* ukládání dat, které bude analyzovat místní nebo v Azure hostovaná služba
* ukládání virtuálních pevných disků pro použití se službou Azure Virtual Machines

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
