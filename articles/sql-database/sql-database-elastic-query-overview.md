---
title: Přehled služby Azure SQL Database elastic query | Dokumentace Microsoftu
description: Elastický dotaz můžete spustit dotaz jazyka Transact-SQL, která zahrnuje více databází.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein
manager: craigg
ms.date: 09/14/2018
ms.openlocfilehash: a1ad976be258c418c115d0dbd79d4d6700a15b31
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54158410"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Přehled služby Azure SQL Database elastic query (preview)

Funkce elastický dotaz (ve verzi preview) umožňuje spustit dotaz jazyka Transact-SQL, která zahrnuje více databází ve službě Azure SQL Database. To umožňuje provádět mezidatabázové dotazy, přístup do vzdálených tabulek a připojovat Microsoftu a třetích stran nástroji (Excel, Power BI, Tableau, atd.) pro dotazování napříč datovou vrstvou s více databází. Díky této funkci můžete horizontální navýšení kapacity dotazů na úrovně velkých objemů dat ve službě SQL Database a vizualizaci výsledků v sestav business intelligence (BI).

## <a name="why-use-elastic-queries"></a>Proč používat elastické dotazy

### <a name="azure-sql-database"></a>Azure SQL Database

Dotazování napříč databázemi Azure SQL zcela v T-SQL. To umožňuje jen pro čtení dotazování vzdálené databáze a nabízí možnost pro stávající zákazníky v místním SQL serveru k migraci aplikací pomocí tří a čtyř částečný názvy nebo propojeného serveru do databáze SQL.

### <a name="available-on-standard-tier"></a>K dispozici na úrovni standard

Elastický dotaz je podporována v úrovních Standard a Premium. V části na omezení verze Preview níže na omezení výkonu pro nižší úrovně.

### <a name="push-parameters-to-remote-databases"></a>Vložit parametry do vzdálené databáze

Elastické dotazy teď může nasdílet změny parametry SQL do vzdálené databáze pro spuštění.

### <a name="stored-procedure-execution"></a>Provedení uložené procedury

Spustit volání vzdálené uložené procedury nebo funkce remote pomocí [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714).

### <a name="flexibility"></a>Flexibilita

Externí tabulky s elastický dotaz mohou odkazovat na vzdálených tabulek s jiné schéma nebo název tabulky.

## <a name="elastic-query-scenarios"></a>Elastický dotaz scénáře

Cílem je usnadnit dotazování scénáře, kde více databází přispívat řádků do jediné celkový výsledek. Dotaz může skládá buď podle uživatele nebo aplikace přímo nebo nepřímo prostřednictvím nástrojů, které jsou připojené k databázi. To je užitečné zejména při vytváření sestav pomocí obchodní BI nebo nástrojů pro integraci dat nebo aplikace, která se nedá změnit. Pomocí elastic query můžete zadávat dotazy napříč několika databázemi v nástrojů, jako je Excel, Power BI, Tableau nebo Cognos známým připojení k serveru SQL Server.
Elastický dotaz umožňuje snadný přístup k celé kolekce databází prostřednictvím dotazy odeslané pomocí SQL Server Management Studio nebo Visual Studio a usnadňuje z Entity Framework nebo v jiných prostředích ORM dotazování napříč databázemi. Obrázek 1 představuje scénář, kdy existující Cloudová aplikace (které používá [Klientská knihovna elastic database](sql-database-elastic-database-client-library.md)) sestavení na horizontálním navýšením kapacity datové vrstvy a elastického dotazu se používá pro vytváření sestav napříč databázemi.

**Obrázek 1** elastický dotaz použitý v horizontálním navýšením kapacity Datová vrstva

![Elastický dotaz použitý v horizontálním navýšením kapacity Datová vrstva][1]

Zákazník scénáře pro elastický dotaz jsou charakteristické následující topologie:

