---
title: Optimalizace jednotek žádosti a náklady na spouštění dotazů ve službě Azure Cosmos DB
description: Zjistěte, jak za jednotky žádosti pro dotaz vyhodnotit a optimalizovat dotaz z hlediska výkonu a nákladů.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: e2cb1a76bfde5bea7707cf3cc6658e3fc82163c5
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043086"
---
# <a name="optimize-the-cost-required-to-run-queries-in-azure-cosmos-db"></a>Optimalizovat náklady na potřebné ke spouštění dotazů ve službě Azure Cosmos DB

Azure Cosmos DB nabízí bohatou sadu databázových operací včetně relačních a hierarchických dotazů, které pracují na položky v rámci kontejneru. Náklady spojené s každou z těchto operací se liší v závislosti na využití procesoru, vstup/výstup a paměti potřebných k dokončení operace. Místo přemýšlet o tom a správy hardwarových prostředků můžete jako jedno opatření pro prostředky požadované k provádění různých databázových operací, která bude sloužit žádost o představit jednotky žádosti (RU). Tento článek popisuje, jak za jednotky žádosti pro dotaz vyhodnotit a optimalizovat dotaz z hlediska výkonu a nákladů. 

Dotazy ve službě Azure Cosmos DB jsou obvykle seřazené od nejrychlejší/většinu efektivní nižší a méně efektivní z hlediska propustnosti následujícím způsobem:  

* Operace získání na jeden klíč oddílu a klíč položky.

* Dotazy s klauzulí filtru v rámci jednoho klíče oddílu.

* Dotazovat se bez klauzule filtru zjištění rovnosti, nebo rozsah jakékoli vlastnosti.

* Dotazy bez filtru.

Dotazy, které načítají data z jednoho nebo více oddílů vyšší latenci se vám účtovat a využívat větší počet jednotek žádostí. Protože každý oddíl má automatického indexování všech vlastností, můžete dotaz efektivně obsloužit z indexu. Můžete vytvořit dotazy, které používají více oddílů rychleji díky použití možností paralelismu. Další informace o vytváření oddílů a klíče oddílů, naleznete v tématu [dělení ve službě Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Vyhodnocení zátěž jednotek žádostí pro dotaz

Po uložení některá data v kontejnerech Azure Cosmos, můžete použít Průzkumníka dat na webu Azure Portal sestavit a spustit vaše dotazy. Náklady na dotazy můžete získat také pomocí Průzkumníku dat. Tato metoda získáte představu o skutečných poplatky spojené s typické dotazy a operace, které systém podporuje.

Náklady na dotazy můžete získat také programově pomocí sad SDK. K měření nároky na jakékoli operace, jako je vytváření, aktualizace nebo odstranění zkontrolujte `x-ms-request-charge` záhlaví při použití rozhraní REST API. Pokud používáte .net nebo sady Java SDK `RequestCharge` vlastnost je ekvivalentní vlastnost k získání zátěž žádostí a tato vlastnost je k dispozici v rámci ResourceResponse nebo FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Faktory ovlivňující zátěž jednotek žádostí pro dotaz

Jednotek žádostí pro dotazy jsou závislé na počtu faktorů. Například počet položek Azure Cosmos načíst/vrátil, počet hledání na index, čas kompilace dotazu podrobnosti atd. Azure Cosmos DB zaručuje, že stejný dotaz při spuštění na stejná data vždy využívat stejný počet jednotek žádostí i při opakovaném spuštění. Profil dotazu využitím metrik spouštění dotazu vám představu, jak se spotřebovávají jednotkách požadavků.  

V některých případech se může zobrazit posloupnost odpovědi 200 a 429 a jednotkách požadavků proměnné stránkovaného provádění dotazů, protože dotazy se spustí nejrychleji podle dostupných RU. Může se zobrazit provádění dotazu rozdělit do několika stránek/zaokrouhlit cest mezi serverem a klientem. Například 10 000 položek mohou být vráceny jako více stránek, každý účtuje podle výpočtu pro danou stránku. Když vás provede součet na těchto stránkách, měli byste obdržet stejný počet jednotek ru budete byste si pro celý dotaz.  

## <a name="metrics-for-troubleshooting"></a>Metriky pro řešení potíží

Výkon a propustnosti dotazů, uživatelem definované funkce (UDF) používané většinou závisí na tělo funkce. Nejjednodušší způsob, jak zjistit, kolik času spuštění dotazu se stráven v UDF a počet ru spotřebovaných, je zapnutí metrik dotazu. V případě použití sady .net SDK, tady je ukázkový dotaz metriky vrácené sady SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Osvědčené postupy a nákladů optimalizace dotazů 

Zvažte následující osvědčené postupy při optimalizaci dotazů pro náklady:

* **Společné umístění několik typů entit**

   Pokuste se umístit společně několik typů entit v rámci jednoduchých nebo menší počet kontejnerů. Tato metoda poskytuje výhody nejenom z hlediska cen, ale také k provádění dotazů a transakcí. Dotazy jsou vymezené do jednoho kontejneru; a atomické transakce přes více záznamů pomocí uložené procedury a triggery mají rozsah klíče oddílu v rámci jednoho kontejneru. Společné umístění entity v rámci stejného kontejneru můžete snížit počet síťových zpátečních cest k rozpoznání relací: n mezi záznamy. Tak to zvyšuje výkon začátku do konce, umožňuje atomické transakce přes několik záznamů pro větší datovou sadu a ve výsledku snižuje náklady. Pokud společné umístění několik typů entit v rámci jednoduchých nebo menší počet kontejnerů je obtížné pro váš scénář, obvykle vzhledem k tomu, že migrujete stávající aplikace a nechcete, aby se změny kódu – měli byste pak zvážit zřizování propustnost na úrovni databáze.  

* **Měření a ladění pro nižší požadavek jednotek za sekundu využití**

   Složitost dotazu má vliv na tom, kolik jednotek žádosti (ru) jsou využité pro operaci. Počet predikátů, povaze predikáty, počet funkcí UDF a velikost zdrojové datové sady. Všechny tyto faktory mají vliv náklady na operace dotazů. 

   Poplatek za žádost vrácená v hlavičce požadavku určuje náklady na daný dotaz. Například pokud dotaz vrátí 1 000 položek 1 KB, náklady na operace je 1000. V rámci jedné sekundy, v důsledku toho serveru respektuje pouze dva takové požadavky před tady následných žádostí. Další informace najdete v tématu [jednotky žádostí](request-units.md) článku a Kalkulačka jednotek žádosti. 

## <a name="next-steps"></a>Další postup

Potom můžete přejít k další informace o optimalizaci nákladů ve službě Azure Cosmos DB najdete v následujících článcích:

* Další informace o [jak Azure Cosmos fungují ceny](how-pricing-works.md)
* Další informace o [optimalizace pro vývoj a testování](optimize-dev-test.md)
* Další informace o [vysvětlení vašeho vyúčtování Azure Cosmos DB](understand-your-bill.md)
* Další informace o [optimalizace propustnosti náklady](optimize-cost-throughput.md)
* Další informace o [optimalizovat náklady na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizovat náklady na operace čtení a zápisy](optimize-cost-reads-writes.md)
* Další informace o [optimalizovat náklady na účty ve více oblastech Azure Cosmos](optimize-cost-regions.md)
* Další informace o [rezervované kapacity služby Azure Cosmos DB](cosmos-db-reserved-capacity.md)

