---
title: Azure DB Cosmos zásady indexování | Microsoft Docs
description: Vysvětluje, jak indexování v Azure Cosmos DB. Zjistěte, jak nakonfigurovat a změnit zásady indexování pro automatické indexování a lepší výkon.
keywords: jak indexování funguje, automatické indexování, indexování databáze
services: cosmos-db
author: rafats
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: rafats
ms.openlocfilehash: 3abae65ccc430c791e289a4767d057cf010b974b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700328"
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Jak funguje Azure Cosmos DB data indexu?

Ve výchozím nastavení je indexovaný všechna data v Azure Cosmos DB. I když jsou radostí umožníte Azure DB Cosmos automaticky zpracovávat všechny aspekty indexování mnoho zákazníků, můžete zadat vlastní *indexování zásad* pro kolekce během vytváření v Azure Cosmos DB. Indexování zásady v Azure Cosmos DB jsou pružnější a výkonnější než sekundární indexy, které nabízí na jiných platformách, databáze. V Azure DB Cosmos můžete navrhnout a přizpůsobit tvaru indexu, aniž by došlo ke ztrátě flexibilitu schémat. 

Informace o tom, jak indexování funguje v Azure Cosmos DB, je důležité si uvědomit, když spravujete zásady indexování, abyste vytvořili podrobných kompromis mezi režijní náklady na úložiště indexů, zápisu a propustnost dotazu a konzistence dotazu.  

V následujícím videu ukazuje Azure manažer programu DB Cosmos Andrew Liu Azure DB Cosmos automatické indexování schopnosti a jak ladit a nakonfigurovat zásady indexování na vaše kontejner Azure Cosmos DB. 

