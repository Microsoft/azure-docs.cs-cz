---
title: Optimalizace nákladů na vaše požadavky v Azure Cosmos DB
description: Tento článek vysvětluje, jak optimalizovat náklady při vystavování požadavků na Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 3f4c33a88d9a8fbf2c3d64135d93da54cf75fab3
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097495"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Optimalizace nákladů na žádosti v Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Tento článek popisuje, jak se požadavky na čtení a zápis převádějí do [jednotek žádostí](request-units.md) a jak optimalizovat náklady na tyto požadavky. Operace čtení zahrnují čtení a dotazy bodů. Operace zápisu zahrnují vložení, nahrazení, odstranění a Upsert položek.

Azure Cosmos DB nabízí bohatou sadu databázových operací, které pracují s položkami v rámci kontejneru. Náklady spojené s jednotlivými operacemi se liší v závislosti na procesoru, V/V a paměti, které jsou potřeba k dokončení operace. Místo toho, abyste si vymysleli a spravovali hardwarové prostředky, si můžete představit jednotku žádosti (RU) jako jedno opatření pro prostředky požadované k provádění různých databázových operací k obsluze žádosti.

## <a name="measuring-the-ru-charge-of-a-request"></a>Měření poplatků za RU žádosti

Je důležité změřit poplatky za RU, abyste porozuměli skutečným nákladům a také vyhodnotili efektivitu vašich optimalizací. Tyto náklady můžete načíst pomocí Azure Portal nebo můžete zkontrolovat odpověď odeslanou zpět z Azure Cosmos DB prostřednictvím jedné ze sad SDK. Podrobné pokyny k tomuto účelu najdete [v tématu Vyhledání poplatků za jednotku žádosti v Azure Cosmos DB](find-request-unit-charge.md) .

## <a name="reading-data-point-reads-and-queries"></a>Čtení dat: čtení a dotazy bodů

Operace čtení v Azure Cosmos DB jsou obvykle seřazené z nejrychlejší/nejefektivnější na pomalejší/méně efektivní v souvislosti s využitím RU, a to následujícím způsobem:  

* Čtení bodů (vyhledávání klíč/hodnota na jednom ID položky a klíči oddílu).
* Dotaz s klauzulí Filter v rámci jednoho klíče oddílu
* Dotaz bez klauzule filtru rovnosti nebo rozsahu u jakékoli vlastnosti
* Dotaz bez filtrů

### <a name="role-of-the-consistency-level"></a>Role úrovně konzistence

Při použití **silné** nebo **ohraničené** [úrovně konzistence](consistency-levels.md)jsou náklady na ru všech operací čtení (Point Read nebo Query) dvojité.

### <a name="point-reads"></a>Čtení bodů

Jediným faktorem, který ovlivňuje poplatek za čtení bodu (kromě úrovně konzistence), je velikost načtené položky. V následující tabulce jsou uvedeny náklady na čtení v bodech pro položky, které mají velikost 1 KB až 100 KB.

| **Velikost položky** | **Náklady na jeden bod čtení** |
| --- | --- |
| 1 kB | 1 RU |
| 100 KB | 10 RU |

Vzhledem k tomu, že čtení bodů (vyhledávání klíč/hodnota u ID položky) představuje nejúčinnější typ čtení, měli byste se ujistit, že vaše ID položky má smysluplnou hodnotu, takže můžete načíst položky s bodem čtení (namísto dotazu), pokud je to možné.

### <a name="queries"></a>Dotazy

Jednotky žádostí na dotazy jsou závislé na několika faktorech. Například počet načtených nebo vrácených položek Azure Cosmos, počet hledání na index, čas kompilace dotazu atd. details. Azure Cosmos DB garantuje, že stejný dotaz, který se spustí na stejných datech, vždycky spotřebuje stejný počet jednotek žádostí i s opakováním spuštění. Profil dotazu používající metriky spuštění dotazu poskytuje dobrý nápad na to, jak se jednotky žádosti stráví.  

V některých případech se může zobrazit sekvence odpovědí 200 a 429 a jednotky požadavků na proměnnou na stránkovaném spuštění dotazů, to znamená, že dotazy budou na základě dostupné ru spuštěné co nejrychleji. Může se zobrazit přerušení provádění dotazů na více stránek/v průběhu odezvy mezi serverem a klientem. Například 10 000 položek může být vráceno jako více stránek, každou se účtují na základě výpočtu provedeného pro tuto stránku. Při sečtení na těchto stránkách byste měli získat stejný počet ru, jako byste získali pro celý dotaz.

#### <a name="metrics-for-troubleshooting-queries"></a>Metriky pro řešení potíží s dotazy

Výkon a propustnost využívané dotazy (včetně uživatelsky definovaných funkcí) většinou závisí na těle funkce. Nejjednodušší způsob, jak zjistit, kolik času vykonává provádění dotazů v systému souborů UDF a počet spotřebovaných ru, je povolení metriky dotazu. Pokud používáte sadu .NET SDK, tady jsou ukázkové metriky dotazů vrácené sadou SDK:

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

#### <a name="best-practices-to-cost-optimize-queries"></a>Osvědčené postupy pro náklady na optimalizaci dotazů 

Při optimalizaci dotazů na náklady Vezměte v úvahu následující osvědčené postupy:

