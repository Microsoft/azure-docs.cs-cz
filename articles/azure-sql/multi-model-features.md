---
title: Možnosti s více modely
description: Microsoft Azure SQL vám umožní pracovat s více datovými modely ve stejné databázi.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: b16a2fc9f107a8420fb7d05667807a869fa3e00a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172753"
---
# <a name="multi-model-capabilities-of-azure-sql-database--sql-managed-instance"></a>Možnosti více modelů Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Databáze s více modely umožňují ukládat a pracovat s daty reprezentovanými ve více formátech dat, jako jsou relační data, grafy, JSON/XML dokumenty, páry klíč-hodnota atd.

## <a name="when-to-use-multi-model-capabilities"></a>Kdy použít možnosti s více modely

[Rodina produktů Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md) je navržená tak, aby fungovala s relačním modelem, který poskytuje nejlepší výkon ve většině případů pro celou řadu aplikací pro obecné účely. Řada produktů Azure SQL se ale neomezí jenom na relační data. Řada produktů Azure SQL vám umožní používat celou řadu nerelačních formátů, které jsou pevně integrované do relačního modelu.
Měli byste zvážit použití možností více modelů v produktech SQL Azure v následujících případech:

- Máte nějaké informace nebo struktury, které jsou lépe vhodné pro modely NoSQL a nechcete používat samostatnou databázi NoSQL.
- Většina vašich dat je vhodná pro relační model a je třeba vytvořit model některých částí dat ve stylu NoSQL.
- Chcete využít bohatý jazyk Transact-SQL k dotazování a analýze relačních i NoSQL dat a integrovat je s celou řadou nástrojů a aplikací, které můžou používat jazyk SQL.
- Chcete použít databázové funkce, jako jsou například [technologie v paměti](in-memory-oltp-overview.md) , aby se zlepšil výkon při analýze nebo zpracování datových struktur NoSQL, použijte [transakční replikaci](managed-instance/replication-transactional-overview.md) nebo [čitelné repliky](database/read-scale-out.md) k vytvoření kopie dat na druhém místě a přesměrujte některé analytické úlohy z primární databáze.

## <a name="overview"></a>Přehled

Řada produktů Azure SQL nabízí následující funkce pro více modelů:

