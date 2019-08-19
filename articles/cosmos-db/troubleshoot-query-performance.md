---
title: Diagnostika a řešení potíží s dotazy při použití Azure Cosmos DB
description: Naučte se identifikovat, diagnostikovat a řešit potíže s Azure Cosmos DB problémy s dotazy SQL.
author: ginamr
ms.service: cosmos-db
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: girobins
ms.subservice: cosmosdb-sql
ms.reviewer: sngun
ms.openlocfilehash: d0dd9a371c4912cae0e74b214c673c629fc1ff55
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515810"
---
# <a name="troubleshoot-query-performance-for-azure-cosmos-db"></a>Řešení potíží s výkonem dotazů pro Azure Cosmos DB
Tento článek popisuje, jak identifikovat, diagnostikovat a řešit potíže s Azure Cosmos DB problémy s dotazy SQL. Chcete-li dosáhnout optimálního výkonu pro Azure Cosmos DB dotazy, postupujte podle následujících kroků pro řešení potíží. 

## <a name="collocate-clients-in-same-azure-region"></a>Společné umístění klienti ve stejné oblasti Azure 
Nejnižší možná latence se dosahuje tím, že se zaručí, že se volající aplikace nachází ve stejné oblasti Azure jako koncový bod zřízené Azure Cosmos DB. Seznam oblastí, které jsou k dispozici, najdete v článku [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/#services) .

## <a name="check-consistency-level"></a>Kontrola úrovně konzistence
[Úroveň konzistence](consistency-levels.md) může mít vliv na výkon a poplatky. Ujistěte se, že vaše úroveň konzistence je pro daný scénář vhodná. Další podrobnosti najdete v tématu [Výběr úrovně konzistence](consistency-levels-choosing.md).

## <a name="log-the-executed-sql-query"></a>Protokolovat spuštěný dotaz SQL 

Spuštěný dotaz SQL můžete protokolovat v účtu úložiště nebo v tabulce diagnostického protokolu. [Protokoly dotazů SQL prostřednictvím diagnostických protokolů](logging.md#turn-on-logging-in-the-azure-portal) umožňují protokolovat zavedený dotaz do účtu úložiště podle vašeho výběru. To vám umožní podívat se na protokoly a najít dotaz, který používá vyšší ru. Později můžete použít ID aktivity tak, aby odpovídalo skutečnému dotazu v QueryRuntimeStatistics. Dotaz je zakódován z důvodu zabezpečení a názvů parametrů dotazu a jejich hodnoty v klauzulích WHERE se liší od skutečných názvů a hodnot. Můžete použít protokolování do účtu úložiště, abyste zachovali dlouhodobé uchovávání provedených dotazů.  

## <a name="log-query-metrics"></a>Metriky dotazů protokolu

Slouží `QueryMetrics` k řešení potíží s pomalými nebo nákladnými dotazy. 

  * Nastavte `FeedOptions.PopulateQueryMetrics = true`vodpovědi. `QueryMetrics`
  * `QueryMetrics`Třída má přetíženou `.ToString()` funkci, kterou lze vyvolat pro získání řetězcové `QueryMetrics`reprezentace. 
  * Metriky je možné využít k odvození následujících přehledů, mimo jiné: 
  
      * Zda některé konkrétní součásti kanálu dotazů trvaly neobvykle dlouho (v pořadí stovek milisekund nebo více). 

          * Podívejte se `TotalExecutionTime`na.
          * `TotalExecutionTime` V případě, že je dotaz menší než čas posledního spuštění, je čas strávený na straně klienta nebo v síti. Překontrolujte, jestli se společně umístěného klient a oblast Azure.
      
      * Zda v analyzovaných dokumentech byly falešně pozitivní (Pokud je počet výstupních dokumentů mnohem menší než počet načtených dokumentů).  

          * Podívejte se `Index Utilization`na.
          * `Index Utilization`= (Počet vrácených dokumentů/počet načtených dokumentů)
          * Pokud je počet vrácených dokumentů mnohem menší než počet načtený, bude analyzováno falešně pozitivních hodnot.
          * Omezte počet dokumentů, které jsou načítány pomocí užších filtrů.  

      * Jak jednotlivé zpáteční cesty fared (viz `Partition Execution Timeline` z řetězcové `QueryMetrics`reprezentace). 
      * Určuje, zda dotaz využil požadavek na vysokou žádost. 

Další podrobnosti najdete v článku [o tom, jak získat metriky spouštění dotazů SQL](profile-sql-api-query.md) .
      
## <a name="tune-query-feed-options-parameters"></a>Ladění parametrů možností kanálu dotazů 
Výkon dotazů je možné ladit přes parametry [možností kanálu](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.feedoptions?view=azure-dotnet) požadavku. Zkuste nastavit následující možnosti:

  * Nejprve `MaxDegreeOfParallelism` nastavte hodnotu-1 a potom porovnejte výkon napříč různými hodnotami. 
  * Nejprve `MaxBufferedItemCount` nastavte hodnotu-1 a potom porovnejte výkon napříč různými hodnotami. 
  * Nastavte `MaxItemCount` na hodnotu-1.

Při porovnávání výkonu pro různé hodnoty zkuste například hodnoty 2, 4, 8, 16 atd.
 
## <a name="read-all-results-from-continuations"></a>Čtení všech výsledků z pokračování
Pokud si myslíte, že se vám nezobrazují všechny výsledky, ujistěte se, že jste úplně vyprázdnili pokračování. Jinými slovy, pokračujete ve čtení výsledků, dokud token pro pokračování poskytuje další dokumenty.

Úplného vyprázdnění je možné dosáhnout jedním z následujících způsobů:

  * Pokračovat ve zpracování výsledků při pokračování není prázdné.
  * Pokračovat ve zpracování, zatímco dotaz má více výsledků. 

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

## <a name="choose-system-functions-that-utilize-index"></a>Zvolte systémové funkce využívající index.
Pokud je výraz možné přeložit na rozsah řetězcových hodnot, znamená to, že může využívat index, jinak ne. 

Tady je seznam řetězcových hodnot, které můžou využívat index: 
    
  * STARTSWITH(str_expr, str_expr) 
  * LEFT(str_expr, num_expr) = str_expr 
  * SUBSTRING(str_expr, num_expr, num_expr) = str_expr, ale pouze pokud má první parametr num_expr hodnotu 0 
    
    Tady je několik příkladů dotazů: 
    
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

  * Vyhněte se systémovým funkcím v filtru (nebo v klauzuli WHERE), které nejsou obsluhovány indexem. Mezi příklady takových systémových funkcí patří například obsahuje, Velká a malá písmena.
  * Pokud je to možné, pište dotazy tak, aby používaly filtr klíče oddílu.
  * Chcete-li dosáhnout provádění dotazů, vyhněte se volání hodnoty horní/nižší ve filtru. Místo toho Normalizujte velká a malá písmena hodnot při vložení. Pro každou z hodnot vložte hodnotu s požadovaným písmenem nebo vložte původní hodnotu a hodnotu s požadovaným velikostí písmen. 

    Příklad:
    
    ```sql

    SELECT * FROM c WHERE UPPER(c.name) = "JOE"

    ```
    
    V tomto případě uložte "Jana" velkými písmeny nebo uložte obě "Jana" původní hodnotu a "Jana". 
    
    Pokud jsou data v datovém formátu JSON normalizována, bude dotaz:
    
    ```sql

    SELECT * FROM c WHERE c.name = "JOE"

    ```

    Druhý dotaz bude více proveden, protože nevyžaduje transformaci každé hodnoty za účelem porovnání hodnot s názvem "Jana".

Další podrobnosti o systémové funkci najdete v článku [systémové funkce](sql-query-system-functions.md) .

## <a name="check-indexing-policy"></a>Vyhledat zásady indexování
Pokud chcete ověřit, že aktuální [zásady indexování](index-policy.md) jsou optimální:

  * Zajistěte, aby všechny cesty JSON použité v dotazech byly zahrnuty v zásadách indexování pro rychlejší čtení.
  * Vylučte cesty nepoužívané v dotazech pro provádění dalších operací zápisu.

Další podrobnosti najdete v článku [Správa zásad indexování](how-to-manage-indexing-policy.md) .

## <a name="spatial-data-check-ordering-of-points"></a>Prostorová data: Kontrolovat řazení bodů
Body v rámci mnohoúhelníku musí zadat v pořadí proti směru hodinových ručiček. Mnohoúhelník zadat v pořadí po směru hodinových ručiček představuje inverzní oblasti v rámci něj.

## <a name="optimize-join-expressions"></a>Optimalizovat výrazy JOIN
`JOIN`výrazy se můžou rozšířit na velké mezi produkty. Pokud je to možné, dotazování na menší místo pro hledání prostřednictvím užšího filtru.

Podhodnoty poddotazů mohou optimalizovat `JOIN` výrazy vložením predikátů za každým výrazem Select-many, nikoli po všech křížových spojeních `WHERE` v klauzuli. Podrobný příklad najdete v článku věnovaném [optimalizaci výrazů spojení](https://docs.microsoft.com/azure/cosmos-db/sql-query-subquery#optimize-join-expressions) .

## <a name="optimize-order-by-expressions"></a>Optimalizovat pořadí podle výrazů 
`ORDER BY`Pokud jsou pole zhuštěná nebo nejsou zahrnutá v zásadách indexu, může dojít k výkonu dotazů.

  * Pro zhuštěná pole, jako je například čas, zmenšete hledaný prostor co nejvíce s filtry. 
  * V případě jedné `ORDER BY`vlastnosti uveďte vlastnost include v zásadách indexování. 
  * Pro více výrazů `ORDER BY` vlastností definujte [složený index](https://docs.microsoft.com/azure/cosmos-db/index-policy#composite-indexes) pro pole, která jsou seřazená.  

## <a name="many-large-documents-being-loaded-and-processed"></a>Načítá a zpracovává se mnoho velkých dokumentů.
Čas a ru, které jsou vyžadovány dotazem, nejsou závislé na velikosti odpovědi, jsou také závislé na práci, kterou provádí kanál zpracování dotazů. Čas a ru se proporcionálně zvyšují s množstvím práce, kterou provádí celý kanál zpracování dotazů. U rozsáhlých dokumentů je prováděno více práce, což znamená více času a ru se vyžaduje pro načtení a zpracování velkých dokumentů.

## <a name="low-provisioned-throughput"></a>Nízká zajištěná propustnost
Zajistěte, aby zajištěná propustnost mohla zpracovávat úlohy. Zvyšte rozpočet RU pro ovlivněné kolekce.

## <a name="try-upgrading-to-the-latest-sdk-version"></a>Zkuste upgradovat na nejnovější verzi sady SDK.
Informace o tom, jak zjistit nejnovější sadu SDK, najdete v článku [Stažení sady SDK a poznámky k verzi](sql-api-sdk-dotnet.md) .

## <a name="next-steps"></a>Další kroky
Další informace o tom, jak měřit ru na dotaz, získat statistiku spouštění a vyladit dotazy a další informace:

* [Získání metrik spouštění dotazů SQL pomocí sady .NET SDK](profile-sql-api-query.md)
* [Ladění výkonu dotazů pomocí služby Azure Cosmos DB](sql-api-sql-query-metrics.md)
* [Tipy ke zvýšení výkonu pro sadu .NET SDK](performance-tips.md)
