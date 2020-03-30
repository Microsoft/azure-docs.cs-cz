---
title: Možnosti s více modely
description: Azure SQL Database umožňuje pracovat s více datovými modely ve stejné databázi.
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
ms.openlocfilehash: 2e8519fa8d96b7fe016b9da4ba84ce481a57d94e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73802820"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Možnosti více modelů azure sql database

Vícemodelové databáze umožňují ukládat a pracovat s daty reprezentované ve více datových formátech, jako jsou relační data, grafy, dokumenty JSON/XML, páry klíč-hodnota atd.

## <a name="when-to-use-multi-model-capabilities"></a>Kdy použít funkce více modelů

Azure SQL Database je navržen pro práci s relační model, který poskytuje nejlepší výkon ve většině případů pro různé aplikace pro obecné účely. Azure SQL Database se však neomezuje pouze na relační data. Azure SQL Database umožňuje používat různé nerelační formáty, které jsou úzce integrované do relačního modelu.
Měli byste zvážit použití vícemodelových funkcí Azure SQL Database v následujících případech:
- Máte nějaké informace nebo struktury, které jsou vhodnější pro NoSQL modely a nechcete používat samostatnou NoSQL databázi.
- Většina vašich dat je vhodná pro relační model a některé části dat musíte modelovat ve stylu NoSQL.
- Chcete využít bohatý jazyk Transact-SQL k dotazování a analýze relačních i nosql dat a integrovat je s různými nástroji a aplikacemi, které mohou používat jazyk SQL.
- Chcete použít funkce databáze, jako jsou [technologie v paměti,](sql-database-in-memory.md) abyste zlepšili výkon analýzy nebo zpracování datových struktur NoSQL, použijte [transakční replikaci](sql-database-managed-instance-transactional-replication.md) nebo [čitelné repliky](sql-database-read-scale-out.md) k vytvoření kopie dat na jiném místě a přeložte některé analytické úlohy z primární databáze.

## <a name="overview"></a>Přehled

