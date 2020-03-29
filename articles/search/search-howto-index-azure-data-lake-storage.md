---
title: Vyhledávání přes Azure Data Lake Storage Gen2 (preview)
titleSuffix: Azure Cognitive Search
description: Zjistěte, jak indexovat obsah a metadata v Azure Data Lake Storage Gen2. Tato funkce je v současné době ve verzi Public Preview.
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 4b725c8a1bf0649a640c02a9a1828ec9014d36d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905655"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexování dokumentů v Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Podpora Azure Data Lake Storage Gen2 je momentálně ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Přístup k náhledům můžete požádat vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje tuto funkci. V současné době neexistuje žádná podpora portálu nebo sady .NET SDK.


Při nastavování účtu úložiště Azure máte možnost povolit [hierarchický obor názvů](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). To umožňuje uspořádat shromažďování obsahu v účtu do hierarchie adresářů a vnořených podadresářů. Povolením hierarchického oboru názvů povolíte [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Tento článek popisuje, jak začít s indexováním dokumentů, které jsou v Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Nastavení indexeru Azure Data Lake Storage Gen2

Existuje několik kroků, které budete muset provést k indexování obsahu z Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Krok 1: Zaregistrujte se do náhledu

Zaregistrujte se k náhledu indexeru Data Lake Storage Gen2 vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Jakmile budete přijati do náhledu, obdržíte potvrzovací e-mail.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Krok 2: Postupujte podle kroků nastavení indexování úložiště objektů blob Azure

Jakmile obdržíte potvrzení, že registrace ve verzi Preview byla úspěšná, jste připraveni vytvořit kanál indexování.

Obsah a metadata můžete indexovat z modulu Gen2 úložiště datového jezera pomocí [rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md). V tuto chvíli neexistuje žádná podpora portálu nebo sady .NET SDK.

Indexování obsahu v úložišti datového jezera Gen2 je shodné s indexováním obsahu v úložišti objektů blob Azure. Takže pochopit, jak nastavit zdroj dat, index a indexer úložiště datového jezera Gen2, najdete v části [Jak indexovat dokumenty v azure blob storage s Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md). Článek úložiště objektů blob také obsahuje informace o tom, jaké formáty dokumentů jsou podporovány, jaké vlastnosti metadat objektu blob jsou extrahovány, přírůstkové indexování a další. Tyto informace budou stejné pro úložiště datového jezera Gen2.

## <a name="access-control"></a>Řízení přístupu

Azure Data Lake Storage Gen2 implementuje [model řízení přístupu,](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) který podporuje jak řízení přístupu na základě rolí Azure (RBAC) a posix-like seznamy řízení přístupu (ACLs). Při indexování obsahu z Data Lake Storage Gen2 Azure Cognitive Search nebude extrahovat Informace RBAC a ACL z obsahu. V důsledku toho tyto informace nebudou zahrnuty do indexu Azure Cognitive Search.

Pokud je důležité udržovat řízení přístupu u každého dokumentu v indexu, je na vývojáři aplikace, aby implementoval [oříznutí zabezpečení](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Zjišťování změn

Indexer Data Lake Storage Gen2 podporuje zjišťování změn. To znamená, že při spuštění indexeru pouze reindexuje změněné objekty `LastModified` BLOB podle časového razítka objektu blob.

> [!NOTE] 
> Data Lake Storage Gen2 umožňuje přejmenování adresářů. Při přejmenování adresáře se časová razítka pro objekty BLOB v tomto adresáři neaktualizují. V důsledku toho indexer nebude reindexovat tyto objekty BLOB. Pokud potřebujete objekty BLOB v adresáři, které mají být přeindexovány po přejmenování adresáře, `LastModified` protože nyní mají nové adresy URL, budete muset aktualizovat časové razítko pro všechny objekty BLOB v adresáři tak, aby indexer ví, že je přeindexovat během budoucího spuštění.
