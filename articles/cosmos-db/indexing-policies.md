---
title: Azure Cosmos DB zásadám indexování | Dokumentace Microsoftu
description: Zjistěte, jak funguje indexování ve službě Azure Cosmos DB. Zjistěte, jak konfigurovat a měnit zásady indexování pro automatické indexování a vyšší výkon.
keywords: indexování fungování, automatické indexování, indexování databáze
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: fea3455b31ff2ea7119fa4146aa84f855a3b6e35
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44054668"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Jak funguje Azure Cosmos DB indexuje data?

Ve výchozím nastavení všechna data služby Azure Cosmos DB se indexuje zpětně. Ačkoli mnoho zákazníků s radostí umožňují automaticky zpracovat všechny aspekty indexování služby Azure Cosmos DB, můžete zadat vlastní *zásady indexování* pro kolekce během vytváření ve službě Azure Cosmos DB. Zásady indexování ve službě Azure Cosmos DB jsou pružnější a výkonnější než sekundární indexy, které nabízíme na jiných platformách, databáze. Ve službě Azure Cosmos DB můžete navrhovat a přizpůsobit tvar indexu, aniž byste museli obětovat flexibilitu schémat. 

Informace o tom, jak funguje indexování ve službě Azure Cosmos DB, je důležité pochopit, že při správě zásady indexování lze provádět podrobné kompromisy mezi režijní náklady na úložiště indexů, zápisu a propustnost dotazů a konzistenci dotazů.  

V následujícím videu ukazuje Azure Cosmos DB programový manažer Andrew Liu, automatické indexování, funkce a jak nakonfigurovat zásady indexování na váš kontejner Azure Cosmos DB můžete vyladit a rozhraní Azure Cosmos DB. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

V tomto článku se podíváme Zavřít ve službě Azure Cosmos DB indexování zásady na tom, jak přizpůsobit zásady indexování a přidružené kompromisy. 

Po přečtení tohoto článku, budete moci odpovědět na následující otázky:

* Jak lze přepsat vlastnosti, které chcete zahrnout nebo vyloučit z indexování?
* Jak lze nakonfigurovat index pro konečné aktualizace?
* Jak nakonfigurovat indexování provádět dotazy klauzule ORDER BY nebo oblast?
* Jak provést změny zásady indexování kolekce
* Jak porovnat úložiště a výkon různé zásady indexování?

## <a id="Indexing"></a> Indexování služby cosmos DB

Účelem indexy databáze je pro obsluhu dotazů v různých formách a tvary spotřeba minimální prostředků (např. využití procesoru a vstup/výstup) současně poskytují dobrou propustnost a nízkou latencí. Volba správného indexu pro dotazování databáze často vyžaduje mnohem plánování a služby experimentování ve službě. Tento přístup představuje výzvu pro bez schématu databáze, pokud data neodpovídají striktní schéma a rychle vyvíjí. 

Proto když jsme navrhovali indexování subsystému Cosmos DB, nastavíme následujících cílů:

* Indexování dokumentů bez nutnosti schématu: indexování subsystému nevyžaduje žádné informace o schématu nebo nevyvozujte předpoklady o schématu dokumentů.  

* Podpora pro efektivní a bohaté hierarchické a relační dotazů: index podporuje dotazovací jazyk služby Cosmos DB efektivně, včetně podpory pro hierarchické a relační projekce.  

* Podpora pro konzistentních dotazů in face of trvalý objem zápisy: pro úlohy zápisu vysoké propustnosti s konzistentních dotazů, index se aktualizuje přírůstkově, efektivně a online i v případě trvalý objem zápisy. Aktualizace konzistentní indexu je zásadní pro obsluhu dotazů na úrovně konzistence, ve kterém uživatel nakonfigurovaný dokumentu služby.  

* Podpora pro více tenantů: Zadaný model založený na rezervaci pro zásady správného řízení prostředků mezi tenanty, index se aktualizace prováděly v mezích rozpočtu systémových prostředků (procesoru, paměti a vstupně výstupní operace za sekundu) přidělené na repliku.  

