---
title: Indexování dokumentů v Azure Data Lake Storage Gen2 (Náhled)
titleSuffix: Azure Cognitive Search
description: Naučte se indexovat obsah a metadata v Azure Data Lake Storage Gen2. Tato funkce je aktuálně ve verzi Public Preview.
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 390f45ce874e4e64d3a7b9a6c3944983e0ba9028
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719917"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indexování dokumentů v Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Podpora Azure Data Lake Storage Gen2 je v současnosti ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview)můžete požádat o přístup k náhledům. Tato funkce poskytuje [REST API verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.


Při nastavování účtu úložiště Azure máte možnost povolit [hierarchický obor názvů](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Díky tomu může být kolekce obsahu v účtu uspořádána do hierarchie adresářů a vnořených podadresářů. Povolením hierarchického oboru názvů povolíte [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Tento článek popisuje, jak začít s indexováním dokumentů, které jsou v Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Nastavení Azure Data Lake Storage Gen2 indexeru

K indexování obsahu z Data Lake Storage Gen2 je potřeba provést několik kroků.

### <a name="step-1-sign-up-for-the-preview"></a>Krok 1: Zaregistrujte si verzi Preview

Zaregistrujte se do služby Data Lake Storage Gen2 indexer Preview vyplněním [tohoto formuláře](https://aka.ms/azure-cognitive-search/indexer-preview). Po přijetí do verze Preview obdržíte e-mail s potvrzením.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Krok 2: postup při instalaci indexování služby Azure Blob Storage

Jakmile obdržíte potvrzení, že se vaše verze Preview úspěšně přihlásila, jste připraveni vytvořit kanál indexování.

Obsah a metadata z Data Lake Storage Gen2 můžete indexovat pomocí [REST API verze 2019-05-06-Preview](search-api-preview.md). V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

Indexování obsahu v Data Lake Storage Gen2 je stejné jako indexování obsahu v úložišti objektů BLOB v Azure. Proto si Pochopte, jak nastavit Data Lake Storage Gen2 zdroj dat, index a indexer, [v tématu Postup indexování dokumentů v azure BLOB Storage pomocí Azure kognitivní hledání](search-howto-indexing-azure-blob-storage.md). Článek o službě BLOB Storage také poskytuje informace o tom, jaké formáty dokumentů jsou podporovány, jaké vlastnosti metadat objektů BLOB jsou extrahovány, přírůstkové indexování a další. Tyto informace budou pro Data Lake Storage Gen2 stejné.

## <a name="access-control"></a>Řízení přístupu

Azure Data Lake Storage Gen2 implementuje [model řízení přístupu](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) , který podporuje řízení přístupu na základě role (RBAC) Azure a seznamy řízení přístupu (ACL) typu POSIX. Při indexování obsahu z Data Lake Storage Gen2 Azure Kognitivní hledání z obsahu neextrahuje informace o RBAC a seznamu ACL. V důsledku toho tyto informace nebudou zahrnuty do indexu služby Azure Kognitivní hledání.

Je-li udržování řízení přístupu u každého dokumentu v indexu důležité, je k implementaci [oříznutí zabezpečení](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search)k dispozici vývojář aplikace.