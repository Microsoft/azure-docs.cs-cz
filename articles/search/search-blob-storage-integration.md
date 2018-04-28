---
title: Přidávání do úložiště objektů Blob Azure Search | Microsoft Docs
description: Vytvořte index v kódu pomocí rozhraní HTTP REST API Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/04/2017
author: chaosrealm
manager: jlembicz
ms.author: eugenesh
ms.openlocfilehash: 71e43920f0e6a64beb7cdb28d0707dd30502bf05
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="searching-blob-storage-with-azure-search"></a>Prohledávání služby Blob Storage pomocí Azure Search

Hledání mezi různé typy obsahu, které jsou uložené v úložišti objektů Blob v Azure může být obtížné problém vyřešit. Můžete však indexu a vyhledávat obsah objektů BLOB v pouhých několika kliknutích pomocí Azure Search. Hledání v úložišti objektů Blob vyžaduje zřízení služby Azure Search. Různé limity služby a cenových úrovní Azure Search najdete na [stránce s cenami](https://aka.ms/azspricing).

## <a name="what-is-azure-search"></a>Co je Azure Search?
[Služba Azure Search](https://aka.ms/whatisazsearch) je služba vyhledávání, která usnadňuje vývojářům přidat vyskytne robustní fulltextové vyhledávání pro webové a mobilní aplikace. Jako služba Azure Search eliminuje nutnost ke správě infrastruktury žádné vyhledávání při nabídky [99,9 % dostupnost SLA](https://aka.ms/azuresearchsla).

## <a name="index-and-search-enterprise-document-formats"></a>Formáty dokumentu enterprise index a vyhledávání
S podporou [dokumentu extrakce](https://aka.ms/azsblobindexer) v úložišti objektů Blob v Azure, mohou indexu následující obsah:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

Extrahováním metadat a text z těchto typů souborů, můžete hledat ve více formátech souborů s jeden dotaz. 

## <a name="search-through-your-blob-metadata"></a>Hledání v metadata objektu blob
Běžný scénář, který usnadňuje řazení prostřednictvím objektů BLOB s libovolným typem obsahu je index vlastních metadat a vlastnosti systému pro každý objekt blob. Tímto způsobem je indexovaný informace pro všechny objekty BLOB bez ohledu na typ dokumentu. Pak můžete pokračovat řazení, filtru a omezující vlastnost napříč veškerý obsah úložiště objektů Blob.

[Podívejte se, že informace o indexování blob metadat.](https://aka.ms/azsblobmetadataindexing)

## <a name="image-search"></a>Obrázek hledání
Fulltextové vyhledávání, Fasetové navigace a řazení možnosti Azure Search je teď použít pro metadata ukládají do objektů BLOB bitové kopie.

Pokud tyto Image jsou předem zpracované pomocí [počítače vize API](https://www.microsoft.com/cognitive-services/computer-vision-api) z kognitivní služby společnosti Microsoft, je možné indexování visual najít v každé bitové kopie, včetně rozpoznávání rozpoznávání znaků a ručně psaného obsahu. Pracujeme na přidání rozpoznávání znaků a další možnosti zpracování bitovou kopii přímo do služby Azure Search, pokud vás zajímá v těchto funkcí, odešlete žádost na našem [UserVoice](https://aka.ms/azsuv) nebo [e-mailu nám](mailto:azscustquestions@microsoft.com).

## <a name="index-and-search-through-json-blobs"></a>Rejstřík a vyhledávání prostřednictvím objekty BLOB JSON
Vyhledávání systému Azure se dá nakonfigurovat k extrakci strukturovaných obsah nalezen do objektů BLOB, které obsahují JSON. Služba Azure Search může číst objekty BLOB JSON a analyzovat strukturovaných obsah do příslušných polí dokument Azure Search. Vyhledávání systému Azure můžete taky využít objekty BLOB, které obsahují pole objektů JSON a mapování každý prvek na samostatný dokument Azure Search.

Analýza JSON není aktuálně lze změnit v nastavení portálu. [Další informace o analýze JSON ve službě Azure Search.](https://aka.ms/azsjsonblobindexing)

## <a name="quick-start"></a>Rychlý start
Služba Azure Search lze přidat na objekty BLOB přímo ze stránky portálu úložiště objektů Blob.

![](./media/search-blob-storage-integration/blob-blade.png)

Klikněte na tlačítko **přidat Azure Search** spustíte toku, kde můžete vybrat existující službu Azure Search nebo vytvořit novou službu. Pokud vytvoříte novou službu, jsou navigaci z prostředí portálu účtu úložiště. Můžete přejít zpět na stránku portálu úložiště a znovu vyberte **přidat Azure Search** možnost, kde můžete vybrat existující službu.

### <a name="next-steps"></a>Další kroky
Další informace o indexeru pro hledání objektů Blob Azure v úplném [dokumentaci](https://aka.ms/azsblobindexer).