* Efektivitu úložiště: pro nákladové efektivity režijní náklady na diskové úložiště indexu je omezená a předvídatelné. To je zásadní, protože Cosmos DB umožňuje vývojářům dělat kompromisy, co náklady na základě mezi režijní náklady na indexů ve vztahu k výkonu dotazů.  

## Upravit zásady indexování kolekce <a id="CustomizingIndexingPolicy"></a>  
Kompromisy mezi úložiště, zápisu a výkon dotazů a konzistenci dotazů můžete přizpůsobit tak, že přepíšete výchozí zásady na kolekci Azure Cosmos DB indexování. Můžete nakonfigurovat následující aspekty:

* **Zahrnout nebo vyloučit dokumenty a cesty do a z indexu**. Můžete vyloučit nebo zahrnout konkrétní dokumenty v indexu při vložení nebo nahrazení dokumenty v kolekci. Můžete také zahrnout nebo vyloučit určité vlastnosti JSON, také nazývané *cesty*, indexovaných mezi dokumenty, které jsou zahrnuty v indexu. Programy zahrnují vzor zástupných znaků.
* **Konfiguraci různých typů index**. Pro každou zahrnuté cestu můžete zadat typ index, který se vyžaduje cesta pro kolekci. Můžete určit typ indexu na základě cesty dat, očekávané zátěže a číselné nebo řetězec "přesnosti."
* **Konfigurace režimů aktualizace indexu**. Azure Cosmos DB podporuje tři režimy indexování: konzistentní vzhledem k aplikacím, opožděné a None. Indexování způsoby přes zásady indexování můžete konfigurovat v kolekci Azure Cosmos DB. 

Následující fragment kódu rozhraní Microsoft .NET ukazuje, jak nastavit vlastní zásady indexování při vytváření kolekce. V tomto příkladu jsme nastavili zásady s indexem rozsahu pro řetězce a čísla na maximální přesnost. Můžete použít tyto zásady můžete spouštět dotazy klauzule ORDER BY řetězců.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Schéma JSON pro zásady indexování změnit verzi rozhraní REST API verze 2015-06-03. V této verzi podporuje schéma JSON pro zásady indexování rozsah indexů řetězce. Sady .NET SDK 1.2.0 a Javy, Pythonu a sady SDK pro Node.js 1.1.0 podporují nové schéma zásad. Starší verze sady SDK pomocí rozhraní REST API verze 2015-04-08. Podporují starší schéma pro zásady indexování.
> 
> Ve výchozím nastavení služby Azure Cosmos DB indexuje všechny vlastnosti řetězce v rámci dokumentů konzistentně s indexem Hash. Indexuje všechny číselné vlastností v dokumentech konzistentně s indexem rozsahu.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Upravit zásady indexování na portálu

Můžete změnit zásady indexování kolekce na webu Azure Portal: 

1. Na portálu přejděte k vašemu účtu Azure Cosmos DB a pak vyberte vaši kolekci. 
2. V levé navigační nabídce vyberte **nastavení**a pak vyberte **zásady indexování**. 
3. V části **zásady indexování**změnit zásady indexování a pak vyberte **OK**. 

### Režimy indexování databáze <a id="indexing-modes"></a>  
Azure Cosmos DB podporuje tři režimy indexování, které můžete nakonfigurovat přes zásady indexování v kolekci Azure Cosmos DB: konzistentní vzhledem k aplikacím, opožděné a None.

**Konzistentní**: Pokud zásady kolekci Azure Cosmos DB je konzistentní, dotazy na konkrétní kolekci Azure Cosmos DB použijte stejnou úroveň konzistence jak je uvedeno pro operace čtení bod (silná, ohraničená odolnost, relace a konečný výsledek). Index se aktualizuje synchronně jako součást aktualizace dokumentu (vložení, nahradit, aktualizace a odstranění dokumentů v kolekci Azure Cosmos DB).

Konzistentní indexování podporuje konzistentních dotazů za cenu možné snížení v zapisování. Toto snížení je funkce jedinečné cesty, které je třeba indexovat a "úrovně konzistence." Konzistentní indexování režimu je navržená pro "napsat rychle, okamžitě dotaz" úlohy.