* **Společné umístění více typů entit**

   Zkuste vyhledat více typů entit v rámci jednoho nebo menšího počtu kontejnerů. Tato metoda poskytuje výhody nejen z cenové perspektivy, ale také pro provádění dotazů a transakce. Dotazy jsou vymezeny na jeden kontejner. a atomické transakce přes více záznamů prostřednictvím uložených procedur nebo triggerů jsou vymezeny na klíč oddílu v rámci jednoho kontejneru. Spolulokalizace entit v rámci stejného kontejneru může snížit počet síťových přenosů, které se budou překládat mezi záznamy. Proto zvyšuje konečný výkon, umožňuje atomické transakce nad více záznamy pro větší datovou sadu a v důsledku toho snižuje náklady. Pokud je pro váš scénář obtížné vyhledat více typů entit v rámci jednoho nebo menšího počtu kontejnerů, obvykle proto, že migrujete existující aplikaci a nechcete provádět žádné změny kódu – měli byste zvážit propustnost zřizování na úrovni databáze.  

* **Měření a optimalizace pro nižší jednotky žádostí za sekundu použití**

   Složitost dotazu ovlivňuje, kolik jednotek žádostí (ru) se spotřebuje pro určitou operaci. Počet predikátů, povaha predikátů, počet UDF a velikost zdrojové datové sady. Všechny tyto faktory ovlivňují náklady na operace dotazů. 

Azure Cosmos DB poskytuje předvídatelný výkon z hlediska propustnosti a latence pomocí zřízeného modelu propustnosti. Zajištěná propustnost je vyjádřena z hlediska [jednotek žádosti](request-units.md) za sekundu nebo ru/s. Jednotka požadavku (RU) je logická abstrakce nad výpočetními prostředky, jako jsou například CPU, paměť, vstupně-výstupní operace atd., které jsou nutné k provedení žádosti. Zřízená propustnost (ru) je nastavená jako vyhrazená pro váš kontejner nebo databázi, aby poskytovala předvídatelné propustnost a latenci. Zajištěná propustnost umožňuje Azure Cosmos DB poskytovat předvídatelný a konzistentní výkon, zaručenou nízkou latenci a vysokou dostupnost v jakémkoli měřítku. Jednotky žádosti reprezentují normalizovanou měnu, která zjednodušuje určení toho, kolik prostředků aplikace potřebuje.

Poplatek za požadavek vrácený v hlavičce požadavku indikuje náklady na daný dotaz. Pokud například dotaz vrátí položky 1000 1 – KB, cena za operaci je 1000. V takovém případě se server během jedné sekundy připadá pouze na dva takové požadavky, než frekvence omezí následné požadavky. Další informace najdete v článku [o jednotkách žádosti](request-units.md) a kalkulačkě jednotek žádostí.

## <a name="writing-data"></a>Zápis dat

Náklady na zápis položky na základě RU závisí na:

- Velikost položky
- Počet vlastností, které jsou pokryté [zásadami indexování](index-policy.md) , a je nutné indexovat.

Vložení položky s 1 KB s méně než 5 vlastnostmi k indexování nákladů na 5 ru. Náhrada za položku pokaždé, když se poplatek vyžaduje pro vložení stejné položky.

### <a name="optimizing-writes"></a>Optimalizace zápisů

Nejlepším způsobem, jak optimalizovat náklady na operace zápisu, je nastavte správnou velikost položky a počet vlastností, které se indexují.

- Ukládání velmi velkých položek v Azure Cosmos DB má za následek poplatky za vysoké RU a může se považovat za anti-Pattern. Konkrétně neukládejte binární obsah ani velké bloky textu, na které se nemusíte dotazovat. Osvědčeným postupem je umístit tento druh dat do [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) a Uložit odkaz (nebo odkaz) na objekt BLOB v položce, do které zapisujete Azure Cosmos DB.
- Optimalizace zásad indexování tak, aby bylo možné indexovat pouze vlastnosti, na kterých váš dotaz filtruje, může mít velký rozdíl v ru spotřebovaných vašimi operacemi zápisu. Při vytváření nového kontejneru výchozí zásady indexování indexují každou a každou vlastnost, která se nachází ve vašich položkách. I když se jedná o dobrý výchozí stav pro vývojové aktivity, důrazně se doporučuje znovu vyhodnotit a [přizpůsobit zásady indexování](how-to-manage-indexing-policy.md) při přechodu do produkčního prostředí, nebo když vaše zatížení začne přijímat významné přenosy.

Při provádění hromadných přijímání dat se také doporučuje použít [knihovnu Azure Cosmos DB Bulk prováděcích knihoven](bulk-executor-overview.md) , protože je navržena k optimalizaci spotřeby ru těchto operací. Volitelně můžete také použít [Azure Data Factory](../data-factory/introduction.md) , která je postavená na stejné knihovně.

## <a name="next-steps"></a>Další kroky

Další informace o optimalizaci nákladů v Azure Cosmos DB najdete v následujících článcích:

* Další informace o [optimalizaci pro vývoj a testování](optimize-dev-test.md)
* Další informace o [Azure Cosmos DB vyúčtování](understand-your-bill.md)
* Další informace o [optimalizaci nákladů na propustnost](optimize-cost-throughput.md)
* Další informace o [optimalizaci nákladů na úložiště](optimize-cost-storage.md)
* Další informace o [optimalizaci nákladů na účty Azure Cosmos ve více oblastech](optimize-cost-regions.md)
* Další informace o [Azure Cosmos DB rezervované kapacity](cosmos-db-reserved-capacity.md)