- [Funkce grafu](#graph-features) umožňují znázornit vaše data jako sadu uzlů a hran a používat standardní dotazy Transact-SQL rozšířené s `MATCH` operátorem grafu pro dotazování dat grafu.
- [Funkce JSON](#json-features) umožňují VKLÁDAT dokumenty JSON do tabulek, transformovat relační data do dokumentů JSON a naopak. Můžete použít standardní jazyk Transact-SQL rozšířený s funkcemi JSON pro analýzu dokumentů a pomocí neclusterovaných indexů, indexů columnstore nebo paměťově optimalizovaných tabulek pro optimalizaci vašich dotazů.
- [Prostorové funkce](#spatial-features) umožňují ukládat geografická a geografická data, indexovat je pomocí prostorových indexů a načítat data pomocí prostorových dotazů.
- [Funkce XML](#xml-features) umožňují ukládat a indexovat data XML ve vaší databázi a používat nativní operace XQuery/XPath pro práci s daty XML. Řada produktů SQL Azure má specializovaný integrovaný modul dotazů XML, který zpracovává data XML.
- [Páry klíč-hodnota](#key-value-pairs) nejsou explicitně podporovány jako speciální funkce, protože páry klíč-hodnota lze nativně modelovat jako tabulky se dvěma sloupci.

  > [!Note]
  > Pro přístup k datům, která jste uložili v databázi, můžete použít výraz cesty JSON, výrazy XQuery/XPath, prostorové funkce a výrazy dotazu Transact-SQL ve stejném dotazu Transact-SQL. Také jakýkoli nástroj nebo programovací jazyk, který může spouštět dotazy Transact-SQL, může také použít toto rozhraní dotazu pro přístup k datům s více modely. Jedná se o klíčový rozdíl v porovnání s databázemi s více modely, jako je například [Azure Cosmos DB](../cosmos-db/index.yml) , která poskytuje specializované rozhraní API pro různé datové modely.

V následujících částech se dozvíte o nejdůležitějších funkcích pro více modelů v řadě produktů SQL Azure.

## <a name="graph-features"></a>Funkce grafů

Rodina produktů Azure SQL nabízí možnosti databáze grafu pro modelování vztahů m:n v databázi. Graf je kolekce uzlů (nebo vrcholů) a hran (nebo relací). Uzel představuje entitu (například osobu nebo organizaci) a hrana představuje vztah mezi dvěma uzly, které se připojují (například jako například nebo přátelé). Tady je několik funkcí, které tvoří databázi grafů jedinečné:

- Hrany nebo relace jsou prvními entitami třídy v databázi grafů a můžou mít přidružené atributy nebo vlastnosti.
- Jeden okraj může pružně připojit více uzlů v databázi grafu.
- Rychlé porovnávání vzorů a navigační dotazy s vícenásobným směrováním můžete snadno snadno využít.
- Snadno přenositelný a polymorfní dotazy můžete snadno vyjádřit.

[Relace grafu a možnosti dotazování v grafu](/sql/relational-databases/graphs/sql-graph-overview) jsou integrované do jazyka Transact-SQL a získají výhody použití databázového stroje SQL Server jako základní systém pro správu databází.

### <a name="when-to-use-a-graph-capability"></a>Kdy použít funkci grafu

Neexistuje žádná databáze grafu, kterou by bylo možné dosáhnout, což nelze dosáhnout pomocí relační databáze. Databáze grafu ale může usnadnit vyjádření určitých dotazů. Vaše rozhodnutí, které si zvolíte, může být založeno na následujících faktorech:

- Modelujte hierarchická data, kde jeden uzel může mít více nadřazených objektů, takže HierarchyId se nedá použít.
- Model má vaši aplikaci složitou relaci m:n, Při vývoje aplikace jsou přidány nové relace.
- Potřebujete analyzovat vzájemně propojená data a relace.

## <a name="json-features"></a>Funkce JSON

Rodina produktů Azure SQL umožňuje analyzovat a dotazovat data reprezentovaná ve formátu JavaScript Object Notation [(JSON)](https://www.json.org/) a exportovat relační data jako text JSON.

JSON je oblíbený formát dat, který slouží k výměně dat v moderních webových a mobilních aplikacích. JSON se také používá k ukládání částečně strukturovaných dat do souborů protokolu nebo v databázích NoSQL, jako je [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Mnohé webové služby REST vracejí výsledky formátované jako text JSON nebo přijímají data formátovaná jako JSON. Většina služeb Azure, jako je [Azure kognitivní hledání](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)a [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) mít koncové body REST, které vracejí nebo využívají JSON.


Řada produktů Azure SQL umožňuje snadnou práci s daty JSON a integraci databáze s moderními službami a poskytuje následující funkce pro práci s daty JSON:

![Funkce JSON](./media/multi-model-features/image_1.png)

Pokud máte text JSON, můžete extrahovat data z JSON nebo ověřit, jestli je JSON správně formátovaný, pomocí integrovaných funkcí [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql)a ve formátu [JSON.](/sql/t-sql/functions/isjson-transact-sql) Funkce [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql) umožňuje aktualizovat hodnotu uvnitř textu JSON. Pro pokročilejší dotazování a analýzu může funkce [OPENJSON](/sql/t-sql/functions/openjson-transact-sql) transformovat pole objektů JSON do sady řádků. Libovolný dotaz SQL může být proveden na vrácené sadě výsledků. Nakonec je k dispozici klauzule [for JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server) , která umožňuje formátovat data uložená v relačních tabulkách jako text JSON.

Další informace najdete v tématu [jak pracovat s daty JSON](database/json-features.md).
[JSON](/sql/relational-databases/json/json-data-sql-server) je základní funkce SQL Server databázového stroje.

### <a name="when-to-use-a-json-capability"></a>Kdy použít funkci JSON

Modely dokumentů se dají použít místo relačních modelů v některých specifických scénářích:

- Vysoká normalizace schématu nepřináší významné výhody, protože přistupujete ke všem polím objektů najednou nebo dokud neaktualizujete normalizované části objektů. Normalizovaný model však zvyšuje složitost vašich dotazů z důvodu velkého počtu tabulek, které je třeba připojit k získání dat.
- Pracujete s aplikacemi, které nativně používají Dokumenty JSON, jsou komunikační nebo datové modely a nechcete zavádět další vrstvy, které transformují relační data do formátu JSON a naopak.
- Je potřeba zjednodušit datový model pomocí nenormalizace podřízených tabulek nebo vzorů entit-Object-hodnota.
- Je nutné načíst nebo exportovat data uložená ve formátu JSON bez dalšího nástroje, který data analyzuje.

## <a name="spatial-features"></a>Prostorové funkce

Prostorová data představují informace o fyzickém umístění a tvaru geometrických objektů. Tyto objekty můžou být umístění bodů nebo složitější objekty, jako jsou země/oblasti, silnice nebo jezera.

 Dva podporované typy prostorových dat: 

- Typ geometrie reprezentuje data v Euclidean (plochý) souřadnicový systém.
- Zeměpisný typ představuje data v systému souřadnic pro kulatou zemi.

K dispozici je řada prostorových objektů, které je možné použít v řadě produktů Azure SQL, umožňuje analyzovat a dotazovat data reprezentovaná ve formátu JavaScript Object Notation [(JSON)](https://www.json.org/) a exportovat relační data jako text JSON.
například [Point](/sql/relational-databases/spatial/point), [LineString](/sql/relational-databases/spatial/linestring), [mnohoúhelník](/sql/relational-databases/spatial/polygon)atd.

Řada produktů Azure SQL nabízí také specializované [prostorové indexy](/sql/relational-databases/spatial/spatial-indexes-overview) , které lze použít ke zlepšení výkonu prostorových dotazů.

[Prostorová podpora](/sql/relational-databases/spatial/spatial-data-sql-server) je základní funkcí SQL Server databázového stroje.

## <a name="xml-features"></a>Funkce XML

Databázový stroj SQL Server poskytuje výkonnou platformu pro vývoj bohatých aplikací pro správu částečně strukturovaných dat. Podpora pro XML je integrovaná do všech komponent databázového stroje a obsahuje následující:

- Datový typ XML. Hodnoty XML lze ukládat nativně do sloupce datového typu XML, které lze zadat v závislosti na kolekci schémat XML nebo netypových prázdných. Sloupec XML můžete indexovat.
- Možnost zadat dotaz XQuery na data XML uložená ve sloupcích a proměnných typu XML. Funkce jazyka XQuery lze použít v jakémkoli dotazu Transact-SQL, který přistupuje k libovolnému datovému modelu, který používáte ve vaší databázi.
- Automaticky Indexujte všechny prvky v dokumentech XML pomocí [primárního indexu XML](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) nebo zadejte přesné cesty, které by měly být indexovány pomocí [sekundárního indexu XML](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET, která umožňuje hromadné načítání dat XML.
- Umožňuje transformovat relační data na formát XML.

[XML](/sql/relational-databases/xml/xml-data-sql-server) je základní funkce SQL Server databázového stroje.

### <a name="when-to-use-an-xml-capability"></a>Kdy použít funkci XML

Modely dokumentů se dají použít místo relačních modelů v některých specifických scénářích:

- Vysoká normalizace schématu nepřináší významné výhody, protože přistupujete ke všem polím objektů najednou nebo dokud neaktualizujete normalizované části objektů. Normalizovaný model však zvyšuje složitost vašich dotazů z důvodu velkého počtu tabulek, které je třeba připojit k získání dat.
- Pracujete s aplikacemi, které nativně používají XML dokumenty, jsou komunikační nebo datové modely a nechcete zavádět další vrstvy, které transformují relační data do XML a naopak.
- Je potřeba zjednodušit datový model pomocí nenormalizace podřízených tabulek nebo vzorů entit-Object-hodnota.
- Je nutné načíst nebo exportovat data uložená ve formátu XML bez dalšího nástroje, který data analyzuje.

## <a name="key-value-pairs"></a>Páry klíč-hodnota

Rodina produktů Azure SQL nemá specializované typy nebo struktury, které podporují páry klíč-hodnota, protože struktury klíčových hodnot můžou být nativně reprezentované jako standardní relační tabulky:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Tuto strukturu klíč-hodnota můžete přizpůsobit tak, aby vyhovovala vašim potřebám bez jakýchkoli omezení. Jako příklad může být hodnota dokumentu XML namísto `nvarchar(max)` typu, pokud je hodnota dokument JSON, můžete vložit `CHECK` omezení, které ověří platnost obsahu JSON. V dalších sloupcích můžete zadat libovolný počet hodnot, které se vztahují k jednomu klíči, Přidat vypočítané sloupce a indexy pro zjednodušení a optimalizaci přístupu k datům, definovat tabulku jako paměť/optimalizované tabulky pouze pro zajištění lepšího výkonu atd.

Podívejte se, [jak BWin používá In-Memory OLTP k dosažení nedřívějšího výkonu a škálování](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale) pro své řešení ASP.NET pro ukládání do mezipaměti, které dosáhlo 1.200.000 dávek za sekundu, jako příklad, jak se relační model dá efektivně použít jako řešení páru klíč-hodnota v praxi.

## <a name="next-steps"></a>Další kroky

Možnosti více modelů v řadě produktů SQL Azure jsou také základními funkcemi SQL Server databázového stroje, které jsou sdíleny v rámci řady produktů SQL Azure. Další informace o těchto funkcích najdete na stránce dokumentace k relačním databázím SQL:

- [Zpracování grafu](/sql/relational-databases/graphs/sql-graph-overview)
- [Data JSON](/sql/relational-databases/json/json-data-sql-server)
- [Prostorová podpora](/sql/relational-databases/spatial/spatial-data-sql-server)
- [Data XML](/sql/relational-databases/xml/xml-data-sql-server)