**Opožděné**: index asynchronně aktualizuje, když kolekci Azure Cosmos DB je klidný, to znamená, když kapacita propustnosti kolekci není plně využít k obsluze uživatelských požadavků.  Všimněte si, že může být nekonzistentní výsledky dojde, protože data se ingestují a indexovat pomalu. To znamená, že počet dotazů nebo konkrétní dotaz výsledky nemusí být konzistentní vzhledem k aplikacím nebo repeatable v zadaný čas. 

Index je obvykle v režimu můžete projít s přijatých dat. Pomocí Opožděné indexování čas live (TTL) změní výsledek v indexu se vyřadit a znovu vytvořit. Díky tomu výsledky počet a dotaz nekonzistentní pro určitou dobu. Většina účtů služby Azure Cosmos DB musí používat konzistentní indexování režim.

**Žádný**: kolekce, která nemá žádný index režim nemá žádný index s ním spojená. To se běžně používá, pokud služby Azure Cosmos DB se používá jako úložiště klíč / hodnota a dokumenty jsou přístupné pouze podle jejich ID vlastnosti. 

> [!NOTE]
> Konfigurace zásady indexování s jako jeden má vedlejší účinek vyřadit všechny existující index. Použijte, pokud vaše vzorce přístupu vyžadovat jen ID nebo odkaz na sebe sama.
> 
> 

Následující tabulka uvádí konzistence pro dotazy na základě indexování režim (konzistentní a opožděné) nakonfigurovaný pro kolekci a úroveň konzistence zadaná pro zadání dotazu. To platí pro dotazy pomocí libovolné rozhraní: rozhraní REST API, SDK, nebo v rámci uložené procedury a triggery. 

|Konzistence|Indexování režimu: konzistentní vzhledem k aplikacím|Indexování režimu: opožděné|
|---|---|---|
|Silné|Silné|Nahodilé|
|Omezená neaktuálnost|Omezená neaktuálnost|Nahodilé|
|Relace|Relace|Nahodilé|
|Nahodilé|Nahodilé|Nahodilé|

Azure Cosmos DB vrátí chybu pro dotazy na kolekce, které nemáte nic indexování režimu. Dotazy mohou být provedeny stále jako kontroly prostřednictvím explicitního **x-ms-documentdb-enable kontroly** záhlaví v rozhraní REST API nebo **EnableScanInQuery** vyžádat možnost pomocí sady .NET SDK. Některé funkce dotazu, jako je klauzule ORDER BY, nejsou podporovány jako kontroly s **EnableScanInQuery**.

V následující tabulce jsou uvedeny konzistence pro dotazy podle indexování režimu (konzistentní, opožděné nebo žádný) při **EnableScanInQuery** určena.

|Konzistence|Indexování režimu: konzistentní vzhledem k aplikacím|Indexování režimu: opožděné|Indexování režimu: žádné|
|---|---|---|---|
|Silné|Silné|Nahodilé|Silné|
|Omezená neaktuálnost|Omezená neaktuálnost|Nahodilé|Omezená neaktuálnost|
|Relace|Relace|Nahodilé|Relace|
|Nahodilé|Nahodilé|Nahodilé|Nahodilé|

Následující ukázka kódu ukazují, jak vytvořit kolekci Azure Cosmos DB s použitím sady .NET SDK s konzistentní indexování u všech vložení dokumentu.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index cesty
Azure Cosmos DB modely dokumentů JSON a index jako stromové struktury. Můžete ladit zásady pro cesty v rámci stromu. V rámci dokumentů můžete použít cesty pro zahrnutí nebo vyloučení z indexování. To může nabídnout vylepšené zápisu výkon a dolní index úložiště pro scénáře, ve kterých jsou předem známé vzory dotazů.

Index cesty začínat kořenový adresář (/) a obvykle ukončen? operátor zástupných znaků. To znamená, že existuje několik možných hodnot pro předponu. Například sloužit SELECT * FROM F.familyName WHERE řady F = "Andersen", musí obsahovat cestu k indexu pro /familyName/? v zásadách indexu kolekce.

