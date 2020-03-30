---
title: Přehled elastických dotazů
description: Elastický dotaz umožňuje spustit dotaz Transact-SQL, který zahrnuje více databází.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
ms.date: 12/05/2019
ms.openlocfilehash: 827fab0661a58bfa7d28452960ea6df64d18bf84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873739"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Přehled elastického dotazu azure SQL Database (preview)

Funkce elastického dotazu (ve verzi Preview) umožňuje spustit dotaz Transact-SQL, který zahrnuje více databází v Azure SQL Database. Umožňuje provádět dotazy mezi databázemi pro přístup ke vzdáleným tabulkám a připojit nástroje Microsoftu a třetích stran (Excel, Power BI, Tableau atd.) k dotazování napříč datovými vrstvami s více databázemi. Pomocí této funkce můžete škálovat dotazy na velké datové vrstvy v databázi SQL a vizualizovat výsledky v sestavách business intelligence (BI).

## <a name="why-use-elastic-queries"></a>Proč používat elastické dotazy

### <a name="azure-sql-database"></a>Azure SQL Database

Dotaz napříč databázemi Azure SQL zcela v T-SQL. To umožňuje dotazování vzdálených databází jen pro čtení a poskytuje možnost pro aktuální místní zákazníky SERVERU SQL Server migrovat aplikace pomocí tří a čtyřčástí názvů nebo propojeného serveru sql db.

### <a name="available-on-standard-tier"></a>K dispozici na standardní úrovni

Elastický dotaz je podporován na úrovni služeb Standard i Premium. Podívejte se na část o omezení náhledu níže o omezení výkonu pro nižší úrovně služeb.

### <a name="push-parameters-to-remote-databases"></a>Nabízení parametrů do vzdálených databází

Elastické dotazy nyní můžete tlačit parametry SQL do vzdálených databází pro spuštění.

### <a name="stored-procedure-execution"></a>Provádění uložené procedury

