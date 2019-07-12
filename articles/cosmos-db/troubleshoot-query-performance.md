---
title: Diagnostika a řešení potíží s dotazů při použití služby Azure Cosmos DB
description: Zjistěte, jak k identifikaci, diagnostice a řešení potíží s Azure Cosmos DB SQL dotazu.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: e2c6c9d22672028d7af547235c6fcd1738654828
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811216"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Řešení potíží s výkonem dotazů pro službu Azure Cosmos DB
Tento článek popisuje, jak k identifikaci, diagnostice a řešení potíží s Azure Cosmos DB SQL dotazu. Pokud chcete dosáhnout optimálního výkonu za dotazy na službu Azure Cosmos DB, použijte následující postup řešení potíží. 

## <a name="collocate-clients-in-same-azure-region"></a>Společné umístění klienty ve stejné oblasti Azure 
Nejnižší možnou latenci se dosahuje tím, že zajišťuje, že je volající aplikace umístěné ve stejné oblasti Azure jako koncový bod zřízené služby Azure Cosmos DB. Seznam dostupných oblastí naleznete v tématu [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) článku.

## <a name="check-consistency-level"></a>Zkontrolujte úroveň konzistence
Úroveň konzistence může ovlivnit výkon a náklady. Zajistěte, aby že úroveň konzistence je vhodný pro daný scénář. Další podrobnosti najdete v tématu [Volba úrovně konzistence](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-choosing).

## <a name="log-query-metrics"></a>Protokolujte metriky dotazu
Použití `QueryMetrics` řešení potíží s dotazy pomalé nebo nákladné. 

  * Nastavte `FeedOptions.PopulateQueryMetrics = true` mít `QueryMetrics` v odpovědi.
  * `QueryMetrics` Třída má přetížený `.ToString()` funkce, která může být vyvolána k získání řetězcové vyjádření `QueryMetrics`. 
  * Metriky můžete využít k odvození tyto přehledy, mimo jiné: 
  
      * Určuje, zda všechny konkrétní součást kanálu dotazu neobvyklým způsobem dokončení trvalo dlouho (v pořadí od stovek milisekund nebo více). 

          * Podívejte se na `TotalExecutionTime`.
          * Pokud `TotalExecutionTime` dotazu je menší než čas spuštění kompletního a pak je právě doba trvání na straně klienta nebo sítě. Pečlivě zkontrolujte, že jsou seřazena klienta a oblastí Azure.
      
      * Zda byly v dokumentech počet falešně pozitivních výsledků analyzovat (Pokud počet dokumentů výstup je mnohem menší než počet dokumentů načíst).  

          * Podívejte se na `Index Utilization`.
          * `Index Utilization` = (Počet vrácených dokumentů / bylo načteno množství dokumenty)
          * Pokud počet vrácených dokumentů je mnohem menší než počet načtení, jsou probíhá analýza počet falešně pozitivních výsledků.
          * Omezte počet dokumentů, které načítají s užší filtry.  

      * Využití jednotlivých opakované fared (najdete v článku `Partition Execution Timeline` z řetězcové reprezentace `QueryMetrics`). 
      * Určuje, zda dotaz spotřebované zátěž vysokou žádostí. 

