---
title: Přidáním fulltextové vyhledávání do Azure Blob Storage – Azure Search
description: Procházení textový obsah v úložišti objektů Blob v Azure pro Azure Search indexování v kódu pomocí rozhraní HTTP REST API.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/01/2019
author: mgottein
manager: cgronlun
ms.author: magottei
ms.custom: seodec2018
ms.openlocfilehash: b7e7ecd2a82a8d64967288def9c6ede7a292f72a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61127575"
---
# <a name="searching-blob-storage-with-azure-search"></a>Prohledávání služby Blob Storage pomocí Azure Search

Hledání v rámci různých typů obsahu, které jsou uloženy v úložišti objektů Blob v Azure může být obtížné problém vyřešit. Můžete však indexování a vyhledávání obsahu objektů BLOB s použitím Azure Search pomocí několika kliknutí. Hledání v úložišti objektů Blob vyžaduje zřizování služby Azure Search. Různé omezení služeb a cenových úrovní Azure Search najdete na [stránce s cenami](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Co je Azure Search?
[Služba Azure Search](https://aka.ms/whatisazsearch) je vyhledávací služba, která umožňuje vývojářům snadno přidat výkonné fulltextové vyhledávání prostředí pro webové a mobilní aplikace. Služba Azure Search eliminuje nutnost spravovat nějakou infrastrukturu vyhledávání při nabízení [smlouva SLA na 99,9 % dostupnost](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Formát dokumentu enterprise indexování a vyhledávání
Díky podpoře [dokumentu extrakce](https://aka.ms/azsblobindexer) ve službě Azure Blob storage, můžete indexování následujícím obsahem:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Extrahováním textu a metadata z tyto typy souborů, můžete hledat ve více formátech s pomocí jediného dotazu. 

## <a name="search-through-your-blob-metadata"></a>Hledat metadata objektu blob
Běžný scénář, který umožňuje snadno seřadit prostřednictvím objektů BLOB s libovolným typem obsahu je index vlastních metadat a vlastností systému pro každý objekt blob. Tímto způsobem se indexuje zpětně informace pro všechny objekty BLOB bez ohledu na typ dokumentu. Můžete pak přejít k řazení, filtrování a omezující vlastnost napříč veškerý obsah objektu Blob úložiště.

[Přečtěte si že další informace o indexování objektů blob metadat.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Vyhledávání obrázků
Azure Search fulltextové vyhledávání, Fasetové navigace a možnosti řazení se teď může používat pro metadata uložená v objektech BLOB bitové kopie.

Kognitivní vyhledávání zahrnuje dovednosti zpracování obrázků, například [optického rozpoznávání znaků (OCR)](cognitive-search-skill-ocr.md) a identifikace [funkce visual](cognitive-search-skill-image-analysis.md) , které umožňuje indexování vizuálním obsahu nalezeném v každém obrázek.

## <a name="index-and-search-through-json-blobs"></a>Indexování a vyhledávání pomocí objektů BLOB JSON
Služba Azure Search je možné nakonfigurovat extrahujte strukturované obsahu nalezeném v objektech BLOB, které obsahují JSON. Služba Azure Search můžete čtení objektů BLOB JSON a analýza strukturovaných obsah do příslušných polí dokumentu Azure Search. Služba Azure Search můžete taky využít objekty BLOB, které obsahují pole objektů JSON a mapování každý prvek na samostatný dokument Azure Search.

Analýza formátu JSON není aktuálně dají konfigurovat na portálu. [Další informace o analýza formátu JSON ve službě Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Rychlý start
Služba Azure Search je přidat k objektům BLOB přímo na stránce portálu úložiště objektů Blob.

![](./media/search-blob-storage-integration/blob-blade.png)

Klikněte na tlačítko **přidat Azure Search** ke spuštění toku, kde můžete vybrat existující službu Azure Search nebo vytvořit novou službu. Pokud vytvoříte novou službu, jsou přejde z prostředí portálu účtu úložiště. Můžete přejít zpět na stránku portálu úložiště a znovu vyberte **přidat Azure Search** možnost, kde můžete vybrat existující službu.

## <a name="next-steps"></a>Další kroky
Další informace o Azure Search Blob indexeru v úplném [dokumentaci](https://aka.ms/azsblobindexer).
