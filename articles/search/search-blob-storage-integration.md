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
ms.openlocfilehash: 24886d8b8a7b679f6474789748a002c8d045a746
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331282"
---
# <a name="add-full-text-search-to-azure-blob-data-using-azure-search"></a>Přidání fulltextového vyhledávání do dat objektů BLOB v Azure pomocí Azure Search

Hledání v nejrůznějších typech obsahu uložených ve službě Azure Blob Storage může být obtížné vyřešit problém. Můžete ale indexovat a hledat obsah objektů BLOB jenom několika kliknutími pomocí [Azure Search](search-what-is-azure-search.md). Azure Search má integrovanou integraci pro indexování služby Blob Storage prostřednictvím [*indexeru objektů BLOB*](search-howto-indexing-azure-blob-storage.md) , který přidává do indexování možnosti pro vytváření zdrojů dat.

## <a name="supported-content-types"></a>Podporované typy obsahu

Spuštěním indexeru objektů BLOB v kontejneru můžete extrahovat text a metadata z následujících typů obsahu jediným dotazem:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Volitelně můžete připojit [rozšíření AI](search-blob-ai-integration.md) ve formě *dovednosti* k vytváření nových informací a struktury z objektů blob, včetně textových reprezentace souborů obrázků. Přidání rozšíření AI rozbalí seznam typ obsahu, aby zahrnoval formát JPEG a PNG. Přidává dovednosti zpracování obrázků, jako je [optické rozpoznávání znaků (OCR)](cognitive-search-skill-ocr.md) a identifikace [vizuálních funkcí](cognitive-search-skill-image-analysis.md) , které umožňují indexovat vizuální obsah nalezený v jednotlivých obrazech.

## <a name="search-through-your-blob-metadata"></a>Hledání v metadatech objektů BLOB
Běžný scénář, který usnadňuje řazení pomocí objektů BLOB libovolného typu obsahu, je vytvoření indexu vlastních metadat i systémových vlastností pro každý objekt BLOB. Tímto způsobem jsou informace pro všechny objekty blob indexovány bez ohledu na typ dokumentu uložený v indexu v Azure Search. Pomocí nového indexu pak můžete pokračovat v řazení, filtrování a omezující podmínky v celém obsahu služby Blob Storage.

### <a name="indexing-json-blobs"></a>Indexování objektů BLOB JSON
Azure Search lze nakonfigurovat pro extrakci strukturovaného obsahu nalezeného v objektech blob, které obsahují JSON. Azure Search mohou číst objekty blob JSON a analyzovat strukturovaný obsah do příslušných polí Azure Search dokumentu. Azure Search může také přebírat objekty blob, které obsahují pole objektů JSON, a namapovat jednotlivé prvky na samostatný Azure Search dokument. Můžete nastavit režim analýzy tak, aby ovlivnil typ objektu JSON vytvořený indexerem.

## <a name="how-to-get-started"></a>Jak začít

Můžete začít přímo na stránce portálu účtu úložiště. Kliknutím na **přidat Azure Search** spustíte tok, ve kterém můžete vybrat existující službu Azure Search nebo vytvořit novou službu. Pokud vytvoříte novou službu, přejdete mimo prostředí portálu účtu úložiště. Můžete přejít zpátky na stránku portálu úložiště a znovu vybrat možnost **přidat Azure Search** , kde můžete vybrat existující službu. 

![](./media/search-blob-storage-integration/blob-blade.png)

Pokud již máte v rámci stejného předplatného existující vyhledávací službu, kliknutím na tlačítko **přidat Azure Search** otevřete Průvodce importem dat, abyste mohli okamžitě Krokovat s indexováním, rozšířením a definicí indexu.

Můžete také [vytvořit službu Azure Search](search-create-index-portal.md) a definovat indexery objektů blob, které vyžádají obsah z kontejnerů objektů BLOB.

Následující rychlé starty a kurzy používají data objektů BLOB:

+ [Indexování částečně strukturovaných objektů BLOB pomocí rozhraní REST API](search-semi-structured-data.md)
+ [Vytvoření kanálu pro rozšíření AI na portálu](cognitive-search-quickstart-blob.md)
+ [Vytvoření kanálu pro rozšíření AI vC#](cognitive-search-tutorial-blob-dotnet.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení indexeru objektů BLOB](search-howto-indexing-azure-blob-storage.md) 
