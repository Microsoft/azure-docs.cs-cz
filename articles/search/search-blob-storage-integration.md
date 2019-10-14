---
title: Přidání fulltextového vyhledávání do Azure Blob Storage
titleSuffix: Azure Search
description: Při vytváření fulltextového fulltextového vyhledávání v Azure Search extrakce obsahu a přidání struktury do objektů blob Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 7e5eb73cc6abc72689bbc674b29f4d288dd66b6f
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302906"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Přidání fulltextového vyhledávání do dat objektů BLOB v Azure pomocí Azure Search

Hledání v nejrůznějších typech obsahu uložených ve službě Azure Blob Storage může být obtížné vyřešit problém. Můžete ale indexovat a hledat obsah objektů BLOB jenom několika kliknutími pomocí Azure Search. Vyhledávání v úložišti objektů BLOB vyžaduje zřízení Azure Search služby. Různá omezení služby a cenové úrovně Azure Search najdete na [stránce s cenami](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Co je Azure Search?
[Azure Search](https://aka.ms/whatisazsearch) je vyhledávací služba, která vývojářům usnadňuje přidávání robustních funkcí fulltextového vyhledávání do webových a mobilních aplikací. Jako služba Azure Search odstraňuje nutnost spravovat infrastrukturu vyhledávání a zároveň nabízí [smlouvu SLA o 99,9% provozu](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Rejstřík a hledání ve formátech podnikových dokumentů
Díky podpoře [extrakce dokumentů](https://aka.ms/azsblobindexer) v úložišti objektů BLOB v Azure můžete indexovat následující obsah:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Extrahováním textu a metadat z těchto typů souborů můžete hledat v různých formátech souborů jediným dotazem. 

## <a name="search-through-your-blob-metadata"></a>Hledání v metadatech objektů BLOB
Běžný scénář, který usnadňuje řazení pomocí objektů BLOB libovolného typu obsahu, je vytvoření indexu vlastních metadat i systémových vlastností pro každý objekt BLOB. Tímto způsobem jsou informace o všech objektech blob indexovány bez ohledu na typ dokumentu. Pak můžete pokračovat s řazením, filtrováním a omezujícími vlastnostmi v celém obsahu služby Blob Storage.

[Přečtěte si další informace o indexování metadat objektů BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Hledání obrázků
U metadat imagí uložených v objektech blob se teď dají použít fulltextové vyhledávání, omezující navigace a možnosti řazení. Azure Search

Rozpoznávání rozpoznávání zahrnuje dovednosti zpracování obrázků, jako je [optické rozpoznávání znaků (OCR)](cognitive-search-skill-ocr.md) a identifikace [vizuálních funkcí](cognitive-search-skill-image-analysis.md) , které umožňují indexovat vizuální obsah nalezený v jednotlivých obrazech.

## <a name="index-and-search-through-json-blobs"></a>Indexování a hledání prostřednictvím objektů BLOB JSON
Azure Search lze nakonfigurovat pro extrakci strukturovaného obsahu nalezeného v objektech blob, které obsahují JSON. Azure Search mohou číst objekty blob JSON a analyzovat strukturovaný obsah do příslušných polí Azure Search dokumentu. Azure Search může také přebírat objekty blob, které obsahují pole objektů JSON, a namapovat jednotlivé prvky na samostatný Azure Search dokument.

Analýzu JSON nejde v tuto chvíli konfigurovat prostřednictvím portálu. [Další informace o analýze JSON najdete v Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Rychlý start
Azure Search lze do objektů BLOB přidat přímo ze stránky portálu úložiště objektů BLOB.

![](./media/search-blob-storage-integration/blob-blade.png)

Kliknutím na **přidat Azure Search** spustíte tok, ve kterém můžete vybrat existující službu Azure Search nebo vytvořit novou službu. Pokud vytvoříte novou službu, přejdete mimo prostředí portálu účtu úložiště. Můžete přejít zpátky na stránku portálu úložiště a znovu vybrat možnost **přidat Azure Search** , kde můžete vybrat existující službu.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o Azure Search indexeru objektů BLOB v celé [dokumentaci](https://aka.ms/azsblobindexer).
