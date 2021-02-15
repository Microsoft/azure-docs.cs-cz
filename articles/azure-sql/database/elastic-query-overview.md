---
title: Přehled elastických dotazů
description: Elastický dotaz umožňuje spustit dotaz Transact-SQL, který zahrnuje více databází.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: cac17bbac96d44d8d9bfce2e168de4ea6d4c5c08
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364949"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Přehled elastického dotazu Azure SQL Database (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Funkce elastických dotazů (ve verzi Preview) umožňuje spouštět dotazy Transact-SQL zahrnující několik databází ve službě Azure SQL Database. Umožňuje provádět dotazy napříč databázemi pro přístup ke vzdáleným tabulkám a k propojení nástrojů společnosti Microsoft a jiných dodavatelů (Excel, Power BI, Tableau atd.) pro dotazování napříč datovými vrstvami s více databázemi. S využitím této funkce můžete škálovat dotazy na velké datové vrstvy na více instancí a vizualizovat výsledky v sestavách business intelligence (BI).

## <a name="why-use-elastic-queries"></a>Proč používat elastické dotazy

### <a name="azure-sql-database"></a>Azure SQL Database

Dotazování napříč databázemi v Azure SQL Database zcela v T-SQL. To umožňuje dotazovat se na vzdálené databáze jen pro čtení a poskytuje možnost pro stávající SQL Server zákazníkům migrovat aplikace pomocí názvů tří a čtyř částí nebo propojeného serveru na SQL Database.

### <a name="available-on-standard-tier"></a>K dispozici na úrovni Standard

Elastický dotaz je podporován na úrovni služeb Standard a Premium. V části omezení verze Preview níže najdete omezení výkonu pro nižší úrovně služeb.

### <a name="push-parameters-to-remote-databases"></a>Doručovat parametry do vzdálených databází

Elastické dotazy teď můžou odeslat parametry SQL do vzdálených databází ke spuštění.

### <a name="stored-procedure-execution"></a>Spuštění uložené procedury

Spuštění vzdálených volání uložených procedur nebo vzdálených funkcí pomocí [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database).

### <a name="flexibility"></a>Flexibilita

Externí tabulky s elastickým dotazem můžou odkazovat na vzdálené tabulky s jiným názvem schématu nebo tabulky.

## <a name="elastic-query-scenarios"></a>Scénáře elastického dotazu

Cílem je usnadnit dotazování scénářů, ve kterých více databází přispívá řádky do jednoho celkového výsledku. Dotaz může sestavit buď přímo uživatel, nebo aplikace, nebo nepřímo prostřednictvím nástrojů, které jsou připojené k databázi. To je užitečné hlavně při vytváření sestav, používání komerčních nástrojů BI nebo pro integraci dat nebo libovolné aplikace, kterou nelze změnit. Pomocí elastického dotazu se můžete dotazovat napříč několika databázemi pomocí známých SQL Server možností připojení v nástrojích, jako je Excel, Power BI, Tableau nebo Cognos.
Elastický dotaz umožňuje snadný přístup k celé kolekci databází prostřednictvím dotazů vydaných SQL Server Management Studio nebo sadě Visual Studio a usnadňuje dotazování napříč databázemi z Entity Framework nebo jiných prostředí ORM. Obrázek 1 znázorňuje situaci, kdy existující cloudová aplikace (která používá [klientskou knihovnu elastické databáze](elastic-database-client-library.md)) se vytváří na datové vrstvě s více instancemi a elastický dotaz se používá pro generování sestav mezi databázemi.

**Obrázek 1** Elastický dotaz se používá pro datovou vrstvu s měřítkem.

![Elastický dotaz se používá pro datovou vrstvu s měřítkem.][1]

Scénáře zákazníků pro elastický dotaz jsou charakterizovány následujícími topologiemi:

* **Vertikální dělení – mezidatabázové dotazy** (topologie 1): data jsou rozdělená svisle mezi různé databáze v datové vrstvě. Různé sady tabulek se typicky nacházejí v různých databázích. To znamená, že schéma se liší v různých databázích. Například všechny tabulky pro inventář jsou v jedné databázi, zatímco všechny tabulky související s monitorováním účtů jsou v druhé databázi. Běžné případy použití s touto topologií vyžadují, aby se jedna použila dotaz na napříč tabulkami v několika databázích a aby mohla kompilovat sestavy.
* **Horizontální dělení na oddíly – horizontálního dělení** (topologie 2): data jsou rozdělená na oddíly vodorovně pro distribuci řádků napříč škálované datovou vrstvou. S tímto přístupem je schéma stejné ve všech zúčastněných databázích. Tento přístup se také nazývá "horizontálního dělení". Horizontálního dělení se dá provádět a spravovat pomocí (1) knihoven nástrojů elastické databáze nebo (2) sami horizontálního dělení. Elastický dotaz se používá k dotazování nebo kompilování sestav napříč mnoha horizontálních oddílů. Horizontálních oddílů jsou obvykle databáze v elastickém fondu. Elastický dotaz si můžete představit jako účinný způsob dotazování všech databází elastického fondu, pokud databáze sdílejí společné schéma.