Cesty k indexu můžete použít také \* operátor zástupný znak můžete určit chování pro rekurzivně cest v rámci předponu. Například/datové části / * lze použít k vyloučení všechno v datové části vlastnost indexování.

Tady jsou běžné vzory pro zadání cesty k indexu:

| Cesta                | Popis/případ                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Výchozí cesta pro kolekci. Rekurzivní a platí pro celý dokument stromu.                                                                                                                                                                                                                                   |
| / prop /?             | Cesta index zapotřebí pro zpracování dotazů jako následující (s typy hodnot Hash nebo rozsahu, v uvedeném pořadí):<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop > 5<br><br>Vyberte z kolekce c ORDER BY c.prop                                                                       |
| / prop / *             | Cesta index u všech cest v rámci zadaného popisku. Funguje s následující dotazy<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop.subprop > 5<br><br>Vyberte z kolekce c WHERE c.prop.subprop.nextprop = "hodnota"<br><br>Vyberte z kolekce c ORDER BY c.prop         |
| / Vlastnosti / [] /?         | Požadované index cestu iterace a připojte se k dotazy na pole skaláry, jako je ["a", "b", "c"]:<br><br>Vyberte značku ze značky v collection.props značky WHERE = "hodnota"<br><br>Vyberte značku z kolekce c spojení značky v c.props kde označit > 5                                                                         |
| [] /subprop/ /props/? | Index cesta zapotřebí pro zpracování iterace a spojení dotazy na pole objektů, jako je [{subprop: "a"}, {subprop: "b"}]:<br><br>Vyberte značku ze značky v collection.props WHERE tag.subprop = "hodnota"<br><br>Vyberte značku z kolekce c spojení značky v c.props WHERE tag.subprop = "hodnota"                                  |
| / prop/subprop /?     | Cesta indexu, které jsou zapotřebí pro zpracování dotazů (s typy hodnot Hash nebo rozsahu, v uvedeném pořadí):<br><br>Vyberte z kolekce c WHERE c.prop.subprop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Když nastavíte vlastní index cesty, je nutné zadat výchozí pravidlo indexování pro celý dokument stromu, který je označen zvláštní cesta "a *". 
> 
> 

Následující příklad nastaví konkrétní cesty s indexem rozsahu a hodnotu vlastní přesnost 20 bajtů:

```
    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    

    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);
```

Když se přidá cestu pro indexování, jsou indexovány čísel a řetězců v rámci těchto cest. Takže i když definujete indexování pouze řetězce, přidá službu Azure Cosmos DB výchozí definici pro čísla i. Jinými slovy, Azure Cosmos DB má možnost Cesta vyloučení ze zásady indexování, ale ne type vyloučení z konkrétní cesty. Tady je příklad, Poznámka pouze jeden index je určená pro obě cesty (cesta = "/ *" a cesta = "/\"attr1\"/?"), ale počet datový typ je taky přidaný ke výsledek.

```
var indices = new[]{
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 }// <- note: only 1 index specified
                    },
                    Path =  "/*"
                },
                new IncludedPath  {
                    Indexes = new Collection<Index>
                    {
                        new RangeIndex(DataType.String) { Precision = 3 } // <- note: only 1 index specified
                    },
                    Path =  "/\"attr1\"/?"
                }
            };...

            foreach (var index in indices)
            {
                documentCollection.IndexingPolicy.IncludedPaths.Add(index);
            }
```

