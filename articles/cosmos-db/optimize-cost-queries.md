---
title: Optimalizace nákladů a RU/s pro spouštění dotazů v Azure Cosmos DB
description: Zjistěte, jak vyhodnotit jednotkové poplatky za dotaz a optimalizovat dotaz z hlediska výkonu a nákladů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445134"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na dotazování ve službě Azure Cosmos DB

Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů, které pracují s položkami v rámci kontejneru. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo posuzování a správy hardwarových prostředků můžete jako jedno opatření pro prostředky požadované k provádění různých databázových operací při plnění požadavku použít jednotku žádosti (RU). Tento článek popisuje, jak vyhodnotit poplatky za jednotky žádostí pro dotazy a jak optimalizovat dotazy z hlediska výkonu a nákladů. 

Dotazy v Azure Cosmos DB se obvykle seřazují od nejrychlejších a nejefektivnějších po pomalejší a méně efektivní z hlediska propustnosti následujícím způsobem:  

* GET operace na jeden klíč oddílu a klíč položky.

* Dotaz s klauzulí filtru v rámci jednoho klíče oddílu.

* Dotaz bez rovnosti nebo rozsah filtr klauzule na libovolnou vlastnost.

* Dotaz bez filtrů.

Dotazy, které čtou data z jednoho nebo více oddílů, mají vyšší latenci a spotřebovávají vyšší počet jednotek požadavků. Vzhledem k tomu, že každý oddíl má automatické indexování pro všechny vlastnosti, dotaz může být obsluhován efektivně z indexu. Můžete provádět dotazy, které používají více oddílů rychleji pomocí možnosti paralelismu. Další informace o dělení a klíče oddílů najdete [v tématu dělení v Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Vyhodnocení jednotkové poplatky požadavku na dotaz

Jakmile uložíte některá data do kontejnerů Azure Cosmos, můžete použít Průzkumníka dat na webu Azure Portal k vytvoření a spuštění dotazů. Můžete také získat náklady na dotazy pomocí průzkumníka dat. Tato metoda vám poskytne představu o skutečných poplatcích spojených s typickými dotazy a operacemi, které váš systém podporuje.

Můžete také získat náklady na dotazy programově pomocí sad SDK. Chcete-li měřit režii jakékoli operace, jako `x-ms-request-charge` je například vytvořit, aktualizovat nebo odstranit zkontrolujte záhlaví při použití rozhraní REST API. Pokud používáte .NET nebo Java SDK, `RequestCharge` vlastnost je ekvivalentní vlastnost získat poplatek za požadavek a tato vlastnost je k dispozici v rámci ResourceResponse nebo FeedResponse.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Faktory ovlivňující jednotkový poplatek požadavku na dotaz

Jednotky požadavků na dotazy jsou závislé na řadě faktorů. Například počet položek Azure Cosmos načtených nebo vrácených, počet vyhledávání proti indexu, čas kompilace dotazu atd. Azure Cosmos DB zaručuje, že stejný dotaz při spuštění na stejných datech bude vždy spotřebovávat stejný počet jednotek požadavků i při opakovaných spuštěních. Profil dotazu pomocí metriky spuštění dotazu poskytuje dobrou představu o tom, jak jsou jednotky požadavku vynakládány.  

V některých případech se může zobrazit posloupnost 200 a 429 odpovědí a jednotky požadavků proměnných v stránkovaném provádění dotazů, to je proto, že dotazy budou spuštěny co nejrychleji na základě dostupných ru. Může se zobrazit rozdělení spuštění dotazu do více stránek/zpátečních cest mezi serverem a klientem. Například 10 000 položek může být vráceno jako více stránek, z nichž každá je účtována na základě výpočtu provedeného pro tuto stránku. Při součtu mezi těmito stránkami, měli byste získat stejný počet ru jako by získat pro celý dotaz.  

## <a name="metrics-for-troubleshooting"></a>Metriky pro řešení potíží

Výkon a propustnost spotřebované dotazy, uživatelem definované funkce (UD) většinou závisí na těle funkce. Nejjednodušší způsob, jak zjistit, kolik času spuštění dotazu je vynaloženo v UDF a počet spotřebovaných ru, je povolením metriky dotazu. Pokud používáte sdsad .NET, zde jsou ukázkové metriky dotazu vrácené sadou SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Doporučené postupy pro optimalizaci nákladů dotazů 

Při optimalizaci dotazů na náklady zvažte následující doporučené postupy:

* **Colocate více typů entit**

   Pokuste se colocate více typů entit v rámci jednoho nebo menšího počtu kontejnerů. Tato metoda poskytuje výhody nejen z hlediska cen, ale také pro provádění dotazů a transakce. Dotazy jsou vymezeny na jeden kontejner; a atomické transakce přes více záznamů prostřednictvím uložené procedury/aktivační události jsou vymezeny na klíč oddílu v rámci jednoho kontejneru. Colocating entity v rámci stejného kontejneru můžete snížit počet síťových zpátečních cest k vyřešení vztahů mezi záznamy. Tak zvyšuje výkon začátku do konce, umožňuje atomické transakce přes více záznamů pro větší datové sady a v důsledku toho snižuje náklady. Pokud colocating více typů entit v rámci jednoho nebo menšího počtu kontejnerů je obtížné pro váš scénář, obvykle proto, že migrujete existující aplikace a nechcete provádět žádné změny kódu - pak byste měli zvážit zřizování propustnost na úrovni databáze.  

* **Měření a ladění pro použití s nižšími požadavky/sekunda**

   Složitost dotazu má vliv na to, kolik jednotek požadavku (RU) jsou spotřebovány pro operaci. Počet predikátů, povaha predikátů, počet UDf a velikost zdrojové datové sady. Všechny tyto faktory ovlivňují náklady na operace dotazu. 

   Poplatek za požadavek vrácený v hlavičce požadavku označuje náklady na daný dotaz. Například pokud dotaz vrátí 1000 1-KB položky, náklady na operaci je 1000. Jako takové během jedné sekundy server respektuje pouze dva takové požadavky před omezením rychlosti následné požadavky. Další informace naleznete v článku [jednotky požadavku](request-units.md) a kalkulačku jednotky požadavku. 

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [tom, jak fungují ceny Azure Cosmos](how-pricing-works.md)
* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vaší faktury z DB Služby Azure Cosmos](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos s více oblastmi](optimize-cost-regions.md)
* Další informace o [rezervované kapacitě Azure Cosmos DB](cosmos-db-reserved-capacity.md)

