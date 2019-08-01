---
title: Azure SQL Database možností více modelů | Microsoft Docs
description: Azure SQL Database vám umožní pracovat s více datovými modely ve stejné databázi.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/17/2018
ms.openlocfilehash: e319daf322d688828c7d05d78dacd2359273223f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567121"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Možnosti pro více modelů Azure SQL Database

Databáze s více modely umožňují ukládat a pracovat s daty reprezentovanými ve více formátech dat, jako jsou relační data, grafy, JSON/XML dokumenty, páry klíč-hodnota atd.

## <a name="when-to-use-multi-model-capabilities"></a>Kdy použít možnosti s více modely

Azure SQL Database je navržená tak, aby fungovala s relačním modelem, který poskytuje nejlepší výkon ve většině případů pro celou řadu aplikací pro obecné účely. Azure SQL Database však není omezen pouze na relační data. Azure SQL Database vám umožní používat celou řadu nerelačních formátů, které jsou pevně integrované do relačního modelu.
Měli byste zvážit použití možností více modelů Azure SQL Database v následujících případech:
- Máte nějaké informace nebo struktury, které jsou lépe vhodné pro modely NoSQL a nechcete používat samostatnou databázi NoSQL.
- Většina vašich dat je vhodná pro relační model a je třeba vytvořit model některých částí dat ve stylu NoSQL.
- Chcete využít bohatý jazyk Transact-SQL k dotazování a analýze relačních i NoSQL dat a integrovat je s celou řadou nástrojů a aplikací, které můžou používat jazyk SQL.
- Chcete použít databázové funkce, jako jsou například [technologie v paměti](sql-database-in-memory.md) , aby se zlepšil výkon při analýze nebo zpracování NoSQL dat strucutres, použití [transakční replikace](sql-database-managed-instance-transactional-replication.md) nebo [čitelné repliky](sql-database-read-scale-out.md) k vytvoření kopie vašich dat na druhé místo a přesměruje některé analytické úlohy z primární databáze.

## <a name="overview"></a>Přehled

