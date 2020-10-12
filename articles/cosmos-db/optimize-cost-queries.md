---
title: Optimalizujte náklady a RU/s a spouštějte dotazy v Azure Cosmos DB
description: Naučte se vyhodnocovat poplatky za jednotky požadavků na dotaz a optimalizovat dotaz z hlediska výkonu a nákladů.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: c6ff67e3c1588f6671173d6fa1eec3dc15883291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020911"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na dotazování ve službě Azure Cosmos DB

Azure Cosmos DB nabízí bohatou sadu databázových operací, včetně relačních a hierarchických dotazů, které pracují s položkami v rámci kontejneru. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo posuzování a správy hardwarových prostředků můžete jako jedno opatření pro prostředky požadované k provádění různých databázových operací při plnění požadavku použít jednotku žádosti (RU). Tento článek popisuje, jak vyhodnotit poplatky za jednotky žádostí pro dotazy a jak optimalizovat dotazy z hlediska výkonu a nákladů.

Čtení v Azure Cosmos DB jsou obvykle seřazené od nejrychlejší/nejefektivnější až po pomalejší/méně efektivní z hlediska propustnosti, jak je znázorněno níže:  

* Čtení bodů (vyhledávání klíč/hodnota na jednom ID položky a klíči oddílu).

* Dotaz s klauzulí Filter v rámci jednoho klíče oddílu

* Dotaz bez klauzule filtru rovnosti nebo rozsahu u jakékoli vlastnosti

* Dotaz bez filtrů

Vzhledem k tomu, že vyhledávání klíč/hodnota u ID položky je nejúčinnějším druhem čtení, je třeba mít jistotu, že ID položky má smysluplnou hodnotu.

Dotazy, které čtou data z jednoho nebo více oddílů, vybírají větší latenci a spotřebovávají větší počet jednotek žádostí. Vzhledem k tomu, že každý oddíl má automatické indexování pro všechny vlastnosti, může být dotaz efektivně obsluhován z indexu. Dotazy, které používají více oddílů, můžete provádět rychleji pomocí možností paralelismu. Další informace o dělení a klíčích oddílů najdete v tématu [dělení v Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Vyhodnotit poplatek za jednotku žádosti pro dotaz

Po uložení dat do kontejnerů Azure Cosmos můžete pomocí Průzkumník dat v Azure Portal sestavit a spustit vaše dotazy. Náklady na dotazy můžete také získat pomocí Průzkumníka dat. Tato metoda vám poskytne představu o tom, jaké skutečné poplatky se týkají běžných dotazů a operací, které váš systém podporuje.

Náklady na dotazy můžete také získat programově pomocí sad SDK. Pokud chcete změřit režii jakékoli operace, jako je vytvoření, aktualizace nebo odstranění, zkontrolujte `x-ms-request-charge` hlavičku při použití REST API. Pokud používáte rozhraní .NET nebo Java SDK, `RequestCharge` vlastnost je ekvivalentní vlastnost pro získání poplatků za požadavek a tato vlastnost je k dispozici v rámci ResourceResponse nebo FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Faktory ovlivňující poplatky za jednotky požadavků pro dotaz

Jednotky žádostí na dotazy jsou závislé na několika faktorech. Například počet načtených nebo vrácených položek Azure Cosmos, počet hledání na index, čas kompilace dotazu atd. details. Azure Cosmos DB garantuje, že stejný dotaz, který se spustí na stejných datech, vždycky spotřebuje stejný počet jednotek žádostí i s opakováním spuštění. Profil dotazu používající metriky spuštění dotazu poskytuje dobrý nápad na to, jak se jednotky žádosti stráví.  

V některých případech se může zobrazit sekvence odpovědí 200 a 429 a jednotky požadavků na proměnnou na stránkovaném spuštění dotazů, to znamená, že dotazy budou na základě dostupné ru spuštěné co nejrychleji. Může se zobrazit přerušení provádění dotazů na více stránek/v průběhu odezvy mezi serverem a klientem. Například 10 000 položek může být vráceno jako více stránek, každou se účtují na základě výpočtu provedeného pro tuto stránku. Při sečtení na těchto stránkách byste měli získat stejný počet ru, jako byste získali pro celý dotaz.  

## <a name="metrics-for-troubleshooting"></a>Metriky pro řešení potíží

Výkon a propustnost využívané dotazy, uživatelsky definované funkce (UDF) většinou závisí na těle funkce. Nejjednodušší způsob, jak zjistit, kolik času vykonává provádění dotazů v systému souborů UDF a počet spotřebovaných ru, je povolení metriky dotazu. Pokud používáte sadu .NET SDK, tady jsou ukázkové metriky dotazů vrácené sadou SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Osvědčené postupy pro náklady na optimalizaci dotazů 

Při optimalizaci dotazů na náklady Vezměte v úvahu následující osvědčené postupy:

* **Společné umístění více typů entit**

   Zkuste vyhledat více typů entit v rámci jednoho nebo menšího počtu kontejnerů. Tato metoda poskytuje výhody nejen z cenové perspektivy, ale také pro provádění dotazů a transakce. Dotazy jsou vymezeny na jeden kontejner. a atomické transakce přes více záznamů prostřednictvím uložených procedur nebo triggerů jsou vymezeny na klíč oddílu v rámci jednoho kontejneru. Spolulokalizace entit v rámci stejného kontejneru může snížit počet síťových přenosů, které se budou překládat mezi záznamy. Proto zvyšuje konečný výkon, umožňuje atomické transakce nad více záznamy pro větší datovou sadu a v důsledku toho snižuje náklady. Pokud je pro váš scénář obtížné vyhledat více typů entit v rámci jednoho nebo menšího počtu kontejnerů, obvykle proto, že migrujete existující aplikaci a nechcete provádět žádné změny kódu – měli byste zvážit propustnost zřizování na úrovni databáze.  

* **Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

   Složitost dotazu ovlivňuje, kolik jednotek žádostí (ru) se spotřebuje pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové datové sady. Všechny tyto faktory ovlivňují náklady na operace dotazů. 

   Poplatek za požadavek vrácený v hlavičce požadavku indikuje náklady na daný dotaz. Pokud například dotaz vrátí položky 1000 1 – KB, cena za operaci je 1000. V takovém případě se server během jedné sekundy připadá pouze na dva takové požadavky, než frekvence omezí následné požadavky. Další informace najdete v článku [o jednotkách žádosti](request-units.md) a kalkulačkě jednotek žádostí. 

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [tom, jak funguje ceny Azure Cosmos](how-pricing-works.md)
* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)
* Další informace o [Azure Cosmos DB rezervované kapacity](cosmos-db-reserved-capacity.md)