Další podrobnosti najdete v tématu [získání metriky spouštění dotazů SQL](https://docs.microsoft.com/azure/cosmos-db/profile-sql-api-query) článku.
      
## <a name="tune-query-feed-options-parameters"></a>Vyladění parametry kanálu možnosti dotazu 
Výkon dotazů můžete ladit prostřednictvím požadavku [možnosti datového kanálu](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) parametry. Zkuste níže uvedených možností:

  * Nastavte `MaxDegreeOfParallelism` na hodnotu-1 první a potom porovnejte výkon napříč různými hodnotami. 
  * Nastavte `MaxBufferedItemCount` na hodnotu-1 první a potom porovnejte výkon napříč různými hodnotami. 
  * Nastavte `MaxItemCount` na hodnotu -1.

Při porovnání výkonu různých hodnot, zkuste hodnoty, jako je 2, 4, 8, 16 a dalších.
 
## <a name="read-all-results-from-continuations"></a>Číst všechny výsledky z pokračování
Pokud se domníváte, že se že vám nedaří získat všechny výsledky, ujistěte se, že plně vyprázdnit pokračování. Jinými slovy zachovejte čtení výsledků, zatímco token pro pokračování má více dokumentů výnosu.

Plně vyprazdňování lze nastavit pomocí některé z následujících vzorů:

  * Pokračujte zpracování výsledků při pokračování není prázdný.
  * Pokračujte ve zpracovávání, když má dotaz víc výsledků. 

    ```csharp
    // using AsDocumentQuery you get access to whether or not the query HasMoreResults
    // If it does, just call ExecuteNextAsync until there are no more results
    // No need to supply a continuation token here as the server keeps track of progress
    var query = client.CreateDocumentQuery<Family>(collectionLink, options).AsDocumentQuery();
    while (query.HasMoreResults)
    {
        foreach (Family family in await query.ExecuteNextAsync())
        {
            families.Add(family);
        }
    }
    ```

## <a name="choose-system-functions-that-utilize-index"></a>Zvolte systémové funkce, které využívají indexu
Pokud výraz lze přeložit do rozsahu hodnot řetězce, pak jej mohou využívat index; v opačném případě ji nelze. 

Tady je seznam funkce řetězce, které mohou využívat index: 
    
  * STARTSWITH (str_expr, str_expr) 
  * LEFT (str_expr, num_expr) = str_expr 
  * Dílčí řetězec (str_expr, num_expr num_expr.) = str_expr, ale pouze pokud je první num_expr 0 
    
    Tady je několik příkladů dotazu: 
    
    ```sql

    -- If there is a range index on r.name, STARTSWITH will utilize the index while ENDSWITH won't 
    SELECT * 
    FROM c 
    WHERE STARTSWITH(c.name, 'J') AND ENDSWITH(c.name, 'n')

    ```
    
    ```sql

    -- LEFT will utilize the index while RIGHT won't 
    SELECT * 
    FROM c 
    WHERE LEFT(c.name, 2) = 'Jo' AND RIGHT(c.name, 2) = 'hn'

    ```

  * Vyhněte se funkce systému ve filtru (ani v klauzuli WHERE), která nejsou obsluhovány podle indexu. Mezi příklady těchto funkcí systému patří obsahuje, horní, dolní.
  * Pokud je to možné, pište dotazy tak, aby používaly filtr klíče oddílu.
  * K dosažení výkonné dotazy Vyhněte se volání velká a malá ve filtru. Místo toho normalizujte malá a velká písmena hodnot při vložení. Pro jednotlivé hodnoty vložit hodnotu s požadovanou velikostí písmen nebo vložit původní hodnota a hodnota se požadované velká a malá písmena. 

    Příklad:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    V takovém případě v úložišti "Jan" velkými písmeny nebo uložit původní hodnotu "Jan" a "Jan". 
    
    Normalizovaná JSON data velká a malá písmena stane dotazu:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    Druhý dotaz bude výkonnější jako nevyžaduje provádění transformací na všechny hodnoty, aby bylo možné porovnat hodnoty, které mají "Jan".
   
## <a name="check-indexing-policy"></a>Zkontrolujte zásady indexování
Chcete-li ověřit, zda aktuální [zásady indexování]((https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy)) optimální:

  * Ujistěte se, že jsou všechny cesty JSON v dotazech použít pro rychlejší čtení součástí zásady indexování.
  * Vylučte cesty není možné použít v dotazech u další zápisy výkonné.

## <a name="spatial-data-check-ordering-of-points"></a>Prostorová data: Zkontrolujte řazení bodů
Body v rámci mnohoúhelníku musí zadat v pořadí proti směru hodinových ručiček. Mnohoúhelník zadat v pořadí po směru hodinových ručiček představuje inverzní oblasti v rámci něj.

## <a name="optimize-join-expressions"></a>Optimalizace spojení výrazů
`JOIN` výrazy můžete rozšířit do velké mezi produkty. Při nejbližším dotazu na menší hledání prostoru prostřednictvím užším filtru.

Poddotazy s více hodnotami lze optimalizovat `JOIN` výrazy formou predikáty po každý výraz select: n, nikoli po všechny křížové spojení v `WHERE` klauzuli. Podrobný příklad naleznete v tématu [optimalizaci spojení výrazů](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) článku.

## <a name="optimize-order-by-expressions"></a>Optimalizace výrazy ORDER BY 
`ORDER BY` výkon dotazů může být velmi krátká, pokud jsou tato pole zhuštěných nebo není součástí zásad indexu.

  * Pro sadu zhuštěných pole například čas snížit co nejvíc prostoru pro hledání s filtry. 
  * Pro jednu vlastnost `ORDER BY`, patří vlastnost v zásadách indexu. 
  * Pro více vlastnost `ORDER BY` výrazy, definovat [složeném indexu](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) polí seřazený.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Načítání a zpracování velkého objemu dokumentů
Čas a rezervovaných jednotek, které jsou vyžadované dotaz pouze nejsou závislé na velikost odpovědi, jsou také závisí na práci, která probíhá v kanálu zpracování dotazu. Čas a ru proporcionálně zvýšit množství práce provedené celý dotaz zpracování kanálu. Více práce se provádí pro velké dokumenty, takže budou muset načíst a zpracovat velké dokumenty více času a RU.

## <a name="low-provisioned-throughput"></a>Nízká zřízená propustnost
Zkontrolujte, že zřízená propustnost může zpracovávat úlohy. Zvyšte rozpočtu RU pro ovlivněné kolekce.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Zkuste ho upgradovat na nejnovější verzi sady SDK
Chcete-li zjistit nejnovější sady SDK najdete [stažení sady SDK a release notes](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) článku.

## <a name="recommended-documents"></a>Doporučené dokumenty
Podívejte se na tom, jak měřit ru každý dotaz, získat statistiky provádění vyladit vaše dotazy a další dokumenty:

* [Získat metriky spouštění dotazů SQL pomocí sady .NET SDK](https://docs.microsoft.com/azure/cosmos-db/profile-sql-api-query)
* [Ladění výkonu dotazů pomocí služby Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query-metrics)
* [Informační kanál možnosti pro sadu .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet)
* [Zásady indexování](https://docs.microsoft.com/azure/cosmos-db/indexing-policies)
* [Jak spravovat zásady indexování](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-indexing-policy)
* [Úrovně konzistence – přehled](https://docs.microsoft.com/azure/cosmos-db/consistency-levels)
* [Volba úrovně konzistence](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-choosing)
* [Tipy ke zvýšení výkonu pro sadu .NET SDK](https://docs.microsoft.com/azure/cosmos-db/performance-tips)
* [Začínáme s dotazy SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-sql-query)
* [Nejnovější sadu SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet)