Výsledek vytvoření indexu:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        },
        {
            "path": "/\"attr\"/?",
            "indexes": [
                {
                    "kind": "Range",
                    "dataType": "String",
                    "precision": 3
                },
                {
                    "kind": "Range",
                    "dataType": "Number",
                    "precision": -1
                }
            ]
        }
    ],
}
```

### <a name="index-data-types-kinds-and-precisions"></a>Index datových typů, typů a přesnosti
Máte několik možností, když konfigurujete zásady indexování pro cestu. Můžete zadat jednu nebo víc definic indexování pro každou cestu:

* **Datový typ**: řetězec, číslo, bod, mnohoúhelník nebo LineString (může obsahovat jenom jeden záznam za datový typ na cestu).
* **Index typu**: hodnoty Hash (dotazy na rovnost), rozsah (rovnosti, oblast nebo ORDER BY dotazy) nebo Spatial (prostorový dotazy).
* **Přesnost**: index pro Hash, to se liší od 1 do 8 pro čísla i řetězce. Výchozí hodnota je 3. Pro index na rozsah Tato hodnota může být -1 (Maximální přesnost). To se může lišit mezi 1 a 100 (Maximální přesnost) pro řetězec nebo číselné hodnoty.

#### <a name="index-kind"></a>Typ indexu
Azure Cosmos DB podporuje Hash index a typy index rozsahu pro každou cestu, která je možné nakonfigurovat pro datové typy řetězec nebo číslo, nebo obojí.

* **Hodnota hash** podporuje efektivní rovnosti a dotazy spojení. Pro většinu případů použití nepotřebujete indexy Hash vyšší přesností než na výchozí hodnotu 3 bajtů. Datový typ může být řetězec nebo číslo.
* **Rozsah** podporuje dotazy na rovnost efektivní, dotazy na rozsah (pomocí >, <>, =, < =,! =) a dotazy klauzule ORDER BY. Dotazy klauzule ORDER By ve výchozím nastavení také vyžadovat maximální index přesnosti (-1). Datový typ může být řetězec nebo číslo.

Azure Cosmos DB podporuje také typ prostorového indexu pro každou cestu, která se dá nastavit pro datové typy, které bod mnohoúhelníku či LineString. Hodnota v zadané cestě musí být platný fragment GeoJSON jako `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Prostorový** podporuje efektivní spatial (v rámci a vzdálenost) dotazy. Datový typ může být bodu mnohoúhelníku či LineString.

> [!NOTE]
> Azure Cosmos DB podporuje automatické indexování bodu mnohoúhelníku a LineString datových typů.
> 
> 

Toto jsou typy podporovaných index a příklady dotazů, které je možné použít pro obsluhu:

| Typ indexu | Popis/případ                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hodnota hash       | Hodnota hash přes/prop /? (nebo /) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Hodnota hash přes/vlastnosti / [] /? (nebo / / vlastnosti/nebo) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte značku z kolekce c spojení značky v c.props značky WHERE = 5                                                                                                                       |
| Rozsah      | V rozsahu přes/prop /? (nebo /) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop > 5<br><br>Vyberte z kolekce c ORDER BY c.prop                                                                                                                                                                                                              |
| Spatial     | V rozsahu přes/prop /? (nebo /) umožňuje efektivně slouží následující dotazy:<br><br>Vyberte z kolekce c<br><br>KDE ST_DISTANCE (c.prop, {"type": "Bodu", "coordinates": [0.0, 10.0]}) < 40<br><br>Vyberte z kolekce c kde ST_WITHIN(c.prop, {"type": "Polygon",...}) – s indexování povolený tento počet bodů<br><br>Vyberte z kolekce c kde ST_WITHIN({"type": "Point",...}, c.prop) – s indexování povoleno mnohoúhelníky              |

Ve výchozím nastavení, vrátí se chyba pro dotazy v rozsahu operátory, jako > =, pokud neexistuje žádný index rozsahu (z jakékoli přesnosti) na signál, že kontrola může být potřeba poskytovat dotaz. Dotazy na rozsah můžete provést bez index na rozsah s použitím **x-ms-documentdb-enable kontroly** záhlaví v rozhraní REST API nebo **EnableScanInQuery** vyžádat možnost pomocí sady .NET SDK. Pokud nejsou žádné filtry v dotazu, že služby Azure Cosmos DB pomocí indexu můžete filtrovat proti, je vrácena žádná chyba.