Azure SQL poskytuje následující funkce pro více modelů:
- [Funkce grafu](#graph-features) umožňují znázornit vaše data jako sadu uzlů a hran a používat standardní dotazy Transact-SQL rozšířené s operátorem grafu `MATCH` pro dotazování dat grafu.
- [Funkce JSON](#json-features) umožňují VKLÁDAT dokumenty JSON do tabulek, transformovat relační data do dokumentů JSON a naopak. Můžete použít standardní jazyk Transact-SQL rozšířený s funkcemi JSON pro analýzu dokumentů a pomocí neclusterovaných indexů, indexů columnstore nebo paměťově optimalizovaných tabulek pro optimalizaci vašich dotazů.
- [Prostorové funkce](#spatial-features) umožňují ukládat geografická a geografická data, indexovat je pomocí prostorových indexů a načítat data pomocí prostorových dotazů.
- [Funkce XML](#xml-features) umožňují ukládat a indexovat data XML ve vaší databázi a používat nativní operace XQuery/XPath pro práci s daty XML. Azure SQL Database má specializovaný integrovaný dotazovací modul XML, který zpracovává data XML.
- [Páry klíč-hodnota](#key-value-pairs) nejsou explicitně podporované jako speciální funkce, protože Paříž klíč-hodnota lze nativně modelovat jako tabulky se dvěma sloupci.

  > [!Note]
  > Pro přístup k datům, která jste uložili v databázi, můžete použít výraz cesty JSON, výrazy XQuery/XPath, prostorové funkce a výrazy dotazu Transact-SQL ve stejném dotazu Transact-SQL. Také jakýkoli nástroj nebo programovací jazyk, který může spouštět dotazy Transact-SQL, může také použít toto rozhraní dotazu pro přístup k datům s více modely. Jedná se o klíčový rozdíl v porovnání s databázemi s více modely, jako je například [Azure Cosmos DB](/azure/cosmos-db/) , která poskytuje specializované rozhraní API pro různé datové modely.

V následujících částech se dozvíte o nejdůležitějších možnostech více modelů Azure SQL Database.

## <a name="graph-features"></a>Funkce grafů

Azure SQL Database nabízí možnosti databáze grafu pro modelování vztahů m:n v databázi. Graf je kolekce uzlů (nebo vrcholů) a hran (nebo relací). Uzel představuje entitu (například osobu nebo organizaci) a hrana představuje vztah mezi dvěma uzly, které se připojují (například jako například nebo přátelé). Tady je několik funkcí, které tvoří databázi grafů jedinečné:
- Hrany nebo relace jsou prvními entitami třídy v databázi grafů a můžou mít přidružené atributy nebo vlastnosti.
- Jeden okraj může pružně připojit více uzlů v databázi grafu.
- Rychlé porovnávání vzorů a navigační dotazy s vícenásobným směrováním můžete snadno snadno využít.
- Snadno přenositelný a polymorfní dotazy můžete snadno vyjádřit.

Relace grafu a možnosti dotazování v grafu jsou integrované do jazyka Transact-SQL a získají výhody použití SQL Server jako základní systém pro správu databází.
[Zpracování grafů](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) je funkce jádro SQL Server databázového stroje, takže můžete najít další informace o zpracování grafu.

### <a name="when-to-use-a-graph-capability"></a>Kdy použít funkci grafu

Neexistuje žádná databáze grafu, kterou by bylo možné dosáhnout, což nelze dosáhnout pomocí relační databáze. Databáze grafu ale může usnadnit vyjádření určitých dotazů. Vaše rozhodnutí, které si zvolíte, může být založeno na následujících faktorech:

- Modelujte hierarchická data, kde jeden uzel může mít více nadřazených objektů, takže HierarchyId se nedá použít.
- Model má vaši aplikaci složitou relaci m:n, Při vývoje aplikace jsou přidány nové relace.
- Potřebujete analyzovat vzájemně propojená data a relace.

## <a name="json-features"></a>Funkce JSON

Azure SQL Database umožňuje analyzovat a dotazovat data reprezentovaná ve formátu JavaScript Object Notation [(JSON)](https://www.json.org/) a exportovat relační data jako text JSON.

JSON je oblíbený formát dat, který slouží k výměně dat v moderních webových a mobilních aplikacích. JSON se také používá k ukládání částečně strukturovaných dat do souborů protokolu nebo v databázích NoSQL, jako je [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Mnohé webové služby REST vracejí výsledky formátované jako text JSON nebo přijímají data formátovaná jako JSON. Většina služeb Azure, například [Azure Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)a [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) mít koncové body REST, které vracejí nebo využívají JSON.

Azure SQL Database umožňuje snadnou práci s daty JSON a integraci databáze s moderními službami. Azure SQL Database poskytuje následující funkce pro práci s daty JSON:

![Funkce JSON](./media/sql-database-json-features/image_1.png)

Pokud máte text JSON, můžete extrahovat data z JSON nebo ověřit, jestli je formát JSON správně formátovaný, pomocí integrovaných funkcí [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)a. [JSON](https://msdn.microsoft.com/library/dn921896.aspx). Funkce [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) umožňuje aktualizovat hodnotu uvnitř textu JSON. Pro pokročilejší dotazování a analýzu může funkce [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) transformovat pole objektů JSON do sady řádků. Libovolný dotaz SQL může být proveden na vrácené sadě výsledků. Nakonec je k dispozici klauzule [for JSON](https://msdn.microsoft.com/library/dn921882.aspx) , která umožňuje formátovat data uložená v relačních tabulkách jako text JSON.

Další informace najdete v tématu [jak pracovat s daty JSON v azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) je základní SQL Server funkce databázového stroje, takže můžete najít další informace o funkci JSON tam.

### <a name="when-to-use-a-json-capability"></a>Kdy použít funkci JSON

Modely dokumentů se dají použít místo relačních modelů v některých specifických scénářích:
- Vysoká normalizace schématu nepřináší významné výhody, protože přistupujete ke všem polím objektů najednou nebo dokud neaktualizujete normalizované části objektů. Normalizovaný model však zvyšuje složitost vašich dotazů z důvodu velkého počtu tabulek, které je třeba připojit k získání dat.
- Pracujete s aplikacemi, které nativně používají Dokumenty JSON, jsou komunikační nebo datové modely a nechcete zavádět další vrstvy, které transformují relační data do formátu JSON a naopak.
- Je potřeba zjednodušit datový model pomocí nenormalizace podřízených tabulek nebo vzorů entit-Object-hodnota.
- Je nutné načíst nebo exportovat data uložená ve formátu JSON bez dalšího nástroje, který data analyzuje.

## <a name="spatial-features"></a>Prostorové funkce

Prostorová data představují informace o fyzickém umístění a tvaru geometrických objektů. Tyto objekty můžou být umístění bodů nebo složitější objekty, jako jsou země/oblasti, silnice nebo jezera.

Azure SQL Database podporuje dva prostorové datové typy – datový typ geometrie a zeměpisný datový typ.
- Typ geometrie reprezentuje data v Euclidean (plochý) souřadnicový systém.
- Zeměpisný typ představuje data v systému souřadnic pro kulatou zemi.

K dispozici je řada prostorových objektů, které lze použít ve službě Azure SQL Database, jako je například [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [mnohoúhelník](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)atd.

Azure SQL Database také poskytuje specializované [prostorové indexy](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) , které lze použít ke zlepšení výkonu prostorových dotazů.

[Prostorová podpora](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) je základní SQL Server funkce databázového stroje, takže můžete najít další informace o prostorové funkci tam.

## <a name="xml-features"></a>Funkce XML

SQL Server poskytuje výkonnou platformu pro vývoj bohatých aplikací pro správu částečně strukturovaných dat. Podpora pro XML je integrovaná do všech součástí v SQL Server a obsahuje následující:

- Datový typ XML. Hodnoty XML lze ukládat nativně do sloupce datového typu XML, které lze zadat v závislosti na kolekci schémat XML nebo netypových prázdných. Sloupec XML můžete indexovat.
- Možnost zadat dotaz XQuery na data XML uložená ve sloupcích a proměnných typu XML. Funkce jazyka XQuery lze použít v jakémkoli dotazu Transact-SQL, který přistupuje k libovolnému datovému modelu, který používáte ve vaší databázi.
- Automaticky Indexujte všechny prvky v dokumentech XML pomocí [primárního indexu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) nebo zadejte přesné cesty, které by měly být indexovány pomocí [sekundárního indexu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET, která umožňuje hromadné načítání dat XML.
- Umožňuje transformovat relační data na formát XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) je základní funkce SQL Server databázového stroje, takže můžete najít další informace o funkci XML tam.

### <a name="when-to-use-an-xml-capability"></a>Kdy použít funkci XML

Modely dokumentů se dají použít místo relačních modelů v některých specifických scénářích:
- Vysoká normalizace schématu nepřináší významné výhody, protože přistupujete ke všem polím objektů najednou nebo dokud neaktualizujete normalizované části objektů. Normalizovaný model však zvyšuje složitost vašich dotazů z důvodu velkého počtu tabulek, které je třeba připojit k získání dat.
- Pracujete s aplikacemi, které nativně používají XML dokumenty, jsou komunikační nebo datové modely a nechcete zavádět další vrstvy, které transformují relační data do XML a naopak.
- Je potřeba zjednodušit datový model pomocí nenormalizace podřízených tabulek nebo vzorů entit-Object-hodnota.
- Je nutné načíst nebo exportovat data uložená ve formátu XML bez dalšího nástroje, který data analyzuje.

## <a name="key-value-pairs"></a>Páry klíč-hodnota

Azure SQL Database neexistují specializované typy nebo struktury, které podporují páry klíč-hodnota, protože struktury klíčových hodnot můžou být nativně reprezentované jako standardní relační tabulky:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Tuto strukturu klíč-hodnota můžete přizpůsobit tak, aby vyhovovala vašim potřebám bez jakýchkoli omezení. Jako příklad může být hodnota dokumentu XML namísto `nvarchar(max)` typu, pokud je hodnota dokument JSON, můžete vložit `CHECK` omezení, které ověří platnost obsahu JSON. V dalších sloupcích můžete zadat libovolný počet hodnot, které se vztahují k jednomu klíči, Přidat vypočítané sloupce a indexy pro zjednodušení a optimalizaci přístupu k datům, definovat tabulku jako paměť/optimalizované tabulky pouze pro zajištění lepšího výkonu atd.

Podívejte se, [jak BWin používá OLTP v paměti k dosažení nedřívějšího výkonu a škálování](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) pro řešení ASP.NET pro ukládání do mezipaměti, které dosáhlo 1.200.000 dávek za sekundu, jako příklad, jak se relační model dá efektivně použít jako párové řešení klíč-hodnota v. praktick.

## <a name="next-steps"></a>Další postup
Možnosti více modelů v databázích SQL Azure jsou také základními funkcemi SQL Server databázového stroje, které jsou sdíleny mezi Azure SQL Database a SQL Server. Další informace o těchto funkcích najdete na stránce dokumentace k relačním databázím SQL:

* [Zpracování grafu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [JSON data](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Prostorová podpora](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [Data XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