Spouštět vzdálená volání uložených procedur nebo vzdálené funkce pomocí [vzdáleného spuštění\_ \_sp](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilita

Externí tabulky s elastickým dotazem mohou odkazovat na vzdálené tabulky s jiným schématem nebo názvem tabulky.

## <a name="elastic-query-scenarios"></a>Scénáře elastických dotazů

Cílem je usnadnit dotazování scénáře, kde více databází přispívají řádky do jednoho celkového výsledku. Dotaz může být složen uživatelem nebo aplikací přímo nebo nepřímo prostřednictvím nástrojů, které jsou připojeny k databázi. To je užitečné zejména při vytváření sestav, pomocí komerčních nástrojů BI nebo nástroje pro integraci dat nebo jakékoli aplikace, kterou nelze změnit. Pomocí elastického dotazu můžete dotazovat naněkolik databází pomocí známého prostředí pro připojení sql serveru v nástrojích, jako je Excel, Power BI, Tableau nebo Cognos.
Elastický dotaz umožňuje snadný přístup k celé kolekci databází prostřednictvím dotazů vydaných SQL Server Management Studio nebo Visual Studio a usnadňuje dotazování mezi databázemi z entity frameworku nebo jiných prostředí ORM. Obrázek 1 znázorňuje scénář, kde existující cloudová aplikace (která používá [klientskou knihovnu elastické databáze)](sql-database-elastic-database-client-library.md)sestaví na škálované datové vrstvě a elastický dotaz se používá pro vytváření sestav mezi databázemi.

**Obrázek 1** Elastický dotaz používaný na vrstvě dat s horizontálním navýšením kapacity

![Elastický dotaz používaný na vrstvě dat s horizontálním navýšením kapacity][1]

Scénáře zákazníků pro elastický dotaz jsou charakterizovány následujícími topologiemi:

* **Vertikální dělení – dotazy mezi databázemi** (topologie 1): Data jsou rozdělena svisle mezi počet databází v datové vrstvě. Různé sady tabulek jsou obvykle umístěny v různých databázích. To znamená, že schéma se liší v různých databázích. Například všechny tabulky pro zásoby jsou v jedné databázi, zatímco všechny tabulky související s účtováním jsou na druhé databázi. Běžné případy použití s touto topologii vyžadují jeden dotaz napříč nebo kompilovat sestavy napříč tabulkami v několika databázích.
* **Horizontální dělení - horizontálního dělení** (topologie 2): Data jsou rozdělena vodorovně distribuovat řádky přes škálované datové vrstvy. S tímto přístupem schéma je identické ve všech zúčastněných databázích. Tento přístup se také nazývá "sharding". Sharding lze provést a spravovat pomocí (1) knihovny elastické databázové nástroje nebo (2) samosvorné. Elastický dotaz se používá k dotazování nebo kompilaci sestav napříč mnoha úlomky. Úlomky jsou obvykle databáze v rámci elastického fondu. Elastický dotaz si můžete usuzovat jako efektivní způsob dotazování na všechny databáze elastického fondu najednou, pokud databáze sdílejí společné schéma.

> [!NOTE]
> Elastický dotaz funguje nejlépe pro vytváření sestav, kde většinu zpracování (filtrování, agregace) lze provést na straně externího zdroje. Není vhodný pro operace ETL, kde se velké množství dat přenáší ze vzdálených databází. Pro náročné úlohy vytváření sestav nebo scénáře ukládání dat se složitějšími dotazy zvažte také použití [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikální dělení – dotazy mezi databázemi

Chcete-li začít kódování, naleznete [v tématu Začínáme s dotazem mezi databázemi (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).

Elastický dotaz lze použít k zpřístupnění dat umístěných v databázi SQL pro jiné databáze SQL. To umožňuje dotazy z jedné databáze odkazovat na tabulky v jakékoli jiné vzdálené databázi SQL. Prvním krokem je definování externího zdroje dat pro každou vzdálenou databázi. Externí zdroj dat je definován v místní databázi, ze které chcete získat přístup k tabulkám umístěným ve vzdálené databázi. Ve vzdálené databázi nejsou nutné žádné změny. Pro typické scénáře vertikální dělení, kde různé databáze mají různá schémata, elastické dotazy lze použít k implementaci běžných případů použití, jako je například přístup k referenčním datům a dotazování mezi databázemi.

> [!IMPORTANT]
> Musíte mít oprávnění ALTER any external data source. Toto oprávnění je součástí oprávnění ALTER DATABASE. ZMĚNIT všechna oprávnění externího zdroje dat jsou potřebné k odkazování na základní zdroj dat.
>

**Referenční údaje**: Topologie se používá pro správu referenčních dat. Na obrázku níže jsou dvě tabulky (T1 a T2) s referenčními údaji uloženy ve vyhrazené databázi. Pomocí elastického dotazu můžete nyní vzdáleně přistupovat k tabulkám T1 a T2 z jiných databází, jak je znázorněno na obrázku. Topologie 1 použijte, pokud jsou referenční tabulky malé nebo vzdálené dotazy do referenční tabulky mají selektivní predikáty.

**Obrázek 2** Svislé dělení – použití elastického dotazu k dotazování referenčních dat

![Svislé dělení – použití elastického dotazu k dotazování referenčních dat][3]

**Dotazování mezi databázemi**: Elastické dotazy umožňují případy použití, které vyžadují dotazování v několika databázích SQL. Obrázek 3 znázorňuje čtyři různé databáze: CRM, Inventory, HR a Products. Dotazy provedené v jedné z databází také potřebují přístup k jedné nebo všech ostatních databází. Pomocí elastického dotazu můžete nakonfigurovat databázi pro tento případ spuštěním několika jednoduchých příkazů DDL v každé ze čtyř databází. Po této jednorázové konfiguraci je přístup ke vzdálené tabulce stejně jednoduchý jako odkaz ování místní tabulky z vašich dotazů T-SQL nebo z nástrojů BI. Tento přístup se doporučuje, pokud vzdálené dotazy nevracejí velké výsledky.

**Obrázek 3** Vertikální dělení – použití elastického dotazu k dotazování v různých databázích

![Vertikální dělení – použití elastického dotazu k dotazování v různých databázích][4]

Následující kroky nakonfigurují elastické databázové dotazy pro scénáře vertikálního dělení, které vyžadují přístup k tabulce umístěné ve vzdálených databázích SQL se stejným schématem:

* [VYTVOŘIT MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [Vytvořit pověření oboru databáze](https://msdn.microsoft.com/library/mt270260.aspx)
* [VYTVOŘIT/PŘETAŽENÍ EXTERNÍHO ZDROJE DAT](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **RDBMS**
* [VYTVOŘIT/DROP EXTERNÍ TABULKA](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po spuštění ddl příkazy, můžete přistupovat ke vzdálené tabulce "mytable", jako by to byla místní tabulka. Azure SQL Database automaticky otevře připojení ke vzdálené databázi, zpracuje váš požadavek na vzdálené databázi a vrátí výsledky.

## <a name="horizontal-partitioning---sharding"></a>Horizontální dělení - horizontální dělení

Použití elastického dotazu k provádění úloh vytváření sestav přes horizontálně dělené, datová vrstva vyžaduje [mapování horizontálního oddílu elastické databáze](sql-database-elastic-scale-shard-map-management.md) k reprezentaci databází datové vrstvy. V tomto scénáři se obvykle používá pouze jedna mapa střepu a vyhrazená databáze s možnostmi elastického dotazu (hlavní uzel) slouží jako vstupní bod pro vytváření sestav dotazů. Pouze tato vyhrazená databáze potřebuje přístup k mapě střepů. Obrázek 4 znázorňuje tuto topologii a její konfiguraci pomocí databáze elastických dotazů a mapy úlomků. Databáze v datové vrstvě může být libovolné verze nebo edice Azure SQL Database. Další informace o klientské knihovně elastické databáze a vytváření map sít, naleznete v [tématu Správa map storu](sql-database-elastic-scale-shard-map-management.md).

**Obrázek 4** Horizontální dělení – použití elastického dotazu pro vytváření sestav přes horizontálně dělené datové vrstvy

![Horizontální dělení – použití elastického dotazu pro vytváření sestav přes horizontálně dělené datové vrstvy][5]

> [!NOTE]
> Elastická databáze dotazů (hlavní uzel) může být samostatná databáze nebo může být stejná databáze, která je hostitelem mapy střepů.
> Bez ohledu na konfiguraci, kterou zvolíte, ujistěte se, že úroveň služby a výpočetní velikost této databáze je dostatečně vysoká pro zpracování očekávaného množství požadavků na přihlášení/dotaz.

Následující kroky nakonfigurují elastické databázové dotazy pro scénáře horizontálního dělení, které vyžadují přístup k sadě tabulek umístěných v (obvykle) několika vzdálených databázích SQL:

* [VYTVOŘIT MASTER KEY](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [Vytvořit pověření oboru databáze](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
* Vytvořte [mapování střepů](sql-database-elastic-scale-shard-map-management.md) představující vaši datovou vrstvu pomocí klientské knihovny elastické databáze.
* [VYTVOŘIT/PŘETÁHNOUT EXTERNÍ ZDROJ DAT](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource typu **SHARD_MAP_MANAGER**
* [VYTVOŘIT/DROP EXTERNÍ TABULKA](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Po provedení těchto kroků můžete přistupovat k vodorovně dělené tabulce "mytable", jako by se jednalo o místní tabulku. Azure SQL Database automaticky otevře více paralelních připojení ke vzdáleným databázím, kde jsou tabulky fyzicky uloženy, zpracuje požadavky na vzdálených databázích a vrátí výsledky.
Další informace o krocích požadovaných pro scénář vodorovného dělení naleznete v [elastickém dotazu pro horizontální dělení](sql-database-elastic-query-horizontal-partitioning.md).

Chcete-li začít kódování, naleznete [v tématu Začínáme s elastickým dotazem pro horizontální dělení (horizontálního dělení)](sql-database-elastic-query-getting-started.md).

> [!IMPORTANT]
> Úspěšné spuštění elastického dotazu přes rozsáhlou sadu databází do značné míry závisí na dostupnosti každé databáze během provádění dotazu. Pokud jedna z databází není k dispozici, celý dotaz se nezdaří. Pokud máte v plánu dotaz na stovky nebo tisíce databází najednou, ujistěte se, že vaše klientská aplikace má vložené logiky opakování nebo zvažte využití [úlohy elastické databáze](https://docs.microsoft.com/azure/sql-database/sql-database-job-automation-overview#elastic-database-jobs-preview) (náhled) a dotazování menších podmnožiny databází, konsolidace výsledků každého dotazu do jednoho cíle.

## <a name="t-sql-querying"></a>Dotazování T-SQL

Po definování externích zdrojů dat a externích tabulek můžete použít běžné připojovací řetězce serveru SQL Server pro připojení k databázím, kde jste definovali externí tabulky. Potom můžete spustit Příkazy T-SQL přes externí tabulky na toto připojení s omezeními uvedenými níže. Další informace a příklady dotazů T-SQL naleznete v dokumentačních tématech pro [horizontální dělení](sql-database-elastic-query-horizontal-partitioning.md) a [vertikální dělení](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Konektivita pro nástroje

Běžné připojovací řetězce SERVERU SQL Server můžete použít k připojení aplikací a nástrojů pro integraci BI nebo dat k databázím, které mají externí tabulky. Ujistěte se, že SQL Server je podporován jako zdroj dat pro váš nástroj. Po připojení, odkazovat na databázi elastických dotazů a externí tabulky v této databázi stejně jako u jakékoli jiné databáze SERVERU SQL Server, které se připojíte pomocí nástroje.

> [!IMPORTANT]
> Ověřování pomocí služby Azure Active Directory s elastické dotazy není aktuálně podporováno.

## <a name="cost"></a>Náklady

Elastický dotaz je součástí nákladů na databáze Azure SQL Database. Všimněte si, že topologie, kde jsou vzdálené databáze v jiném datovém centru než koncový bod elastického dotazu jsou podporovány, ale data odchozí ze vzdálených databází se účtuje pravidelně [Sazby Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Omezení náhledu

* Spuštění prvního elastického dotazu může trvat až několik minut na úrovni služby Standard. Tato doba je nezbytná k načtení funkce elastického dotazu; načítání výkon zlepšuje s vyšší úrovně služeb a výpočetní velikosti.
* Skriptování externích zdrojů dat nebo externích tabulek z SSMS nebo SSDT ještě není podporováno.
* Import a export pro SQL DB ještě nepodporuje externí zdroje dat a externí tabulky. Pokud potřebujete použít import nebo export, přetáhněte tyto objekty před exportem a po importu je znovu vytvořte.
* Elastický dotaz aktuálně podporuje pouze přístup jen pro čtení k externím tabulkám. Můžete však použít plnou funkci T-SQL v databázi, kde je definována externí tabulka. To může být užitečné například zachovat dočasné výsledky pomocí, například SELECT <column_list> INTO <local_table> nebo definovat uložené procedury v databázi elastických dotazů, které odkazují na externí tabulky.
* S výjimkou nvarchar(max) nejsou typy LOB (včetně prostorových typů) podporovány v definicích externích tabulek. Jako řešení můžete vytvořit zobrazení na vzdálené databázi, která přetypuje typ LOB do nvarchar(max), definovat externí tabulku nad zobrazením namísto základní tabulky a pak ji přetypovat zpět do původního typu LOB v dotazech.
* Sloupce datového typu nvarchar(max) v sadě výsledků zakazují pokročilou techniku dávkování používanou v implementaci elastického dotazu a mohou ovlivnit výkon dotazu na řád, nebo dokonce dva řádově v případech nekanonického použití, kde velké množství neagregovaná data jsou přenášena jako výsledek dotazu.
* Statistiky sloupců nad externími tabulkami nejsou aktuálně podporovány. Statistiky tabulek jsou podporovány, ale je třeba je vytvořit ručně.
* Elastický dotaz funguje jenom s Azure SQL Database. Nelze jej použít pro dotazování místní SQL Server nebo SQL Server ve virtuálním počítači.

## <a name="feedback"></a>Váš názor

Sdílejte zpětnou vazbu o svých zkušenostech s elastické dotazy s námi níže, na fórech MSDN nebo na přetečení zásobníku. Máme zájem o všechny druhy zpětné vazby o službě (vady, drsné hrany, mezery funkcí).

## <a name="next-steps"></a>Další kroky

* Kurz vertikálního dělení naleznete v [tématu Začínáme s dotazem mezi databázemi (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro svisle dělená data, najdete [v tématu Dotazování svisle dělených dat)](sql-database-elastic-query-vertical-partitioning.md)
* Kurz horizontálního dělení (horizontálního dělení) naleznete [v tématu Začínáme s elastickým dotazem pro horizontální dělení (horizontální dělení)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělená data naleznete v [tématu Dotazování horizontálně dělených dat)](sql-database-elastic-query-horizontal-partitioning.md)
* Viz [\_sp \_spustit vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provede příkaz Transact-SQL na jednom vzdáleném Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly v horizontálním dělení schéma.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