Stejná pravidla platí i pro prostorových dotazů. Ve výchozím nastavení je vrácena chyba pro prostorových dotazů, pokud neexistuje žádný prostorový index, a neexistují žádné filtry, které může obsloužit z indexu. Je možné provést v prohledávání pomocí **x-ms-documentdb-enable kontroly** nebo **EnableScanInQuery**.

#### <a name="index-precision"></a>Index přesnost
Aby kompromisy mezi nároky na úložiště indexů a výkon dotazů, můžete použít index přesnosti. Pro čísla doporučujeme použít výchozí konfiguraci přesnost-1 (maximální). Protože jsou čísla 8 bajtů ve formátu JSON, jde o ekvivalent konfigurace 8 bajtů. Výběrem hodnoty nižší přesnost, jako je například 1 až 7, prostředky, které hodnoty v rámci některé oblasti mapují na stejný index položky. Proto je zmenšit prostor úložiště indexu, ale provádění dotazů může být nutné zpracovat více dokumentů. V důsledku toho využívá větší propustnost v jednotkách požadavků.

Přesnost konfigurace indexu má více praktické využití s oblastmi řetězec. Protože řetězce může být jakékoli libovolné délky, volba přesnosti indexu může ovlivnit výkon dotazů na rozsah řetězec. Velikost indexu úložného prostoru, který je potřeba také může ovlivnit. Řetězec rozsah indexů může mít nakonfigurovanou 1 až 100 nebo -1 (maximální). Pokud chcete provést klauzule ORDER BY dotazy na vlastnosti řetězce, je nutné zadat s přesností na -1 pro odpovídající cesty.

Prostorové indexy vždy používat výchozí přesnost index pro všechny typy (Point, LineString a mnohoúhelníku). Nelze přepsat výchozí přesnost index pro prostorové indexy. 

Následující příklad ukazuje, jak zvýšit přesnost pro rozsah indexů kolekce s použitím sady .NET SDK. 

**Vytvořte kolekci s vlastním indexu přesností**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB vrátí chybu při dotazu pomocí klauzule ORDER BY ale není zaškrtnuta možnost index na rozsah proti dotazované cestu s nejvyšší přesností. 
> 
> 

Podobně můžete zcela vyloučit cesty indexování. Následující příklad ukazuje, jak vyloučit celý oddíl dokumenty ( *podstrom*) z indexování pomocí \* zástupných znaků operátoru.

    var excluded = new DocumentCollection { Id = "excludedPathCollection" };
    excluded.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    excluded.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Vyjádřit výslovný souhlas a vyjádřit výslovný nesouhlas indexování
Můžete zvolit, zda má být kolekce, kterou chcete automaticky indexuje všechny dokumenty. Ve výchozím nastavení všechny dokumenty jsou automaticky indexovány, ale můžete vypnout automatické indexování. Když je vypnutý indexování, dokumentů je přístupný pouze prostřednictvím jejich odkazů na sebe sama nebo dotazy pomocí dokumentů ID.

Pomocí automatické indexování, vypnutý, můžete stále selektivně přidat pouze konkrétní dokumenty do indexu. Naopak můžete nechat automatické indexování, abyste si zvolit vyloučit konkrétní dokumenty. Indexování zapnout nebo vypnout konfigurace jsou užitečné, pokud máte jenom podmnožinu dokumentů, které potřebuje, aby se dalo dotazovat.

Následující příklad ukazuje, jak vložit dokument explicitně pomocí [SQL SDK pro .NET API](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) a [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) vlastnost.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Upravit zásady indexování kolekce
Ve službě Azure Cosmos DB můžete provedené změny zásady indexování kolekce v reálném čase. Změna zásady v kolekci Azure Cosmos DB indexování může vést ke změně tvaru index. Změna ovlivní cesty, které můžete indexovat, jejich přesnosti a modelu konzistence indexu samotný. Změna zásady indexování efektivně vyžaduje transformaci starého indexu do nového indexu. 

**Transformace indexu online**

