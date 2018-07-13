---
title: Průzkumník služby Search pro dotazování indexů ve službě Azure Search | Dokumentace Microsoftu
description: Další informace o použití Průzkumníka služby Search pro dotazování indexů ve službě Azure Search.
manager: cgronlun
author: HeidiSteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: heidist
ms.openlocfilehash: 520d9e7b1899c54d922ff6fb77e0901f9609b029
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004129"
---
# <a name="how-to-use-search-explorer-to-query-indexes-in-azure-search"></a>Použití Průzkumníka služby Search na dotaz indexy ve službě Azure Search 

V tomto článku se dozvíte, jak zadávat dotazy na existující index Azure Search pomocí **Průzkumníka služby Search** na webu Azure Portal. Průzkumník služby Search můžete odesílat jednoduché nebo úplné řetězce dotazů Lucene do jakéhokoli existujícího indexu ve službě.

## <a name="open-the-service-dashboard"></a>Otevření řídicího panelu služby
1. Klikněte na **Všechny prostředky** na panelu odkazů na levé straně webu [Azure Portal](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).
2. Vyberte službu Azure Search.

## <a name="select-an-index"></a>Výběr indexu

Na dlaždici **Indexy** vyberte index, který chcete prohledat.

   ![](./media/search-explorer/pick-index.png)

## <a name="open-search-explorer"></a>Otevřete Průzkumníka služby Search

Klikněte na dlaždici Průzkumník služby Search otevřete vysouvací panel hledání a podokno výsledků.

   ![](./media/search-explorer/search-explorer-tile.png)

## <a name="start-searching"></a>Spusťte hledání

Pokud používáte Průzkumník služby Search, můžete určit [parametrů dotazu](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) dotaz formulovat.

1. V části **Řetězec dotazu** zadejte dotaz a potom stiskněte **Hledat**. 

   Řetězec dotazu se automaticky parsuje do správné adresy URL žádosti za účelem odeslání žádosti HTTP do rozhraní REST API služby Azure Search.   
   
   K vytvoření žádosti můžete použít jakoukoli platnou syntaxi jednoduchého nebo úplného dotazu Lucene. Znak `*` je ekvivalentní prázdnému nebo nespecifikovanému hledání, které vrátí všechny dokumenty bez zvláštního pořadí.

2. V části **Výsledky** jsou výsledky dotazu uvedené ve stejném nezpracovaném formátu JSON jako datová část vrácená v textu odpovědi HTTP při vydávání žádostí prostřednictvím kódu programu.

   ![](./media/search-explorer/search-bar.png)

## <a name="next-steps"></a>Další postup

V následujících zdrojích najdete další informace o syntaxi dotazů a příklady.

 + [Jednoduchá syntaxe dotazů](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) 
 + [Syntaxe dotazů Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search) 
 + [Příklady syntaxe dotazů Lucene](https://docs.microsoft.com/azure/search/search-query-lucene-examples) 
 + [Syntaxe výrazů filtru OData](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) 