> [!NOTE]
> Elastický dotaz funguje nejlépe pro scénáře vytváření sestav, kde většinu zpracování (filtrování, agregace) je možné provést na straně externího zdroje. Není vhodný pro operace ETL, kde se přenáší velké množství dat ze vzdálených databází. Pro náročné úlohy vytváření sestav nebo scénáře datového skladu pomocí složitějších dotazů Zvažte také použití [Azure synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikální dělení – mezidatabázové dotazy

Chcete-li začít s kódováním, přečtěte si téma [Začínáme s mezidatabázovým dotazem (vertikální dělení)](elastic-query-getting-started-vertical.md).

Elastický dotaz lze použít k zajištění dat umístěných v databázi v SQL Database k dispozici pro jiné databáze v SQL Database. To umožňuje dotazům z jedné databáze odkazovat na tabulky v jakékoli jiné vzdálené databázi v SQL Database. Prvním krokem je definování externího zdroje dat pro každou vzdálenou databázi. Externí zdroj dat je definován v místní databázi, ze které chcete získat přístup k tabulkám umístěným ve vzdálené databázi. Ve vzdálené databázi nejsou nutné žádné změny. Pro typické scénáře vertikálního dělení, kde různé databáze mají různá schémata, je možné elastické dotazy použít k implementaci běžných případů použití, jako je například přístup k referenčním datům a dotazování mezi databázemi.

> [!IMPORTANT]
> Musíte mít oprávnění ke změně všech externích zdrojů dat. Toto oprávnění je součástí oprávnění ALTER DATABASE. Aby bylo možné odkazovat na podkladový zdroj dat, je třeba změnit všechna oprávnění ke zdroji externích dat.
>

**Referenční data**: topologie se používá pro správu referenčních dat. Na následujícím obrázku jsou dvě tabulky (T1 a T2) s referenčními daty uchovávány ve vyhrazené databázi. Pomocí elastického dotazu teď můžete přistupovat k tabulkám T1 a T2 vzdáleně z jiných databází, jak je znázorněno na obrázku. Použijte topologii 1, pokud jsou referenční tabulky malé nebo vzdálené dotazy do referenční tabulky mají selektivní predikáty.

**Obrázek 2** Vertikální dělení – použití elastického dotazu k dotazování na referenční data

![Vertikální dělení – použití elastického dotazu k dotazování na referenční data][3]

**Dotazování napříč databázemi**: elastické dotazy umožňují případy použití, které vyžadují dotazování napříč několika databázemi v SQL Database. Obrázek 3 ukazuje čtyři různé databáze: CRM, inventář, HR a produkty. Dotazy provedené v jedné z databází také potřebují přístup k jedné nebo všem ostatním databázím. Pomocí elastického dotazu můžete pro tento případ nakonfigurovat databázi tak, že v každé ze čtyř databází spustíte několik jednoduchých příkazů DDL. Po této jednorázové konfiguraci bude mít přístup ke vzdálené tabulce jednoduché odkazy na místní tabulku z vašich dotazů T-SQL nebo z nástrojů BI. Tento přístup se doporučuje, pokud vzdálené dotazy nevrátí velké výsledky.

**Obrázek 3** Vertikální dělení – použití elastického dotazu k dotazování napříč různými databázemi

![Vertikální dělení – použití elastického dotazu k dotazování napříč různými databázemi][4]

Následující kroky nakonfigurují elastické databázové dotazy pro scénáře vertikální dělení, které vyžadují přístup k tabulce umístěné ve vzdálených databázích v SQL Database se stejným schématem:

* [Vytvořit hlavní klíč](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Vytvořit databázi s rozsahem přihlašovacích údajů](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* [Vytvořit nebo vyřadit externí zdroj dat](/sql/t-sql/statements/create-external-data-source-transact-sql) myDataSource typu **RDBMS**
* [Vytvořit nebo vyřadit externí tabulku](/sql/t-sql/statements/create-external-table-transact-sql) myTable

Po spuštění příkazů DDL máte přístup ke vzdálené tabulce "myTable", jako by šlo o místní tabulku. Azure SQL Database automaticky otevře připojení ke vzdálené databázi, zpracuje vaši žádost ve vzdálené databázi a vrátí výsledky.

## <a name="horizontal-partitioning---sharding"></a>Horizontální dělení – horizontálního dělení

Použití elastického dotazu k provádění úloh vytváření sestav přes horizontálně dělené, tj. horizontálně dělená Datová vrstva vyžaduje, aby [horizontálních oddílů mapa Elastické databáze](elastic-scale-shard-map-management.md) představovala databáze datové vrstvy. V tomto scénáři se obvykle používá pouze jedna mapa horizontálních oddílů a vyhrazená databáze s funkcemi elastického dotazu (hlavní uzel) slouží jako vstupní bod pro dotazy vytváření sestav. Pouze tato vyhrazená databáze potřebuje přístup k mapě horizontálních oddílů. Obrázek 4 znázorňuje tuto topologii a její konfiguraci pomocí elastické databáze dotazů a mapy horizontálních oddílů. Další informace o klientské knihovně elastické databáze a vytváření map horizontálních oddílů najdete v tématu [Správa map horizontálních oddílů](elastic-scale-shard-map-management.md).

**Obrázek 4** Horizontální dělení – použití elastického dotazu pro vytváření sestav přes horizontálně dělené úrovně dat

![Horizontální dělení – použití elastického dotazu pro vytváření sestav přes horizontálně dělené úrovně dat][5]

> [!NOTE]
> Elastická databáze dotazů (hlavní uzel) může být samostatná databáze nebo se může jednat o stejnou databázi, která je hostitelem mapy horizontálních oddílů.
> Ať už si zvolíte takovou konfiguraci, ujistěte se, že je úroveň služby a výpočetní velikost této databáze dostatečně vysoká, aby bylo možné zpracovat očekávané množství žádostí o přihlášení nebo dotaz.

Následující kroky nakonfigurují elastické databázové dotazy pro scénáře horizontálního dělení, které vyžadují přístup k sadě tabulek umístěných na (obvykle) několika vzdálených databází v SQL Database:

* [Vytvořit hlavní klíč](/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Vytvořit databázi s rozsahem přihlašovacích údajů](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Vytvořte [mapu horizontálních oddílů](elastic-scale-shard-map-management.md) představující vaši datovou vrstvu pomocí klientské knihovny elastické databáze.
* [Vytvořit nebo vyřadit externí zdroj dat](/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource typu **SHARD_MAP_MANAGER**
* [Vytvořit nebo vyřadit externí tabulku](/sql/t-sql/statements/create-external-table-transact-sql) myTable

Po provedení těchto kroků můžete získat přístup k horizontálně rozdělené tabulce "myTable", jako by šlo o místní tabulku. Azure SQL Database automaticky otevírá více paralelních připojení ke vzdáleným databázím, kde jsou fyzicky uloženy tabulky, zpracovává požadavky na vzdálené databáze a vrací výsledky.
Další informace o krocích, které jsou potřebné pro scénář horizontálního dělení, najdete v [elastickém dotazu pro horizontální dělení na oddíly](elastic-query-horizontal-partitioning.md).

Chcete-li začít s kódováním, přečtěte si téma [Začínáme s elastickým dotazem pro horizontální dělení (horizontálního dělení)](elastic-query-getting-started.md).

> [!IMPORTANT]
> Úspěšné provedení elastického dotazu nad velkou sadou databází v průběhu provádění dotazu intenzivně závisí na dostupnosti jednotlivých databází. Pokud není jedna z databází k dispozici, celý dotaz se nezdaří. Pokud plánujete dotazovat stovky nebo tisíce databází najednou, ujistěte se, že vaše klientská aplikace má vloženou logiku opakování, nebo zvažte použití [Elastic Databasech úloh](./job-automation-overview.md) (Preview) a dotazování na menší podmnožiny databází a konsoliduje výsledky každého dotazu do jednoho cíle.

## <a name="t-sql-querying"></a>Dotazování T-SQL

Po definování externích zdrojů dat a externích tabulek můžete použít běžné připojovací řetězce SQL Server pro připojení k databázím, kde jste definovali externí tabulky. Pak můžete v tomto připojení spustit příkazy T-SQL na svých externích tabulkách s omezeními uvedenými níže. Další informace a příklady dotazů T-SQL najdete v tématech dokumentace pro [horizontální dělení](elastic-query-horizontal-partitioning.md) a [vertikální dělení](elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Možnosti připojení pro nástroje

Pomocí běžných SQL Serverch připojovacích řetězců můžete propojit aplikace a nástroje pro integraci s daty a nástroji pro integraci dat s databázemi, které mají externí tabulky. Ujistěte se, že je pro nástroj SQL Server podporována jako zdroj dat. Po připojení si přečtěte databázi elastického dotazu a externí tabulky v této databázi stejně, jako byste to propojili s jinou databází SQL Server, ke které se připojíte pomocí nástroje.

> [!IMPORTANT]
> Ověřování pomocí Azure Active Directory s elastickými dotazy není v současné době podporováno.

## <a name="cost"></a>Náklady

Elastický dotaz je zahrnutý v ceně Azure SQL Database. Mějte na paměti, že topologie, ve kterých jsou vaše vzdálené databáze v jiném datovém centru, než je podporovaný koncový bod elastického dotazu, se účtují na základě pravidelného [přenosu](https://azure.microsoft.com/pricing/details/data-transfers/)dat ze vzdálených databází.

## <a name="preview-limitations"></a>Omezení verze Preview

* Spuštění prvního elastického dotazu může trvat až několik minut na standardní úrovni služby. Tento čas je nezbytný pro načtení funkce elastického dotazu. načítají se výkon s vyššími úrovněmi služeb a výpočetními velikostmi.
* Skriptování externích zdrojů dat nebo externích tabulek z SSMS nebo SSDT se ještě nepodporuje.
* Import/export pro SQL Database ještě nepodporuje externí zdroje dat a externí tabulky. Pokud potřebujete použít import/export, vyřaďte tyto objekty před exportem a pak je znovu vytvořte po importu.
* Elastický dotaz momentálně podporuje jenom přístup jen pro čtení k externím tabulkám. V databázi, kde je definována externí tabulka, však můžete použít úplnou funkci T-SQL. To může být užitečné, například zachovat dočasné výsledky pomocí, například vybrat <column_list> do <local_table> nebo definovat uložené procedury v databázi elastických dotazů, které odkazují na externí tabulky.
* S výjimkou nvarchar (max) nejsou v definicích externích tabulek podporovány typy LOB (včetně prostorových typů). Jako alternativní řešení můžete vytvořit zobrazení na vzdálené databázi, která přenese typ LOB do nvarchar (max), definovat externí tabulku přes zobrazení místo základní tabulky a následně ji přetypovat zpátky na původní typ LOB v dotazech.
* Sloupce datového typu nvarchar (max) ve výsledné sadě zakazují pokročilou dávkovou Technics dávkování použitou v implementaci elastického dotazu a mohou ovlivnit výkon dotazů na určité místo, nebo dokonce dva objednávky v nekanonickém případě použití, kdy se velké množství neagregovaných dat přenáší jako výsledek dotazu.
* Statistiky sloupců v externích tabulkách nejsou aktuálně podporovány. Statistiky tabulek jsou podporovány, ale je nutné je vytvořit ručně.
* Elastický dotaz funguje pouze s Azure SQL Database. Nemůžete ho použít pro dotazování instance SQL Server.

## <a name="share-your-feedback"></a>Sdílejte svůj názor

Flexibilní dotazy vám pomůžete sdílet s elastickými dotazy, a to na fórech MSDN nebo na Stack Overflow. Máme zájem o všechny druhy zpětné vazby k této službě (vady, hrubou hranu, nedostatky funkcí).

## <a name="next-steps"></a>Další kroky

* Kurz pro vertikální dělení najdete v tématu [Začínáme s mezidatabázovým dotazem (vertikální dělení)](elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro vertikálně dělená data najdete v tématu [dotazování na vertikálně dělená data](elastic-query-vertical-partitioning.md) .
* Kurz horizontálního dělení na oddíly (horizontálního dělení) najdete v tématu [Začínáme s elastickým dotazem pro horizontální dělení na oddíly (horizontálního dělení)](elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně rozdělená data najdete v tématu [dotazování na horizontálně dělená data](elastic-query-horizontal-partitioning.md) .
* V tématu [SP \_ Execute \_ Remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) pro uloženou proceduru, která provádí příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadě databází, která slouží jako horizontálních oddílů ve vodorovném schématu dělení.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->