>[!VIDEO https://www.youtube.com/embed/uFu2D-GscG0]

V tomto článku jsme trvat zblízka na Azure Cosmos DB indexování zásady na tom, jak přizpůsobit zásady indexování a přidružené kompromis. 

Po přečtení tohoto článku, budete moct odpovězte si na následující otázky:

* Jak můžete přepsat vlastnosti, které chcete zahrnout nebo vyloučit z indexování?
* Konfigurování index pro případné aktualizace
* Jak nakonfigurovat indexování provádět dotazy ORDER BY ani rozsah?
* Jak provést změny zásady indexování kolekce?
* Jak porovnat úložiště a výkon různé zásady indexování?

## Přizpůsobit zásady indexování kolekce <a id="CustomizingIndexingPolicy"></a>  
Přepsání výchozího indexování zásady na kolekci Azure Cosmos DB můžete přizpůsobit kompromis mezi úložiště, zápisu a výkon dotazů a konzistence dotazu. Můžete nakonfigurovat následující aspekty:

* **Zahrnout nebo vyloučit dokumenty a cesty do a z indexu**. Můžete vyloučit nebo zahrnout konkrétní dokumenty v indexu při vložení nebo nahrazení dokumenty v kolekci. Můžete taky zahrnout nebo vyloučit konkrétní vlastnosti JSON, označované taky jako *cesty*, indexovaných na dokumentech, které jsou zahrnuty v indexu. Cesty obsahovat zástupné znaky.
* **Konfigurovat různé typy index**. Pro každou z cest zahrnuty můžete zadat typ index, který se vyžaduje cesta pro kolekci. Můžete zadat typ indexu na základě dat cesty, očekávané dotazu úlohy a číselný či řetězce "přesnost."
* **Konfiguraci režimů aktualizace indexu**. Azure Cosmos DB podporuje tři indexování režimy: konzistentní, opožděné a None. Režimů indexování prostřednictvím indexování zásad můžete konfigurovat v kolekci Azure Cosmos DB. 

Následující fragment kódu rozhraní Microsoft .NET ukazuje, jak nastavit vlastní zásady indexování, při vytváření kolekce. V tomto příkladu jsme na maximální přesnost nastavte zásady s indexem rozsahu pro řetězce a čísla. Tyto zásady můžete spouštět dotazy ORDER BY na řetězce.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> Schéma JSON pro zásady indexování změnit verzi rozhraní REST API verze 2015-06-03. V této verzi podporuje schéma JSON pro indexování zásad rozsah indexů pro řetězce. .NET SDK 1.2.0 a Java, Python a Node.js SDK 1.1.0 podporovat nové schéma zásad. Starší verze sady SDK pomocí rozhraní REST API verze 2015-04-08. Podporují starší schéma pro indexování zásad.
> 
> Ve výchozím nastavení Azure Cosmos DB indexuje všechny vlastnosti řetězce v rámci dokumenty konzistentně s indexem Hash. Všechny číselné vlastnosti v rámci dokumenty jej indexovat konzistentně s indexem rozsahu.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Přizpůsobit zásady indexování na portálu

Můžete změnit zásady indexování kolekce na portálu Azure: 

1. Na portálu přejděte k účtu Azure Cosmos DB a pak vyberte kolekce. 
2. V levém navigační nabídce vyberte **nastavení**a potom vyberte **zásady indexování**. 
3. V části **zásady indexování**, změnit zásady indexování a pak vyberte **OK**. 

### Režimy indexování databáze <a id="indexing-modes"></a>  
Azure Cosmos DB podporuje tři indexování režimy, které můžete nakonfigurovat přes zásady indexování v kolekci Azure Cosmos DB: konzistentní, opožděné a None.

**Konzistentní**: Pokud kolekci Azure Cosmos DB zásady nejsou konzistentní, dotazy na určitou kolekci Azure Cosmos DB, postupujte podle kroků stejnou úroveň konzistence jsou zadány pro čtení bodu (silné a ohraničenou odolností, založenou relace, nebo případné). Index je aktualizována synchronně jako součást aktualizace dokumentu (vložení, nahraďte, aktualizace a odstranění dokumentu v kolekci Azure Cosmos DB).

Konzistentní indexování podporuje konzistentní dotazy za cenu možné snížení zápisu propustnost. Toto snížení je funkce jedinečné cesty, které je třeba zpracovat a "úroveň konzistence". Konzistentní indexování režimu je určená pro "zápisu rychle dotaz okamžitě" úlohy.

**Opožděné**: index se asynchronně aktualizuje, pokud kolekci Azure Cosmos DB tichém, to znamená, pokud kapacita propustnosti kolekce není plně využívat k obsluze požadavků uživatele.  Všimněte si, že se mohou objevit nekonzistentní výsledky, protože data jsou požity a indexované pomalu. To znamená, že počet dotazů nebo výsledky konkrétní dotaz nemusí být konzistentní nebo repeatable v daný čas. 

Index je obvykle v opravný režimu ingestovaný daty. Doba provozu (TTL) s Lazy indexování, změní výsledek v indexu se vyřadit a znovu vytvořit. To usnadňuje výsledky počet a dotaz nekonzistentní v časovém intervalu. Většina účtů Azure Cosmos DB musí používat konzistentní indexování režim.

**Žádný**: kolekce, která má žádný index režimu neobsahuje index s ním spojená. To se často používá, pokud Azure Cosmos DB slouží jako úložiště klíč hodnota a dokumenty jsou dostupné jenom přes jejich vlastnost ID. 

> [!NOTE]
> Konfigurace zásady indexování s jako None má vedlejším účinkem vyřadit všechny existující index. Použijte, pokud vaše přístupové vzorce vyžadují jenom ID nebo vlastního odkazu.
> 
> 

Následující tabulka uvádí konzistence pro dotazy na základě indexování režim (konzistentní a Lazy) nakonfigurovaný pro kolekce a úroveň konzistence zadané pro požadavku dotazu. To platí pro dotazy, které jsou vytvářeny pomocí libovolné rozhraní: REST API, sady SDK, nebo prostřednictvím uložené procedury a triggery. 

|Konzistence|Indexování režim: konzistentní|Indexování režim: opožděné|
|---|---|---|
|Silné|Silné|Nahodilé|
|Typu s ohraničenou prošlostí|Typu s ohraničenou prošlostí|Nahodilé|
|Relace|Relace|Nahodilé|
|Nahodilé|Nahodilé|Nahodilé|

Azure Cosmos DB vrátí chybu pro dotazy na kolekce, které mají žádné indexování režimu. Dotazy mohou být provedeny stále jako kontroly prostřednictvím explicitní **x-ms-documentdb-enable kontroly** hlavičky v rozhraní REST API nebo **EnableScanInQuery** vyžádat možnost pomocí .NET SDK. Některé funkce dotazů, třeba ORDER BY, nejsou podporovány jako kontroly s **EnableScanInQuery**.

V následující tabulce jsou uvedeny konzistence pro dotazy na základě indexování režimu (konzistentní, Lazy a None) při **EnableScanInQuery** je zadán.

|Konzistence|Indexování režim: konzistentní|Indexování režim: opožděné|Indexování režim: žádné|
|---|---|---|---|
|Silné|Silné|Nahodilé|Silné|
|Typu s ohraničenou prošlostí|Typu s ohraničenou prošlostí|Nahodilé|Typu s ohraničenou prošlostí|
|Relace|Relace|Nahodilé|Relace|
|Nahodilé|Nahodilé|Nahodilé|Nahodilé|

Následující ukázka kódu ukazují, jak vytvořit kolekci Azure Cosmos DB pomocí .NET SDK konzistentní indexování na všechny vložení dokumentu.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Index cesty
Azure Cosmos DB modely jako stromy dokumentů JSON a index. Abyste mohli vyladit zásad pro cesty v rámci stromu. V rámci dokumenty můžete zvolit cesty, které chcete zahrnout nebo vyloučit z indexování. To nabízí vylepšené zápisu výkonu a nižší index úložiště pro scénáře, ve kterých jsou předem známé vzorům dotazů.

Index cesty začínat kořenu (/) a obvykle končit? operátor zástupný znak. To znamená, že existuje více možných hodnot pro předponu. Například sloužit vybrat * z řady F kde F.familyName = "Rodinu", musí obsahovat cestu index pro /familyName/? v zásadách indexu kolekce.

Index cesty můžete také použít \* operátor zástupný znak určit způsob chování pro rekurzivní cest v rámci předponu. Například/datové části / * umožňuje vyloučit všechno v datové části vlastnost z indexu.

Zde jsou obecné vzory pro zadání cesty index:

| Cesta                | Popis nebo používají                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Výchozí cesta pro kolekci. Rekurzivní a platí pro stromu celý dokument.                                                                                                                                                                                                                                   |
| / prop /?             | Index cesta zapotřebí pro zpracování dotazů takto (s typy Hash nebo rozsah, v uvedeném pořadí):<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop > 5<br><br>Vyberte z kolekce c Order c.prop                                                                       |
| / prop / *             | Index cesta u všech cest v rámci zadaného popisku. Funguje s následující dotazy<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop.subprop > 5<br><br>Vyberte z kolekce c WHERE c.prop.subprop.nextprop = "hodnota"<br><br>Vyberte z kolekce c Order c.prop         |
| / props / [] /?         | Cesta index vyžaduje iterace a připojení dotazy pro pole skalárních hodnot jako ["a", "b", "c"]:<br><br>Vyberte označit z značky ve collection.props kde značky = "hodnota"<br><br>Vyberte značku z kolekce c spojení značky v c.props kde značky > 5                                                                         |
| [] /subprop/ /props/? | Cesta index vyžaduje k obsluze iterace a spojení dotazy na pole objektů, jako jsou [{subprop: "a"}, {subprop: "b"}]:<br><br>Vyberte označit z značky ve collection.props kde tag.subprop = "hodnota"<br><br>Vyberte označit z kolekce c spojení značky v c.props kde tag.subprop = "hodnota"                                  |
| / prop/subprop /?     | Index cesta zapotřebí pro zpracování dotazů (s typy Hash nebo rozsah, v uvedeném pořadí):<br><br>Vyberte z kolekce c WHERE c.prop.subprop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Když nastavíte cesty vlastní index, je nutné zadat výchozí pravidlo indexování pro strom celý dokument, který je označený jako speciální cestu "/ *". 
> 
> 

Následující příklad konfiguruje cestu, která s indexem rozsahu a hodnotu vlastní přesnost 20 bajtů:

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


### <a name="index-data-types-kinds-and-precisions"></a>Index datové typy, typy a přesnosti
Když konfigurujete zásady indexování pro cestu, mají několik možností. Můžete určit jeden nebo více indexování definice pro každý cestu:

* **Datový typ**: řetězec, čísla, bod, mnohoúhelníku nebo LineString (může obsahovat jenom jeden záznam za datový typ na cestě).
* **Index typu**: Hash (dotazy na rovnost), rozsah (rovnosti, rozsah nebo dotazy ORDER BY) nebo Spatial (prostorových dotazy).
* **Přesnost**: index pro Hash, to se liší od 1 do 8 pro čísla i řetězce. Výchozí hodnota je 3. Pro rozsah index tato hodnota může být -1 (Maximální přesnost). Můžete se liší mezi 1 a 100 (Maximální přesnost) pro řetězec nebo číselné hodnoty.

#### <a name="index-kind"></a>Typ indexu
Azure Cosmos DB podporuje Hash index a typy index rozsah pro každou cestu, která mohou být konfigurovány pro datové typy řetězec nebo číslo, nebo obojí.

* **Hodnota hash** podporuje efektivní rovnosti a dotazy spojení. Pro většinu případy použití nepotřebujete indexy Hash vyšší přesností než je výchozí hodnota 3 bajtů. Datový typ může být řetězec nebo číslo.
* **Rozsah** podporuje dotazy na rovnost efektivní, dotazy na rozsah (pomocí >, <>, =, < =,! =) a dotazy ORDER BY. Dotazy ORDER By ve výchozím nastavení také vyžadují maximální index přesnost (-1). Datový typ může být řetězec nebo číslo.

Druh prostorový index Azure Cosmos DB také podporuje pro každé cestu, kterou lze zadat pro datové typy bodu, mnohoúhelníku nebo LineString. Hodnota v zadané cestě musí být platný fragment GeoJSON jako `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Prostorové** podporuje efektivní prostorových (v rámci a vzdálenost) dotazy. Datový typ může být bodu, mnohoúhelníku nebo LineString.

> [!NOTE]
> Azure Cosmos DB podporuje automatické indexování bodu mnohoúhelníku a LineString datových typů.
> 
> 

Toto jsou typy podporované index a příklady dotazů, které můžou být použita k obsluze:

| Typ indexu | Popis nebo používají                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hodnota hash       | Hodnoty hash přes/prop /? (nebo /) slouží k efektivní slouží následující dotazy:<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Hodnota hash přes/props / [] /? (nebo / nebo/props /) slouží k efektivní slouží následující dotazy:<br><br>Vyberte označit z kolekce c spojení značky v c.props kde značky = 5                                                                                                                       |
| Rozsah      | Rozsah přes/prop /? (nebo /) slouží k efektivní slouží následující dotazy:<br><br>Vyberte z kolekce c WHERE c.prop = "hodnota"<br><br>Vyberte z kolekce c WHERE c.prop > 5<br><br>Vyberte z kolekce c Order c.prop                                                                                                                                                                                                              |
| Spatial     | Rozsah přes/prop /? (nebo /) slouží k efektivní slouží následující dotazy:<br><br>Vyberte z kolekce c<br><br>KDE ST_DISTANCE (c.prop, {"typ": "Bod", "coordinates": [0.0, 10.0]}) < 40<br><br>Vyberte z kolekce c kde ST_WITHIN(c.prop, {"type": "Polygon",...}) – s v bodech povoleno indexování<br><br>Vyberte z kolekce c kde ST_WITHIN({"type": "Point",...}, c.prop) – s indexování na mnohoúhelníky povoleno              |

Ve výchozím nastavení, je vrácena chyba pro dotazy s rozsahu operátory, jako > =, pokud neexistuje žádné rozsah index (všechny přesnost) signál, že kontroly může být potřeba sloužit dotazu. Dotazy na rozsah můžete provést bez indexem rozsahu pomocí **x-ms-documentdb-enable kontroly** hlavičky v rozhraní REST API nebo **EnableScanInQuery** vyžádat možnost pomocí .NET SDK. Pokud jsou v dotazu, Azure Cosmos DB můžete index filtr pro všechny ostatní filtry, vrátí se žádná chyba.

Použít stejná pravidla pro prostorových dotazů. Ve výchozím nastavení je vrácena chyba prostorových dotazů, pokud neexistuje žádné prostorový index, a neexistují žádné filtry, které se dají obsluhovat z indexu. Nemohou být vykonány jako kontrolu pomocí **x-ms-documentdb-enable kontroly** nebo **EnableScanInQuery**.

#### <a name="index-precision"></a>Index přesnost
Chcete-li kompromis mezi režie index úložiště a výkon dotazů můžete přesnost index. Pro čísla doporučujeme používat tuto výchozí konfiguraci přesnost-1 (maximální). Protože jsou čísla 8 bajtů ve formátu JSON, jde o ekvivalent konfigurace 8 bajtů. Výběr na nižší hodnotu pro přesnost, například 1 až 7, znamená, že hodnoty v rámci některé rozsahy namapovány na stejný index položky. Proto můžete omezit prostor úložiště index, ale při provádění dotazu může mít zpracovat další dokumenty. V důsledku toho spotřebuje další propustnost v jednotek žádosti.

Přesnost konfigurace indexu má více praktické aplikace s rozsahy řetězec. Protože řetězce může být jakékoli libovolné délky, volba přesnost index může ovlivnit výkon dotazů rozsah řetězec. Množství prostoru úložiště indexu, které je nutné také může ovlivnit. Řetězec rozsah indexy se dá nakonfigurovat s 1 až 100 nebo -1 (maximální). Pokud chcete provádět dotazy ORDER BY pro vlastnosti string, je nutné zadat přesností-1 pro odpovídající cesty.

Prostorové indexy vždy použít výchozí index přesnost pro všechny typy (Point, LineString a mnohoúhelníku). Nebylo možné přepsat výchozí index přesnost pro prostorové indexy. 

Následující příklad ukazuje, jak zvýšit přesnost pro rozsah indexy v kolekci pomocí .NET SDK. 

**Vytvořte kolekci s přesností vlastním indexu**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB vrátí chybu, pokud dotaz používá ORDER BY, ale nemá indexem rozsahu proti dotazované cestu s maximální přesnost. 
> 
> 

Podobně můžete úplně vyloučit cesty z indexování. Další příklad ukazuje, jak vyloučit celý oddíl dokumentů ( *podstrom*) z indexování pomocí \* operátor zástupný znak.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Vyjádřit výslovný souhlas a vyjádření výslovného nesouhlasu s indexování
Můžete zvolit, jestli chcete kolekci automaticky indexuje všechny dokumenty. Ve výchozím nastavení jsou automaticky indexovány všechny dokumenty, ale můžete vypnout automatické indexování. Když je vypnutý indexování, dokumenty jsou přístupné pouze prostřednictvím jejich odkazů na sebe sama nebo dotazy pomocí dokumentů ID.

S automatické indexování, vypnutý, můžete stále selektivně přidat pouze konkrétní dokumentů do indexu. Naopak můžete nechat automatické indexování na a selektivně zvolte vyloučit konkrétní dokumenty. Indexování zapnout nebo vypnout konfigurace jsou užitečné v případě, že máte jenom podmnožinu dokumentů, které musí být dotazována.

Následující příklad ukazuje, jak zahrnout dokumentu explicitně pomocí [SQL rozhraní API .NET SDK](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) a [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx) vlastnost.

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Upravit zásady indexování kolekce
V Azure DB Cosmos můžete provést změny zásady indexování kolekce za chodu. Změnu indexování zásady na kolekci Azure Cosmos DB může vést k změně ve tvaru index. Změna ovlivní cesty, které lze indexovat, jejich přesnost a model konzistence indexu sám sebe. Změnu zásady indexování efektivně vyžaduje transformaci původní index do nového indexu. 

**Transformace indexu online**

![Indexování fungování – Azure Cosmos DB indexu online transformace](./media/indexing-policies/index-transformations.png)

Transformace indexu jsou vytvářeny online. To znamená, že dokumenty indexované podle staré zásad jsou transformovány efektivně na nové zásady *bez ovlivnění dostupnosti zápisu nebo zřízené propustnosti* kolekce. Konzistence operacích čtení a zápisu provedené pomocí rozhraní REST API sady SDK, vliv na jeden nebo prostřednictvím uložených procedur a aktivačních událostí není během index transformace. Pokud provedete zásady indexování změnit neexistuje žádné snížení výkonu nebo výpadek aplikace.

Ale po dobu, která index transformaci je průběh, jsou dotazy nakonec byl konzistentní bez ohledu na to indexování konfiguraci režimu (konzistentní nebo Lazy). To platí také pro dotazy od všech rozhraní: REST API, sady SDK a v rámci uložené procedury a triggery. Stejně jako s Lazy indexování, index je provedena transformace asynchronně na pozadí na replikách pomocí k výměně za chodu prostředky, které jsou k dispozici pro konkrétní repliky. 

Transformace indexu jsou také vytvářeny na místě. Azure Cosmos DB není zachována dvě kopie index a přepnutí na původní index s tímto novým připojením. To znamená, že žádné další místo na disku je požadováno, nebo využívat v kolekcích, když dojde k indexu transformace.

Když změníte zásady indexování, jsou změny přesunout z původní index k novému primárně podle indexování režimu konfigurace. Konfigurace indexování režimu hrají roli větší než ostatní hodnoty jako zahrnout nebo vyloučit cesty, typy index a přesnosti. 

Pokud vaše staré a nové zásady obě používají konzistentní indexování, provede Azure Cosmos DB transformaci indexu online. Nelze použít jiný indexování změny zásad, která má konzistentní indexování režimu, zatímco probíhá transformace. Však můžete přesunout na Lazy nebo hodnotu None probíhá indexování režimu při transformaci: 

* Když přesouváte Lazy, změna zásad index je hned platná. Azure Cosmos DB spustí znovu vytvořit index asynchronně. 
* Když přesouváte na hodnotu None, index se okamžitě ukončí. Přesun na hodnotu None je užitečné, pokud chcete zrušit transformaci v průběhu a začít pracovat s jiný zásady indexování. 

Následující fragment kódu ukazuje, jak upravit indexování zásady kolekce z konzistentní indexování režimu Opožděné indexování režimu. Pokud používáte sady SDK rozhraní .NET, můžete ji indexování změn zásad pomocí nového **ReplaceDocumentCollectionAsync** metoda.

**Upravit zásady indexování z konzistentní k Lazy**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Sledovat průběh index transformace**

Procento průběh transformace index do konzistentního indexu můžete sledovat pomocí **IndexTransformationProgress** vlastnost odpověď z **ReadDocumentCollectionAsync** volání. Jiné sady SDK a rozhraní REST API podporují ekvivalentní vlastnosti a metody pro provádění změn v zásadách indexování. Můžete zkontrolovat průběh transformaci index do konzistentního indexu voláním **ReadDocumentCollectionAsync**: 

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
> * **IndexTransformationProgress** vlastnost lze použít pouze v případě, že transformace konzistentní indexu. Použití **ResourceResponse.LazyIndexingProgress** vlastnost pro sledování transformace opožděné indexu.
> * **IndexTransformationProgress** a **LazyIndexingProgress** vlastnosti jsou naplněny jenom pro kolekce bez oddílů, který je kolekce, která je vytvořen bez klíče oddílu.
>

Přesunutím na hodnotu None indexování režim můžete vyřadit index pro kolekci. Pokud chcete zrušit transformaci v průběhu a okamžitě začít nový, může to být užitečné provozní nástroj.

**Vyřaďte index pro kolekci**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Pokud by provedete indexování změny zásad Azure Cosmos DB kolekce? Tady jsou nejběžnější případy použití:

* Poskytovat konzistentní výsledky při běžném provozu, ale vrátit zpět do režimu Opožděné indexování během hromadné importy dat.
* Začít používat nové funkce indexování na aktuální kolekce Azure Cosmos DB. Můžete například použít geoprostorové dotazování, který vyžaduje typ prostorového indexu nebo ORDER BY / řetězec rozsah dotazy, které vyžadují řetězec index typu rozsah.
* Ruční – vybrat vlastnosti pro indexování a časem změnit.
* Vyladění indexování přesnost pro zlepšení výkonu dotazů nebo ke snížení úložiště využívat.

> [!NOTE]
> Chcete-li upravit zásady indexování pomocí **ReplaceDocumentCollectionAsync**, musíte použít verzi 1.3.0 nebo novější verzi .NET SDK.
> 
> Pro index transformace na úspěšně dokončit zkontrolujte, zda je dostatek volného místa k dispozici v kolekci. Pokud kolekce dosáhne kvóta úložiště, transformaci index je pozastavena. Index transformace automaticky obnoví Pokud úložiště je k dispozici, například pokud odstraníte některé dokumenty.
> 
> 

## <a name="performance-tuning"></a>Ladění výkonu
Rozhraní API SQL poskytují informace o metrik výkonu, jako je například úložiště indexu používá a náklady na propustnost (jednotek žádosti) pro všechny operace. Tyto informace můžete použít k porovnání různé zásady indexování a pro optimalizaci výkonu.

Chcete-li zkontrolovat kvótu úložiště a využití kolekce, spusťte **HEAD** nebo **získat** požadavek na prostředek kolekce. Zkontrolujte **x-ms požadavku quota** a **x-ms požadavku využití** hlavičky. V sadě SDK .NET [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) a [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) vlastnosti v [ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) obsahovat tyto odpovídající hodnoty.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


K měření režii indexování na každou operaci zápisu (vytvoření, aktualizace nebo odstranění), zkontrolujte **x-ms požadavku poplatků** záhlaví (nebo ekvivalentní [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) vlastnost v [ ResourceResponse < T\> ](http://msdn.microsoft.com/library/dn799209.aspx) v sadě SDK .NET) k měření počtu jednotek žádosti, které jsou uplatníte tyto operace.

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
Změnu schématu pro zásady indexování byla zavedena července 7 2015, pomocí rozhraní REST API verze 2015-06-03. Odpovídající třídy ve verzích sady SDK mají nové implementace tak, aby odpovídala schématu. 

Následující změny byly implementovány ve specifikaci JSON:

* Indexování zásad podporuje rozsah indexů pro řetězce.
* Každá cesta může mít několik definic indexu. Může mít jeden pro každý typ dat.
* Indexování přesnost podporuje 1 až 8 pro čísla, 1 až 100 pro řetězce a -1 (Maximální přesnost).
* Segmenty cesty nevyžadují dvojité uvozovky, abyste se vyhnuli každá cesta zahrnovat. Například můžete přidat cestu pro   **/název /?** místo **/ "title" /?**.
* Kořenová cesta, která představuje "všechny cesty" může být reprezentován jako **/ \*** (kromě **/**).

Pokud kódu máte s vlastní zásady indexování napsané pomocí .NET SDK verze 1.1.0 nebo starší verzi této kolekce zřizuje, přesunout do verze 1.2.0, sady SDK, musíte změnit kód aplikace pro zpracování těchto změn. Pokud nemáte kód, který nakonfiguruje zásady indexování nebo pokud plánujete dál používat starší verze sady SDK, je potřeba žádné změny.

Praktické porovnání tady je příklad vlastní zásady indexování zapsány pomocí rozhraní REST API verze 2015-06-03, za nímž následuje stejné zásady indexování zapsány pomocí starší verze 2015-04-08 REST API.

**Aktuální indexování zásad JSON (REST API verze 2015-06-03)**

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


**Dříve indexování zásad JSON (REST API verze 2015-04-08)**

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
Ukázky správy zásad index a další informace o Azure Cosmos DB dotazovací jazyk v následujících tématech:

* [Ukázky kódu správu index rozhraní SQL API .NET](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Operace SQL API REST kolekce](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Dotaz s SQL](sql-api-sql-query.md)