![Jak funguje indexování – transformace online indexu služby Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

Index transformace jsou provedeny online. To znamená, že dokumenty indexované podle starého zásad jsou transformovány efektivně na nové zásady *bez ovlivnění dostupnosti zápisu nebo zřízená propustnost* kolekce. Konzistence čtení a zápisu operace, které provádějí pomocí rozhraní REST API, SDK, nebo v rámci uložené procedury a triggery neovlivní při transformaci indexu. 

Změna zásady indexování je asynchronní proces a čas k dokončení operace závisí na počet dokumentů, zřízených ru a velikost dokumentů. Probíhá vytvoření nového indexu, dotaz nemusí vracet všechny odpovídající výsledky, pokud dotaz používá index, který je právě upravuje a dotazy nebudou nalezeny žádné chyby nebo selhání. Probíhá vytvoření nového indexu, dotazy jsou konzistentní bez ohledu na to indexování konfiguraci režimu (konzistentní nebo líné). Po indexu transformace se dokončí, budete i nadále konzistentní výsledky. To platí i pro dotazy ze všech rozhraní: rozhraní REST API, SDK a v rámci uložené procedury a triggery. Stejně jako s Opožděné indexování, index je provedena transformace asynchronně na pozadí na replikách pomocí náhradních prostředků, které jsou k dispozici pro konkrétní repliky. 

Index transformace také probíhají na místě. Azure Cosmos DB není udržovat dvě kopie index a přepnutí na starý index s novým. To znamená, že žádné další místo na disku je nutné využívat v kolekcích, zatímco index dochází.

Při změně zásady indexování aplikují změny přesunout z původní index do nové především podle indexování konfigurací režimu. Indexování konfigurace režimu hrají roli větší než jiné hodnoty jako zahrnuté a vyloučené cesty typy index a přesnosti. 

Pokud vaše staré a nové zásady obě používají konzistentní indexování, Azure Cosmos DB provede transformaci indexu online. Nelze použít jiné indexování změnu zásad, který má konzistentní indexování režimu, zatímco probíhá transformace. Však můžete přesunout k opožděné nebo žádný probíhá indexování režimu při transformaci: 

* Při přesunu do opožděné, změna zásad indexu je hned platná. Azure Cosmos DB začínají znovuvytvoření indexu asynchronně. 
* Při přesunu na žádný index se okamžitě ukončí. Přechod na hodnotu None je užitečné, když chcete zrušit transformaci probíhá a začít pracovat s různé zásady indexování. 

Následující fragment kódu ukazuje, jak změnit kolekci zásady indexování z konzistentní indexování režimu do režimu Opožděné indexování. Pokud při použití sady .NET SDK, může aktivovat indexování změnu zásad pomocí nového **ReplaceDocumentCollectionAsync** metody.

**Upravit zásady indexování z konzistentní k opožděné**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Sledování průběhu index transformace**

Procento průběhu transformace index do konzistentního indexu můžete sledovat pomocí **IndexTransformationProgress** vlastnost odpovědi z **ReadDocumentCollectionAsync** volání. Jiných sad SDK a rozhraní REST API podporují rovnocenné vlastností a metod pro indexování změn zásad. Můžete zkontrolovat průběh transformaci index do konzistentního indexu zavoláním **ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * **IndexTransformationProgress** vlastnost se vztahuje pouze při transformování do konzistentního indexu. Použití **ResourceResponse.LazyIndexingProgress** vlastnost pro sledování transformace opožděné indexu.
> * **IndexTransformationProgress** a **LazyIndexingProgress** vlastnosti se vyplní pouze pro kolekci bez oddílů, to znamená, že kolekce, který je vytvořen bez klíče oddílu.
>

Přechodem na hodnotu None indexování režimu můžete vyřadit index pro kolekci. Pokud chcete zrušit transformaci probíhá a okamžitě spustit novou to může být užitečné provozní nástroj.

**Odstranit index pro kolekci**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Pokud by provedete indexování změny zásad kolekce Azure Cosmos DB? Následují nejběžnějších případech použití:

* Poskytovat konzistentní výsledky při normálním provozu, ale během importu dat hromadného vrátit zpět k Opožděné indexování režimu.
* Začněte používat nové funkce indexování na aktuální kolekce Azure Cosmos DB. Můžete například použít geoprostorové dotazy, které vyžaduje typ prostorového indexu nebo ORDER BY / řetězec dotazy na rozsah, které vyžadují řetězec index typu rozsah.
* Ruční výběr vlastnosti, které mají být indexovány a je v průběhu času měnit.
* Vyladění indexování přesnosti pro zlepšení výkonu dotazů nebo ke snížení využité úložiště.

> [!NOTE]
> Upravit zásady indexování pomocí **ReplaceDocumentCollectionAsync**, musíte použít verzi 1.3.0 nebo novější verze sady .NET SDK.
> 
> Pro transformace index úspěšně dokončit Ujistěte se, že není dostatek volného místa k dispozici v kolekci. Pokud kolekce dosáhne kvóta úložiště, se pozastaví transformace indexu. Index transformace automaticky obnoví při prostor úložiště je k dispozici, například pokud odstraníte některé dokumenty.
> 
> 

## <a name="performance-tuning"></a>Ladění výkonu
Rozhraní SQL API poskytují informace o metrikách výkonu, jako je například využité úložiště indexů a náklady na propustnost (jednotky žádostí) u každé operace. Tyto informace můžete použít k porovnání různé zásady indexování a pro optimalizaci výkonu.

Můžete zkontrolovat kvótu úložiště a využití kolekce spuštěním **HEAD** nebo **získat** požadavek proti kolekci prostředků. Poté, zkontrolujte **x-ms žádost quota** a **x-ms žádost využití** záhlaví. V sadě .NET SDK [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) a [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) vlastnosti v [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) obsahovat tyto hodnoty, které odpovídají.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


K měření režii indexování na každou operaci zápisu (vytvoření, aktualizace nebo odstranění), zkontrolujte **x-ms žádost poplatek** záhlaví (nebo ekvivalentní [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) vlastnost v [ ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) v sadě .NET SDK) k měření počtu jednotek žádosti, které se spotřebovávají tyto operace.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Změny specifikace zásady indexování
Změny ve schématu pro zásady indexování byl zaveden dne 7. 2015 pomocí rozhraní REST API verze 2015-06-03. Odpovídající třídy v verze sady SDK mají nové implementace tak, aby odpovídala schématu. 

Ve specifikaci JSON byly implementovány následující změny:

* Zásady indexování podporuje rozsah indexů pro řetězce.
* Jednotlivé cesty může mít několik definic indexu. To může mít jednu pro jednotlivé datové typy.
* Indexování přesnost podporuje 1 až 8 pro čísla, 1 až 100 pro řetězce a -1 (Maximální přesnost).
* Segmenty cesty nevyžadují dvojité uvozovky, řídicí jednotlivé cesty. Například můžete přidat cestu pro   **/title /?** místo **/ "title" /?**.
* Kořenová cesta, která představuje "všechny cesty" může být reprezentován jako **/ \*** (kromě **/**).

Pokud máte kód této kolekce ustanovení pomocí vlastních zásad indexování napsané pomocí sady .NET SDK verze 1.1.0 nebo dřívější verzi, přejděte na verzi 1.2.0, sadu SDK, musíte změnit kódu aplikace, která by tyto změny. Pokud nemáte kód, který nakonfiguruje zásady indexování, nebo pokud plánujete dál používat starší verzi sady SDK, nejsou potřeba žádné změny.

Praktické porovnání tady je příklad vlastní zásady indexování napsané pomocí rozhraní REST API verze 2015-06-03, za nímž následuje stejné zásady indexování vytvořili pomocí starší verze 2015-04-08 REST API.

**Aktuální zásady JSON (rozhraní REST API verze 2015-06-03) indexování**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }


**Dříve indexování zásad JSON (rozhraní REST API verze 2015-04-08)**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>Další postup
Pro index zásad správy ukázky a další informace o službě Azure Cosmos DB dotazovací jazyk v následujících tématech:

* [Ukázky kódu správu index rozhraní SQL API .NET](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Operace kolekce SQL rozhraní API REST](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Dotazy pomocí jazyka SQL](sql-api-sql-query.md)