Azure SQL poskytuje následující funkce více modelů:
- [Funkce grafu](#graph-features) umožňují reprezentovat data jako sadu uzlů a hran a používat standardní dotazy Transact-SQL rozšířené o operátor grafu `MATCH` k dotazování na data grafu.
- [Funkce JSON](#json-features) umožňují umístit dokumenty JSON do tabulek, transformovat relační data do dokumentů JSON a naopak. Můžete použít standardní jazyk Transact-SQL rozšířený s funkcemi JSON pro analýzu dokumentů a pro optimalizaci dotazů můžete použít neseskupené indexy, indexy columnstore nebo tabulky optimalizované pro paměť.
- [Prostorové prvky](#spatial-features) umožňují ukládat geografická a geometrická data, indexovat je pomocí prostorových indexů a načítat data pomocí prostorových dotazů.
- [Funkce XML](#xml-features) umožňují ukládat a indexovat data XML v databázi a používat nativní operace XQuery/XPath pro práci s daty XML. Azure SQL database má specializovaný vestavěný dotazovací stroj XML, který zpracovává data XML.
- [Dvojice klíč-hodnota](#key-value-pairs) nejsou explicitně podporovány jako speciální funkce, protože dvojice klíč-hodnota mohou být nativně modelovány jako tabulky se dvěma sloupci.

  > [!Note]
  > Výraz Cesta JSON, výrazy XQuery/XPath, prostorové funkce a výrazy grafových dotazů ve stejném dotazu Transact-SQL můžete použít pro přístup k datům uloženým v databázi. Také jakýkoli nástroj nebo programovací jazyk, který může spouštět dotazy Transact-SQL, můžete také použít toto rozhraní dotazu pro přístup k datům s více modely. To je klíčový rozdíl ve srovnání s databází s více modely, jako je [Azure Cosmos DB,](/azure/cosmos-db/) který poskytuje specializované rozhraní API pro různé datové modely.

V následujících částech se dozvíte o nejdůležitějších funkcích více modelů Azures SQL Database.

## <a name="graph-features"></a>Funkce grafů

Azure SQL Database nabízí možnosti databáze grafů pro modelování vztahů N:N v databázi. Graf je kolekce uzlů (nebo vrcholů) a hran (nebo relací). Uzel představuje entitu (například osobu nebo organizaci) a okraj představuje vztah mezi dvěma uzly, které spojuje (například to se mi líbí nebo přátelé). Zde jsou některé funkce, které dělají databázi grafů jedinečnou:
- Hrany nebo vztahy jsou entity první třídy v databázi grafu a mohou k nim být přidruženy atributy nebo vlastnosti.
- Jedna hrana může flexibilně připojit více uzlů v databázi grafu.
- Můžete snadno vyjádřit porovnávání vzorů a multi-hop navigační dotazy.
- Můžete snadno vyjádřit přenosné uzavření a polymorfní dotazy.

Vztahy grafu a funkce dotazu grafu jsou integrovány do Transact-SQL a získat výhody použití SQL Server jako základní systém pro správu databáze.
[Zpracování grafu](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) je základní funkce SQL Server Database Engine, takže můžete najít další informace o zpracování grafu zde.

### <a name="when-to-use-a-graph-capability"></a>Kdy použít funkci grafu

Neexistuje nic, co by databáze grafů mohla dosáhnout, čehož nelze dosáhnout pomocí relační databáze. Databáze grafů však může usnadnit vyjádření určitých dotazů. Vaše rozhodnutí vybrat si jeden nad druhým může být založeno na následujících faktorech:

- Hierarchická data modelu, kde jeden uzel může mít více nadřazených objektů, takže hierarchyId nelze použít
- Model má Vaše aplikace má složité relace N:N; jak se aplikace vyvíjí, jsou přidány nové vztahy.
- Je třeba analyzovat propojená data a vztahy.

## <a name="json-features"></a>Funkce JSON

Azure SQL Database umožňuje analyzovat data a dotazy reprezentovaná ve formátu [JSON (JavaScript](https://www.json.org/) Object Notation) a exportovat relační data jako text JSON.

JSON je populární datový formát používaný pro výměnu dat v moderních webových a mobilních aplikacích. JSON se také používá pro ukládání částečně strukturovaných dat v souborech protokolu nebo v databázích NoSQL, jako je [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Mnoho webových služeb REST vrací výsledky formátované jako text JSON nebo přijímá data formátovaná jako JSON. Většina služeb Azure, jako je [Azure Cognitive Search](https://azure.microsoft.com/services/search/), Azure [Storage](https://azure.microsoft.com/services/storage/)a [Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/) má koncové body REST, které vracejí nebo spotřebovávají JSON.

Azure SQL Database umožňuje snadnou práci s daty JSON a integraci databáze s moderními službami. Azure SQL Database poskytuje následující funkce pro práci s daty JSON:

![Funkce JSON](./media/sql-database-json-features/image_1.png)

Pokud máte text JSON, můžete extrahovat data z JSON nebo ověřit, zda je JSON správně formátován pomocí vestavěných funkcí [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx)a [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). Funkce [JSON_MODIFY](https://msdn.microsoft.com/library/dn921892.aspx) umožňuje aktualizovat hodnotu uvnitř textu JSON. Pro pokročilejší dotazování a analýzu může funkce [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) transformovat pole objektů JSON do sady řádků. Jakýkoli dotaz SQL lze spustit na vrácené sadě výsledků. Nakonec je [for JSON](https://msdn.microsoft.com/library/dn921882.aspx) klauzule, která umožňuje formátovat data uložená ve vašich relačních tabulkách jako text JSON.

Další informace najdete v tématu [Jak pracovat s daty JSON v azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) je základní funkce SQL Server Database Engine, takže zde najdete další informace o funkci JSON.

### <a name="when-to-use-a-json-capability"></a>Kdy použít funkci JSON

V některých konkrétních scénářích lze místo relačních modelů použít modely dokumentů:
- Vysoká normalizace schématu nepřináší významné výhody, protože máte přístup ke všem polím objektů najednou nebo nikdy neaktualizujete normalizované části objektů. Normalizovaný model však zvyšuje složitost dotazů z důvodu velkého počtu tabulek, které je třeba připojit k získání dat.
- Pracujete s aplikacemi, které nativně používají dokumenty JSON jsou komunikační nebo datové modely a nechcete zavádět další vrstvy, které transformují relační data do JSON a naopak.
- Datový model je třeba zjednodušit denormalizací podřízených tabulek nebo vzorů entity-objekt-hodnota.
- Je třeba načíst nebo exportovat data uložená ve formátu JSON bez nějakého dalšího nástroje, který analyzuje data.

## <a name="spatial-features"></a>Prostorové prvky

Prostorová data představují informace o fyzickém umístění a tvaru geometrických objektů. Tyto objekty mohou být umístění bodů nebo složitější objekty, jako jsou země nebo oblasti, silnice nebo jezera.

Azure SQL Database podporuje dva typy prostorových dat – datový typ geometrie a geografický datový typ.
- Typ geometrie představuje data v euklidovský (plochý) souřadnicový systém.
- Typ geografie představuje data v souřadnicovém systému s kulatá země.

Existuje řada prostorových objektů, které lze použít v databázi Azure SQL, jako je [Point](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [Mnohonožka](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)atd.

Azure SQL Database také poskytuje specializované [prostorové indexy,](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) které lze použít ke zlepšení výkonu prostorových dotazů.

[Prostorová podpora](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) je základní funkce SQL Server Database Engine, takže můžete najít další informace o prostorové funkce.

## <a name="xml-features"></a>Funkce XML

SQL Server poskytuje výkonnou platformu pro vývoj bohatých aplikací pro částečně strukturovanou správu dat. Podpora jazyka XML je integrována do všech součástí serveru SQL Server a zahrnuje následující:

- Datový typ xml. Hodnoty XML mohou být uloženy nativně ve sloupci datového typu XML, který lze zadat podle kolekce schémat XML nebo je ponechat bez typu. Sloupec XML můžete indexovat.
- Možnost zadat dotaz XQuery proti datům XML uloženým ve sloupcích a proměnných typu XML. Funkce XQuery lze použít v libovolném dotazu Transact-SQL, který přistupuje k libovolnému datovému modelu, který používáte v databázi.
- Automaticky indexovat všechny prvky v dokumentech XML pomocí [primárního indexu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) nebo určit přesné cesty, které by měly být indexovány pomocí [sekundárního indexu XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET, který umožňuje hromadné načítání XML dat.
- Transformujte relační data do formátu XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) je základní funkce databázového stroje SQL Serveru, takže zde najdete další informace o funkci XML.

### <a name="when-to-use-an-xml-capability"></a>Kdy použít funkci XML

V některých konkrétních scénářích lze místo relačních modelů použít modely dokumentů:
- Vysoká normalizace schématu nepřináší významné výhody, protože máte přístup ke všem polím objektů najednou nebo nikdy neaktualizujete normalizované části objektů. Normalizovaný model však zvyšuje složitost dotazů z důvodu velkého počtu tabulek, které je třeba připojit k získání dat.
- Pracujete s aplikacemi, které nativně používají dokumenty XML jsou komunikační nebo datové modely a nechcete zavádět další vrstvy, které transformují relační data do XML a naopak.
- Datový model je třeba zjednodušit denormalizací podřízených tabulek nebo vzorů entity-objekt-hodnota.
- Je třeba načíst nebo exportovat data uložená ve formátu XML bez nějakého dalšího nástroje, který data analyzuje.

## <a name="key-value-pairs"></a>Páry klíč-hodnota

Azure SQL Database nemají specializované typy nebo struktury, které podporují dvojice klíč-hodnota, protože struktury klíč-hodnota mohou být nativně reprezentovány jako standardní relační tabulky:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Tuto strukturu klíč-hodnota můžete přizpůsobit tak, aby vyhovovala vašim potřebám bez jakýchkoli omezení. Jako příklad může být hodnota xml `nvarchar(max)` dokument namísto typu, pokud je hodnota `CHECK` JSON dokument, můžete umístit omezení, které ověřuje platnost obsahu JSON. Můžete umístit libovolný počet hodnot souvisejících s jedním klíčem v dalších sloupcích, přidat vypočítané sloupce a indexy pro zjednodušení a optimalizaci přístupu k datům, definovat tabulku jako tabulku pouze pro paměť/ optimalizované schéma, abyste získali lepší výkon atd.

Podívejte se, [jak BWin používá v paměti OLTP k dosažení bezprecedentní výkon a škálování](https://blogs.msdn.microsoft.com/sqlcat/20../../how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) pro jejich řešení ukládání do mezipaměti ASP.NET, který dosáhl 1.200.000 dávek za sekundu, jako příklad, jak relační model lze efektivně použít jako řešení pár klíč hodnota v praxi.

## <a name="next-steps"></a>Další kroky
Funkce více modelů v databázích Azure SQL jsou také základní funkce sql serveru database engine, které jsou sdílené mezi Azure SQL Database a SQL Server. Další podrobnosti o těchto funkcích naleznete na stránkách dokumentace k relační databázi SQL:

* [Zpracování grafů](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Data JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Prostorová podpora](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [data XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
