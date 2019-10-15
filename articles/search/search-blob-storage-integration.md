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
ms.openlocfilehash: 1aec65ab08cd1c0711e51a222a8e674ef56ef508
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312194"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Přidání fulltextového vyhledávání do dat objektů BLOB v Azure pomocí Azure Search

Hledání v nejrůznějších typech obsahu uložených ve službě Azure Blob Storage může být obtížné vyřešit problém. Můžete ale indexovat a hledat obsah objektů BLOB jenom několika kliknutími pomocí [Azure Search](search-what-is-azure-search.md). Azure Search má integrovanou integraci pro indexování služby Blob Storage prostřednictvím [*indexeru objektů BLOB*](search-howto-indexing-azure-blob-storage.md) , který přidává do indexování možnosti pro vytváření zdrojů dat.

## <a name="supported-content-types"></a>Podporované typy obsahu

Spuštěním indexeru objektů BLOB v kontejneru můžete extrahovat text a metadata z následujících typů obsahu jediným dotazem:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Volitelně můžete připojit [rozšíření AI](search-blob-ai-integration.md) ve formě *dovednosti* k vytváření nových informací a struktury z objektů blob, včetně textových reprezentace souborů obrázků. Přidání rozšíření AI rozbalí seznam typ obsahu, aby zahrnoval formát JPEG a PNG. Přidává dovednosti zpracování obrázků, jako je [optické rozpoznávání znaků (OCR)](cognitive-search-skill-ocr.md) a identifikace [vizuálních funkcí](cognitive-search-skill-image-analysis.md) , které umožňují indexovat vizuální obsah nalezený v jednotlivých obrazech.

## <a name="search-through-your-blob-metadata"></a>Hledání v metadatech objektů BLOB
Běžný scénář, který usnadňuje řazení pomocí objektů BLOB libovolného typu obsahu, je vytvoření indexu vlastních metadat i systémových vlastností pro každý objekt BLOB. Tímto způsobem jsou informace o všech objektech blob indexovány bez ohledu na typ dokumentu. Pak můžete pokračovat s řazením, filtrováním a omezujícími vlastnostmi v celém obsahu služby Blob Storage.

[Přečtěte si další informace o indexování metadat objektů BLOB.](https://aka.ms/azsblobmetadataindexing)

## <a name="index-and-search-through-json-blobs"></a>Indexování a hledání prostřednictvím objektů BLOB JSON
Azure Search lze nakonfigurovat pro extrakci strukturovaného obsahu nalezeného v objektech blob, které obsahují JSON. Azure Search mohou číst objekty blob JSON a analyzovat strukturovaný obsah do příslušných polí Azure Search dokumentu. Azure Search může také přebírat objekty blob, které obsahují pole objektů JSON, a namapovat jednotlivé prvky na samostatný Azure Search dokument.

Analýzu JSON nejde v tuto chvíli konfigurovat prostřednictvím portálu. [Další informace o analýze JSON najdete v Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quickstart"></a>Rychlý start
Azure Search lze do objektů BLOB přidat přímo ze stránky portálu úložiště objektů BLOB.

![](./media/search-blob-storage-integration/blob-blade.png)

Kliknutím na **přidat Azure Search** spustíte tok, ve kterém můžete vybrat existující službu Azure Search nebo vytvořit novou službu. Pokud vytvoříte novou službu, přejdete mimo prostředí portálu účtu úložiště. Můžete přejít zpátky na stránku portálu úložiště a znovu vybrat možnost **přidat Azure Search** , kde můžete vybrat existující službu.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o Azure Search indexeru objektů BLOB v celé [dokumentaci](https://aka.ms/azsblobindexer).
