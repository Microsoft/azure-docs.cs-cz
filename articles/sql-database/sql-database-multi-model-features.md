---
title: Možnosti více modelů databáze SQL Azure | Dokumentace Microsoftu
description: Azure SQL Database umožňuje pracovat s více modely dat ve stejné databázi.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 12/17/2018
ms.openlocfilehash: d833d6ea695c05f80f7823f391142fee28872c40
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55300247"
---
# <a name="multi-model-capabilities-of-azure-sql-database"></a>Více modelů možnosti služby Azure SQL Database

Databáze s více modely umožňují snadno ukládat a pracovat s daty v několika datových formátů, jako jsou relační data, grafy, JSON a XML dokumenty, páry klíč hodnota atd.

Azure SQL Database je navržen pro práci s relačními model, který poskytuje nejlepší výkon ve většině případů pro širokou škálu aplikací pro obecné účely. Však není omezen na relační data pouze Azure SQL Database. Azure SQL Database umožňuje používat různé nerelačních formáty, které jsou úzce integrovány do relačního modelu. Azure SQL poskytuje následující funkce pro více modelů:
- [Funkce rozhraní Graph](#graph-features) umožňují reprezentaci dat jako sada uzlů a hran a použijte standardní dotazy Transact-SQL, vylepšené o grafu `MATCH` operátor k dotazování dat grafu.
- [Funkce JSON](#json-features) umožňují dokumenty JSON uložte do tabulky, transformace relačních dat na dokumenty JSON a naopak. Můžete použít standardní jazyk Transact-SQL, rozšířeného s funkcemi JSON k analýze dokumentů a optimalizaci dotazů pomocí jiné Clusterované indexy, indexy columnstore nebo paměťově optimalizovaných tabulkách.
- [Prostorové funkce](#spatial-features) umožňuje ukládat data zeměpisné a geometrické, je pomocí prostorové indexy indexu a načtení dat pomocí prostorových dotazů.
- [Funkce XML](#xml-features) umožňují snadno ukládat a indexaci dat XML do databáze a používat nativní operace XQuery/XPath pro práci s daty XML. Azure SQL database má speciální integrovaný modul dotaz XML, který zpracování dat XML.
- [Páry klíč hodnota](#key-value-pairs) nepodporují explicitně jako speciální funkce od Paříž klíč hodnota můžete nativně nemodelují jako dvěma sloupci tabulky.

  > [!Note]
  > Přístup k žádným datům, která je uložená v databázi, můžete použít výraz cesty JSON XQuery/XPath výrazů, prostorové funkce a výrazy dotazů grafu ve stejném dotazu jazyka Transact-SQL. Navíc libovolného nástroje nebo programovací jazyk, který můžete spouštět dotazy Transact-SQL, můžete také dotaz rozhraní pro přístup k datům pro více modelů. Toto je klíčovým rozdílem v porovnání s vícemodelová databáze, jako [služby Azure Cosmos DB](/azure/cosmos-db/) , které poskytuje specializovaný rozhraní API pro různé datové modely.

V následujících částech můžete informace o vašich nejdůležitějších vícemodelová schopnosti služby Azures SQL Database.

## <a name="graph-features"></a>Funkce grafů

Azure SQL Database nabízí možnosti databáze grafů pro modelování vztahů many-to-many v databázi. Graf je kolekce uzlů (nebo vrcholy) a hrany (nebo vztahy). Uzel představuje entitu (například osoba nebo organizace) a okraj představuje vztah mezi dvěma uzly, které se připojuje (pro příklad, lajky nebo přátel). Toto jsou některé funkce, které usnadňují jedinečný databáze grafu:
- Okraje nebo vztahy jsou první třídy entit v databázi grafu a může mít vlastnosti nebo atributy spojené s nimi.
- Jeden okraj můžete flexibilně připojit více uzlů v databázi grafu.
- Porovnávání vzorů a používat vícenásobnou navigaci dotazů můžete snadno express.
- Přechodné uzavření a polymorfní dotazů můžete snadno express.

Graf vztahů a možnosti dotazu grafu jsou integrované do jazyka Transact-SQL, získáte výhody použití jako systém pro správu základní databáze systému SQL Server.
[Zpracování grafů](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) je funkce databázového stroje SQL Server core, takže můžete najít další informace o grafu zpracování existuje.

### <a name="when-to-use-a-graph-capability"></a>Kdy použít možnosti grafu

Nic, které můžete dosáhnout databázi grafu, který není možné dosáhnout pomocí relační databáze. Nicméně databáze grafu můžete usnadňují expresní určitých dotazy. Rozhodnout a zvolte jednu z nich může být založen na následujících faktorech:

- Hierarchická data modelu, kde jeden uzel může mít více nadřazených objektů, takže se nedá použít HierarchyId
- Vaše aplikace má model obsahuje komplexní relace many-to-many; jak se vyvíjí aplikace se přidají nové relace.
- Budete potřebovat analyzovat propojených dat a relace.

## <a name="json-features"></a>Funkce JSON

Azure SQL Database umožňuje analyzovat a dotazování dat v jazyce JavaScript Object Notation [(JSON)](http://www.json.org/) formátování a exportovat relačních dat jako JSON text.

JSON je oblíbenými datovými formát používaný pro výměnu dat v moderních webových a mobilních aplikací. JSON se také používá pro ukládání částečně strukturovaných dat v souborech protokolů nebo databáze NoSQL jako [služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). Mnoho webových služeb REST vráceny výsledky formátovat jako JSON text nebo přijímat data naformátovaná jako JSON. Většina Azure services, jako [Azure Search](https://azure.microsoft.com/services/search/), [služby Azure Storage](https://azure.microsoft.com/services/storage/), a [služby Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) mít koncové body REST, které vrátí nebo využívají JSON.

Azure SQL Database vám umožní snadno pracovat s daty JSON a integrovat moderních služeb vaší databáze. Azure SQL Database poskytuje následující funkce pro práci s daty JSON:

![Funkce JSON](./media/sql-database-json-features/image_1.png)

Pokud máte JSON text, můžete extrahovat data z formátu JSON nebo ověřte, že je správně ve formátu JSON pomocí integrovaných funkcí [JSON_VALUE](https://msdn.microsoft.com/library/dn921898.aspx), [JSON_QUERY](https://msdn.microsoft.com/library/dn921884.aspx), a [ISJSON](https://msdn.microsoft.com/library/dn921896.aspx). [Příkazu json_modify je](https://msdn.microsoft.com/library/dn921892.aspx) funkce vám umožní aktualizovat hodnotu uvnitř JSON text. Pokročilejší dotazy a analýzy, [OPENJSON](https://msdn.microsoft.com/library/dn921885.aspx) funkce můžete transformovat pole objektů JSON do sady řádků. Jakýkoli dotaz SQL je možné provést v sadě vrácených výsledků. Nakonec je [FOR JSON](https://msdn.microsoft.com/library/dn921882.aspx) klauzuli, která vám umožní formátování data uložená v relačních tabulkách jako JSON text.

Další informace najdete v tématu [jak pracovat s daty JSON ve službě azure SQL Database](sql-database-json-features.md).
[JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) je základní funkce databázového stroje SQL serveru, abyste mohli vyhledat další informace o funkci JSON došlo.

### <a name="when-to-use-a-json-capability"></a>Kdy použít možnosti JSON

Modely dokumentů, je možné použít místo relačních modelů v některých speciálních situacích:
- Vysoce normalizace schématu není významné výhody vyvolat, protože přistupujete všechna pole objektů najednou, nebo nikdy aktualizovat normalizované části objektů. Ale normalizovanou vzoru zvyšuje složitost dotazy z důvodu velkého počtu tabulek, které je potřeba připojit se mají získat data.
- Pracujete s aplikacemi, že nativně jsou dokumenty JSON pomocí komunikaci nebo datové modely a nechcete, aby zavést další vrstvy, která transformuje relačních dat do formátu JSON a naopak.
- Je potřeba zjednodušit datového modelu zrušit normalizace podřízené tabulky nebo hodnota Entity objektu vzory.
- Budete muset načíst nebo exportovat data uložená ve formátu JSON bez některé další nástroj, který analyzuje data.

## <a name="spatial-features"></a>Prostorové funkce

Prostorová data představuje informace o fyzickém umístění a tvar geometrické objekty. Tyto objekty mohou být složitější objekty, jako jsou zemí, silnicích zakázána nebo jezera nebo umístění bodu.

Azure SQL Database podporuje dva typy prostorových dat – datový typ geometry a geography data typu.
- Typ geometry představuje data v Euclidean souřadnicovém systému (bez stromové struktury).
- Typ Geografie představuje data do kruhové earth souřadnicový systém.

Počet prostorových objektů, které lze použít ve službě Azure SQL database, jako je [bodu](https://docs.microsoft.com/sql/relational-databases/spatial/point), [LineString](https://docs.microsoft.com/sql/relational-databases/spatial/linestring), [mnohoúhelníku](https://docs.microsoft.com/sql/relational-databases/spatial/polygon)atd.

Azure SQL Database také poskytuje specializovaný [prostorové indexy](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-indexes-overview) , který lze použít ke zlepšení výkonu prostorových dotazů.

[Prostorový podporu](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) je funkce databázového stroje SQL Server core, tak můžete najít další informace o funkci prostorových existuje.

## <a name="xml-features"></a>Funkce XML

SQL Server poskytuje výkonnou platformu pro vývoj aplikací s bohatým obsahem pro částečně strukturovaná data správy. Podpora pro formát XML je integrované do všech komponent v serveru SQL Server a zahrnuje následující:

- Datový typ xml. Hodnoty XML mohou být uloženy ve sloupci Typ dat xml, který může zadali podle kolekce schémat XML nebo vlevo netypová nativně. Můžete index pro sloupec XML.
- Možnost zadat dotaz XQuery pro XML data uložená ve sloupcích a proměnné typu xml. Jazyk XQuery funkce lze použít v dotaz jazyka Transact-SQL, který přístup k datový model, který používáte ve vaší databázi.
- Automaticky indexuje všechny prvky v dokumentech XML pomocí [primární index XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index) nebo zadat přesné cesty, které se má indexovat pomocí [sekundární index XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- OPENROWSET, který umožňuje hromadné načítání dat XML.
- Transformace relačních dat do formátu XML.

[XML](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server) je základní funkce databázového stroje SQL Server, takže můžete najít další informace o funkci XML došlo.

### <a name="when-to-use-an-xml-capability"></a>Kdy použít možnosti XML

Modely dokumentů, je možné použít místo relačních modelů v některých speciálních situacích:
- Vysoce normalizace schématu není významné výhody vyvolat, protože přistupujete všechna pole objektů najednou, nebo nikdy aktualizovat normalizované části objektů. Ale normalizovanou vzoru zvyšuje složitost dotazy z důvodu velkého počtu tabulek, které je potřeba připojit se mají získat data.
- Pracujete s aplikacemi, že nativně dokumentů XML použijte jsou komunikace nebo datové modely a nechcete, aby zavést další vrstvy, která transformuje relační data XML a naopak.
- Je potřeba zjednodušit datového modelu zrušit normalizace podřízené tabulky nebo hodnota Entity objektu vzory.
- Budete muset načíst nebo exportovat data uložená ve formátu XML bez některé další nástroj, který analyzuje data.

## <a name="key-value-pairs"></a>Páry klíč hodnota

Azure SQL Database nemáte speciálních typů nebo struktury, které podporují páry klíč hodnota, protože klíč hodnota struktury můžou být vyjádřeny nativně jako standardní relačních tabulkách:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Tato struktura klíč hodnota podle vašich potřeb bez jakýchkoliv omezení můžete přizpůsobit. Například, hodnota může být dokument XML namísto `nvarchar(max)` typu, pokud hodnota je dokument JSON, můžete vložit `CHECK` omezení, která ověřuje platnost sady obsah JSON. Můžete vložit libovolný počet hodnoty související s jeden klíč v další sloupce, přidat počítané sloupce a indexy pro zjednodušení a optimalizaci přístupu k datům, definovat tabulku jako paměti či optimalizovanou jen schéma tabulky získat lepší výkon, atd.

Zobrazit [jak BWin OLTP v paměti používá k zajištění mimořádného výkonu a škálování](https://blogs.msdn.microsoft.com/sqlcat/2016/10/26/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale/) pro jejich ukládání do mezipaměti ASP.NET řešení, které dosáhnout 1.200.000 dávek za sekundu, například jak relační model efektivně slouží jako řešení páru klíč hodnota v praxi.

## <a name="next-steps"></a>Další postup
Více modelů možnosti v Azure SQL Database jsou také základní funkce databázového stroje SQL serveru, které jsou sdíleny mezi Azure SQL Database a SQL Server. Další informace o těchto funkcích najdete v tématu stránky dokumentace služby SQL relační databáze:

* [Zpracování grafů](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview)
* [Data JSON](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server)
* [Prostorový podpory](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server)
* [XML data](https://docs.microsoft.com/sql/relational-databases/xml/xml-data-sql-server)