* **Vertikální dělení - mezidatabázové dotazy** (topologie 1): Data je rozdělené do vertikálních oddílů mezi několika databázemi, v datové vrstvě. Různé sady tabulek se obvykle nacházejí na různých databázích. To znamená, že schéma se liší v různých databázích. Například všechny tabulky inventáře jsou na jednu databázi, zatímco všechny tabulky související s monitorování účtů na druhém databáze. Běžné případy použití s touto topologií vyžadovat jeden pro dotazování napříč nebo ke kompilaci sestavy mezi tabulkami v několika databázích.
* **Horizontální dělení – horizontální dělení** (topologie 2): Data jsou rozdělená vodorovně a distribuuje řádky mezi škálovaném datové vrstvy. S tímto přístupem schéma je shodné pro všechny zúčastněné databáze. Tento přístup se také nazývá "horizontálního dělení". Provést horizontálního dělení a spravované pomocí (1) elastických databází nástroje, knihovny, nebo (2) self horizontální dělení. Elastický dotaz slouží k dotazování nebo kompilací sestav napříč velkým počtem horizontálních oddílů.

> [!NOTE]
> Elastický dotaz je nejvhodnější pro scénáře, kde můžete provádět většinu zpracování (filtrování, agregace) na straně externího zdroje vytváření sestav. To není vhodný pro operace ETL, kde je velké množství dat přenášených ze vzdálené databáze. Pro náročné úlohy vytváření sestav nebo datových skladů scénáře s mnohem složitější dotazy, také zvážit použití [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikální dělení - mezidatabázové dotazy

Pokud chcete začít, kódování, najdete v článku [Začínáme s mezidatabázovými dotazy (vertikální oddíly)](sql-database-elastic-query-getting-started-vertical.md).

Elastický dotaz můžete použít tak, aby data nachází ve službě SQL database k dispozici s jinými databázemi SQL. To umožňuje dotazy z jedné databáze k odkazování tabulek v kterékoli jiné vzdálené databáze SQL. Prvním krokem je definování externího zdroje dat pro každou vzdálenou databázi. Externí zdroj dat je definován v místní databázi, ze kterého chcete získat přístup k tabulkám, které jsou umístěné na vzdálené databáze. Žádné změny nejsou potřeba na vzdálené databáze. Pro typické vertikální dělení scénáře, kde různými databázemi. mají různé schémata elastické dotazy slouží k implementaci běžných případů použití, jako je například přístup do referenčních dat a dotazování napříč databázemi.

> [!IMPORTANT]
> Musí mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE. K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.
>

**Referenční data**: Topologie se používá pro správu dat na odkaz. Na následujícím obrázku jsou dvě tabulky (T1 a T2) s referenčními daty ukládají na vyhrazený databázový. Použitím elastického dotazu, můžete teď vzdálený přístup k tabulkám, T1 a T2 z jiných databází, jak je znázorněno na obrázku. Použití topologie 1, pokud jsou malé nebo vzdálené dotazy do tabulky odkaz na referenční tabulky mají selektivní predikáty.

**Obrázek 2** vertikální dělení – použitím elastického dotazu query referenčních dat

![Vertikální dělení – použitím elastického dotazu query referenčních dat][3]

**Dotazování napříč databázemi**: Elastické dotazy umožňují případy použití, které vyžadují dotazování napříč několika databázemi SQL. Obrázek 3 znázorňuje čtyři různé databáze: CRM, inventáře, HR a produkty. Dotazy v jedné z databází potřebovat přístup k jedné nebo všech jiných databází. Použitím elastického dotazu, můžete nakonfigurovat databázi pro tento případ spuštěním několika jednoduchých příkazů DDL každého ze čtyř databází. Po tomto jednorázovou konfiguraci je stejně jednoduché jako odkazující na místní tabulku z vašich dotazů T-SQL nebo z nástrojů BI přístup k vzdálené tabulky. Tento přístup se doporučuje, když vzdálené dotazy nevrátí velké výsledky.

**Obrázek 3** vertikální dělení – použitím elastického dotazu query napříč různými databázemi

![Vertikální dělení – použitím elastického dotazu query napříč různými databázemi][4]

Následující kroky konfigurace elastických databázových dotazů pro vertikální dělení scénáře, které vyžadují přístup k tabulce umístěné na vzdálené databáze SQL pomocí stejné schéma:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [Příkaz CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **relační databázový systém**
* [Příkaz CREATE/DROP externí tabulky](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po spuštění příkazů DDL, dostanete vzdálenou tabulku "mytable", jako by šlo místní tabulky. Azure SQL Database automaticky otevře připojení k vzdálené databázi, zpracování vašeho požadavku na vzdálenou databázi a vrátí výsledky.

## <a name="horizontal-partitioning---sharding"></a>Horizontální dělení – horizontální dělení

Použitím elastického dotazu se provedly horizontálně dělené úlohám generování sestav, to znamená, horizontálně dělené do oddílů, vyžaduje datovou vrstvu [mapy horizontálních oddílů elastické databáze](sql-database-elastic-scale-shard-map-management.md) k reprezentaci databáze v datové vrstvě. Obvykle se používá pouze jeden horizontální oddíl mapy v tomto scénáři a vyhrazené databáze s možnostmi elastický dotaz (hlavní uzel) slouží jako vstupní bod pro oznámení dotazů. Pouze této vyhrazené databáze potřebuje přístup k mapy horizontálních oddílů. Obrázek 4 ukazuje tuto topologii a jeho konfigurace s mapou elastický dotaz databáze a horizontálních oddílů. Databáze v datové vrstvě může být jakékoli edici nebo verzi databáze SQL Azure. Další informace o klientské knihovně elastic database a vytváření map horizontálních oddílů, naleznete v tématu [správy mapování horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md).

**Obrázek 4** horizontální dělení – použitím elastického dotazu pro generování sestav prostřednictvím vrstev horizontálně dělených dat

![Horizontální dělení – použitím elastického dotazu pro generování sestav prostřednictvím vrstev horizontálně dělených dat][5]

> [!NOTE]
> Elastický dotaz na databázi (hlavní uzel) může být samostatné databáze nebo může být stejné databáze, který je hostitelem mapy horizontálních oddílů.
> Libovolné konfigurace, zvolte možnost, ujistěte se, že úrovně služby a vypočítat velikost této databáze je dostatečný pro zpracování očekávaného počtu požadavků na přihlášení/dotazy.

Následující kroky konfigurace dotazy elastických databází pro horizontální dělení scénáře, které vyžadují přístup k sadě tabulek na (obvykle) několik vzdálené databáze SQL:

* [CREATE MASTER KEY](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) mycredential
* Vytvoření [mapy horizontálních oddílů](sql-database-elastic-scale-shard-map-management.md) představující datovou vrstvu pomocí Klientská knihovna elastic database.
* [Příkaz CREATE/DROP EXTERNAL DATA SOURCE](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql) mydatasource typu **SHARD_MAP_MANAGER**
* [Příkaz CREATE/DROP externí tabulky](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql) mytable

Po provedení těchto kroků, dostanete horizontálně dělenou tabulku "mytable", jako by šlo místní tabulku. Azure SQL Database automaticky otevře několik paralelní připojení ke vzdálené databáze fyzicky ukládat tabulky, zpracovává požadavky pro vzdálené databáze a vrátí výsledky.
Další informace o kroky potřebné pro horizontální dělení scénář lze najít v [elastický dotaz pro horizontální dělení](sql-database-elastic-query-horizontal-partitioning.md).

Pokud chcete začít, kódování, najdete v článku [Začínáme se službou elastický dotaz pro horizontální dělení (sharding)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Dotazování na T-SQL

Po definování externích datových zdrojů a externích tabulek, můžete použít regulární systému SQL Server připojovací řetězce k připojení k databázím, ve které jste definovali externí tabulky. Potom spustíte příkazy jazyka T-SQL na externí tabulky v tomto připojení s omezeními uvedených níže. Další informace a příklady dotazů T-SQL najdete v tématech dokumentace pro [horizontální dělení](sql-database-elastic-query-horizontal-partitioning.md) a [vertikální dělení](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Připojení nástroje

Můžete použít regulární systému SQL Server připojovací řetězce pro připojení vašich aplikací a nástrojů pro integraci BI nebo data do databáze, které mají externí tabulky. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro nástroj. Jakmile budete připojeni, odkazovat na dotaz na elastic database a externí tabulky v této databázi stejně jako u kterékoli jiné databáze systému SQL Server, se připojujete k s nástrojem.

> [!IMPORTANT]
> Ověřování pomocí Azure Active Directory s elastickými dotazy se momentálně nepodporuje.

## <a name="cost"></a>Náklady

Elastický dotaz je zahrnuté do ceny za databází Azure SQL Database. Všimněte si, že jsou podporované topologie, kde jsou vaše vzdálené databáze v různých datových center než koncový bod elastického dotazu, ale se pravidelně účtuje odchozí přenos dat ze vzdálených databází [sazeb Azure](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Omezení verze Preview

* Spuštění vaší první elastického dotazu může trvat několik minut, než na úrovni Standard služby. Tentokrát je nezbytný pro načtení elastický dotaz funkce. výkon při načítání se zvyšuje s vyšší úrovní služeb a výpočty velikostí.
* Skriptování z externích zdrojů dat nebo externích tabulek z aplikace SSMS a SSDT se ještě nepodporuje.
* Import/Export pro SQL DB zatím nepodporuje externí zdroje dat a externí tabulky. Pokud je potřeba použít Import/Export, vyřadit tyto objekty před exportem a potom je znovu vytvořit po importu.
* Elastický dotaz aktuálně podporuje pouze přístup jen pro čtení pro externí tabulky. Můžete však použít všechny funkce jazyka T-SQL v databázi ve kterém je definována externí tabulky. To může být užitečné, například zachovat dočasné použití, například výsledky, vyberte < column_list > do < local_table >, nebo k definování uložené procedury v databázi elastický dotaz odkazující na externí tabulky.
* S výjimkou nvarchar(max) typů LOB nepodporují v definici externí tabulky. Jako alternativní řešení můžete vytvořit zobrazení na vzdálenou databázi, která přetypovává typu LOB do nvarchar(max), definovat externí tabulky zobrazení místo v základní tabulce a přetypujte ji zpět do původního typu LOB v dotazech.
* Sloupce datového typu nvarchar(max) ve výsledku sady zakázat rozšířené dávkování technika použitý v implementaci elastický dotaz a může mít vliv na výkon dotazu pro řád, nebo dokonce u dvou řádově v jiné kanonické případy použití, kde je velké množství neagregovaná data přenášejí výsledku dotazu.
* Statistiky sloupce na externí tabulky nejsou aktuálně podporovány. Statistika tabulky jsou podporované, ale musí být vytvořeny ručně.

## <a name="feedback"></a>Váš názor

Sdílení zpětné vazby na vaše zkušenosti s elastickými dotazy s námi níže, na fórech MSDN nebo na webu Stack Overflow. Nás zajímají všechny druhy zpětnou vazbu týkající se služby (vad, hrubé okraje, funkce).

## <a name="next-steps"></a>Další postup

* Vertikální dělení kurz najdete v tématu [Začínáme s mezidatabázovými dotazy (vertikální oddíly)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy na vertikálně dělená data, najdete v části [dotazování na vertikálně dělené data)](sql-database-elastic-query-vertical-partitioning.md)
* Horizontální dělení (sharding) kurz najdete v tématu [Začínáme se službou elastický dotaz pro horizontální dělení (sharding)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělená data, najdete v části [dotazování na horizontálně dělené data)](sql-database-elastic-query-horizontal-partitioning.md)
* Naleznete v tématu [sp\_provést \_vzdálené](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, která provádí příkaz jazyka Transact-SQL na jeden vzdálený Azure SQL Database nebo sadu databází, které slouží jako horizontální oddíly takovým vodorovné schéma vytváření oddílů.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
