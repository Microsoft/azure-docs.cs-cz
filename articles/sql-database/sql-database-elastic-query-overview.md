---
title: Přehled služby Azure SQL Database elastické dotazu | Microsoft Docs
description: Elastické dotaz můžete spustit dotaz jazyka Transact-SQL, která přesahuje více databází.
services: sql-database
manager: craigg
author: MladjoA
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: mlandzic
ms.openlocfilehash: 6367418fb07b2ab5b425609540c653678a207ebc
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Elastické dotazu Přehled služby Azure SQL Database (preview)
Funkce elastické dotazu (ve verzi preview) umožňuje spuštění dotazu jazyka Transact-SQL, která přesahuje více databází ve službě Azure SQL Database. Umožňuje provádět dotazy mezidatabázové přístup do vzdálených tabulek, připojovat a nástroje Microsoft a třetích stran (aplikace Excel, PowerBI, Tableau atd.) k dotazování mezi datové vrstvy s více databází. Pomocí této funkce můžete škálovat dotazy do vrstev velkých objemů dat v databázi SQL a zobrazit výsledky v sestavách business intelligence (BI).


## <a name="why-use-elastic-queries"></a>Proč používat elastické dotazy?

**Azure SQL Database**

Dotazování mezi databází Azure SQL zcela v T-SQL. To umožňuje jen pro čtení dotazování vzdálené databáze. To poskytuje možnost pro aktuální zákazníky místní systém SQL Server k migraci aplikace, které používají tři a čtyř částečný názvy nebo odkazovaný server k databázi SQL.

**K dispozici na úrovni standard**

Na úrovni Standard výkonu kromě úroveň výkonu Premium je podporován elastické dotaz. Na omezení výkonu pro nižší úrovně výkonu najdete v části na omezení verze Preview níže.

**Odešlete do vzdálené databáze**

Elastické dotazy můžete nyní nabízená parametry SQL vzdálené databáze pro provedení.

**Spuštění uložené procedury**

Spustit volání vzdálené uložené procedury nebo funkce remote pomocí [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714).

**Flexibilita**

Externí tabulky s elastické dotazu mohou nyní odkazovat na vzdálených tabulek s jiné schéma nebo název tabulky.

## <a name="elastic-query-scenarios"></a>Scénáře elastické dotazu

Cílem je usnadnit dotazování scénáře, kde více databází přispívat řádků do jednoho celkového výsledku. Dotaz můžete sestavit buď uživatele nebo aplikace přímo nebo nepřímo prostřednictvím nástroje, které jsou připojené k databázi. To je užitečné zejména při vytváření sestav, pomocí komerční integrace nástroje BI nebo data, nebo jakékoli aplikace, kterou nelze změnit. S elastické dotaz se můžete dotazovat napříč několika databází v nástroje, například aplikace Excel, PowerBI, Tableau nebo Cognos známou vlastnost připojení k systému SQL Server.
Dotaz elastické umožňuje snadný přístup k celou kolekci databáze pomocí dotazů vydaný SQL Server Management Studio nebo Visual Studio a usnadňuje mezidatabázové dotazování z Entity Framework nebo jiných ORM prostředích. Obrázek 1 ukazuje scénář, kde existující cloudových aplikací (které používá [klientské knihovny pro elastické databáze](sql-database-elastic-database-client-library.md)) staví na data Škálováním vrstvy a elastické dotazu se používá pro vytváření sestav napříč databáze.

**Obrázek 1** elastické dotaz byl použit na upraveným datové vrstvy

![Elastické dotaz byl použit na upraveným datové vrstvy][1]

Zákazník scénáře pro elastické dotazu jsou charakteristické následující topologie:

* **Vertikální dělení - mezidatabázové dotazy** (topologie 1): data svisle rozdělena mezi počet databází v datové vrstvě. Různé sady tabulek jsou obvykle umístěné na různých databází. To znamená, že schéma se liší v různých databází. Například všechny tabulky pro inventář je na jednu databázi, zatímco všechny tabulky související s monitorování účtů na druhý databáze. Běžné případy použití s touto topologií vyžadovat k dotazování mezi nebo zkompilovat sestavy mezi tabulkami v několika databází.
* **Vodorovné dělení - horizontálního dělení** (topologie 2): Data vodorovně rozdělena distribuci řádků přes škálovaný dat vrstvy. S tímto přístupem schéma je stejná na všechny zúčastněné databáze. Tento přístup je také označován "horizontálního dělení". Provést horizontálního dělení a spravované (1) elastické databáze pomocí nástrojů pro knihovny nebo (2) horizontálního samoobslužné dělení. Elastické dotazu se používá k dotazování nebo zkompilovat napříč mnoha horizontálních oddílů sestavy.

> [!NOTE]
> Elastické dotazu je nejvhodnější pro příležitostně scénáře vytváření sestav, kde lze provádět většinu zpracování na datové vrstvě. Pro scénáře s více složitých dotazů datových skladů nebo náročných úloh vytváření sestav, také zvažte použití [Azure SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Vertikální dělení - mezidatabázové dotazy

Pokud chcete začít, kódování, najdete v části [Začínáme s mezidatabázové dotazu (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).

Elastické dotazu je použít k datům umístěným v databázi SQL, který je k dispozici pro jiné databáze SQL. To umožňuje dotazy z jedné databáze odkazující na tabulky v jiné vzdálené databáze SQL. Prvním krokem je definování externího zdroje dat pro každou vzdálenou databázi. Externí zdroj dat je definován v místní databázi, ze kterého chcete získat přístup k tabulkám, na které se nachází na vzdálené databáze. Žádné změny jsou nezbytné pro vzdálenou databázi. Pro typické vertikální dělení scénáře kde různých databází mají různé schémat elastické dotazy slouží k implementaci běžné případy použití, jako je přístup k datům referenčního a mezidatabázové dotazování.

> [!IMPORTANT]
> Musíte mít oprávnění ALTER ANY EXTERNAL DATA SOURCE. Toto oprávnění je součástí oprávnění ALTER DATABASE. K odkazování na podkladový zdroj dat jsou potřeba oprávnění ALTER ANY EXTERNAL DATA SOURCE.
>

**Referenční data**: topologii se používá pro správu dat na odkaz. Na obrázku jsou dvě tabulky (T1 a T2) u referenčních dat ukládají na vyhrazené databázi. Pomocí elastické dotazu, můžete nyní přistupovat tabulky T1 a T2 vzdáleně z jiných databází, jak je znázorněno na obrázku. Použít topologii 1, pokud referenční tabulky jsou malé nebo vzdálené dotazy na referenční tabulku mít selektivní predikáty.

**Obrázek 2** vertikální dělení – pomocí elastické dotazu pro dotaz referenční data

![Vertikální dělení – pomocí elastické dotazu pro dotaz referenční data][3]

**Dotazování mezidatabázové**: Elastická dotazuje povolit případy použití, které vyžadují dotazování napříč několika databází SQL. Obrázek 3 ukazuje čtyř různých databází: CRM, inventáře, HR a produkty. Dotazy v jedné z databází provést také potřebovat přístup k jeden nebo všechny ostatní databáze. Pomocí elastické dotazu, můžete nakonfigurovat databázi pro tento případ spuštěním několik jednoduchých příkazy DDL na každé čtyři databáze. Po této jednorázovou konfiguraci přístup k vzdálené tabulky je stejně jednoduché jako odkazující na místní tabulku z vašich dotazů T-SQL nebo z vaší nástrojů BI. Tento postup se doporučuje, když vzdálených dotazů nevrátí velké výsledky.

**Obrázek 3** vertikální dělení – pomocí Elastická dotazu pro dotaz napříč různými databázemi

![Vertikální dělení – pomocí Elastická dotazu pro dotaz napříč různými databázemi][4]

Následující kroky konfigurace dotazy elastické databáze pro vertikální dělení scénáře, které vyžadují přístup k tabulce nachází na vzdálené SQL databáze se stejným schématem:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* [Příkaz CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **RDBMS**
* [Příkaz CREATE/DROP externí tabulky](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po spuštění příkazů DDL, můžete přístup k vzdálené tabulce "mytable", jako by šlo místní tabulky. Databáze SQL Azure automaticky vytvoří připojení ke vzdálené databázi, zpracuje požadavek na vzdálené databáze a vrátí výsledky.

## <a name="horizontal-partitioning---sharding"></a>Vodorovné rozdělení do oddílů - horizontálního dělení
Pomocí elastické dotazu úkoly generování sestav prostřednictvím horizontálně dělené tj, vodorovně rozdělena na oddíly, vyžaduje datovou vrstvu [mapy horizontálního oddílu elastické databáze](sql-database-elastic-scale-shard-map-management.md) k reprezentaci databáze datové vrstvy. Obvykle v tomto scénáři se používá jenom jeden horizontálních mapu a vyhrazené databáze s možností elastické dotazu (hlavního uzlu) slouží jako vstupní bod pro vytváření sestav dotazy. Pouze tuto databázi vyhrazené potřebuje přístup k mapě horizontálního oddílu. Na obrázku 4 jsou tato topologie a jeho konfigurace s dotazu elastické databáze a horizontálního oddílu mapy. Databáze v datové vrstvě může mít pro všechny databáze SQL Azure verzi nebo edici. Další informace o klientské knihovny pro elastické databáze a vytváření mapy horizontálního oddílu najdete v tématu [horizontálního oddílu mapy správu](sql-database-elastic-scale-shard-map-management.md).

**Obrázek 4** vodorovné rozdělení do oddílů – pomocí elastické dotazu pro vytváření sestav v horizontálně dělené datové vrstvy

![Vodorovné rozdělení do oddílů – pomocí elastické dotazu pro vytváření sestav v horizontálně dělené datové vrstvy][5]

> [!NOTE]
> Elastické databáze (hlavního uzlu) dotazu může být samostatné databáze, nebo může být stejné databáze, který je hostitelem mapy horizontálního oddílu. Konfigurace, ať si zvolíte, zajistěte, aby tuto úroveň služby a výkon této databáze je dostatečné pro zpracování očekávaného počtu požadavků na přihlášení nebo dotazy.

Následující kroky konfigurace dotazy elastické databáze pro vodorovné rozdělení scénáře vyžadující přístup k sadě tabulky, které se nacházejí na (obvykle) několik vzdálenou databází SQL:

* [CREATE MASTER KEY](https://msdn.microsoft.com/library/ms174382.aspx) mymasterkey
* [CREATE DATABASE SCOPED CREDENTIAL](https://msdn.microsoft.com/library/mt270260.aspx) mycredential
* Vytvoření [horizontálního oddílu mapy](sql-database-elastic-scale-shard-map-management.md) představující vaše datové vrstvy pomocí klientské knihovny elastické databáze.   
* [Příkaz CREATE/DROP EXTERNAL DATA SOURCE](https://msdn.microsoft.com/library/dn935022.aspx) mydatasource typu **SHARD_MAP_MANAGER**
* [Příkaz CREATE/DROP externí tabulky](https://msdn.microsoft.com/library/dn935021.aspx) mytable

Po provedení těchto kroků můžete přistupovat horizontálně dělenou tabulku "mytable", jako by šlo místní tabulky. Azure SQL Database automaticky otevře více paralelních připojení k vzdálené databáze, kde se fyzicky nacházejí v tabulkách, zpracovává požadavky na vzdálenou databází a vrátí výsledky.
Další informace o kroky potřebné pro vodorovné rozdělení scénář lze nalézt v [elastické dotazu pro vodorovné rozdělení do oddílů](sql-database-elastic-query-horizontal-partitioning.md).

Pokud chcete začít, kódování, najdete v části [Začínáme s elastické dotazu pro vodorovné rozdělení do oddílů (horizontálního dělení)](sql-database-elastic-query-getting-started.md).

## <a name="t-sql-querying"></a>Dotazování T-SQL
Po definování zdrojů externích dat a externí tabulky můžete regulární připojovací řetězce SQL serveru pro připojení k databázím, kde definovány externí tabulky. Potom spuštěním příkazů T-SQL na externí tabulky na toto připojení se omezeních uvedených níže. Další informace a příklady dotazů T-SQL můžete najít v tématech dokumentace pro [vodorovné rozdělení do oddílů](sql-database-elastic-query-horizontal-partitioning.md) a [vertikální dělení](sql-database-elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Připojení nástroje
Regulární řetězce připojení SQL Server můžete použít pro připojení k databázím, které mají externí tabulky vaše aplikace a nástrojů pro integraci BI nebo data. Ujistěte se, že systém SQL Server je podporovaný jako zdroj dat pro vaše nástroje. Po připojení naleznete stejně, jako by provést v jiné databázi systému SQL Server, kterou je připojena vaše nástrojem pro databáze elastické dotazu a externí tabulky v databázi.

> [!IMPORTANT]
> Ověřování pomocí služby Azure Active Directory s elastické dotazy není aktuálně podporován.
> 
> 

## <a name="cost"></a>Náklady
Elastické dotazu je zahrnut do náklady databáze Azure SQL Database. Všimněte si, že jsou podporované topologie, kde jsou vaše vzdálené databáze v různých datových center než koncový bod elastické dotazu, ale budou se regular účtovat odchozí data ze vzdálených databází [Azure sazby](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="preview-limitations"></a>Omezení verze Preview
* Spuštění vaší první elastické dotazu může trvat až několik minut na úrovni Standard výkonu. Tato doba je nezbytný pro načtení elastické dotazovacích funkcí; načítání výkonu zlepšuje s vyšší úrovně výkonu.
* Externí zdroje dat nebo externí tabulky z aplikace SSMS nebo rozšíření SSDT skriptu se ještě nepodporuje.
* Import a Export pro SQL DB zatím nepodporuje externí zdroje dat a externí tabulky. Pokud budete muset použít importu a exportu, vyřaďte tyto objekty před exportem a potom je znovu vytvořit po importu.
* Elastické dotazu aktuálně podporuje jenom přístup jen pro čtení k externí tabulky. Můžete však použít plnou funkčnost T-SQL v databázi kterých byla definována externí tabulky. To může být užitečné, například zachovat dočasné výsledky, které používáte, například, vyberte < column_list > do < local_table >, nebo k definování uložené procedury v databázi elastické dotazu, které odkazují na externí tabulky.
* S výjimkou nvarchar(max) typů LOB nepodporují v definicích externí tabulky. Jako alternativní řešení můžete vytvořit zobrazení vzdálené databáze, která vrhá typu LOB do nvarchar(max), definovat externí tabulky přes zobrazení namísto na základní tabulku a převést jej zpět do původní typu LOB v dotazech.
* Statistiky sloupce na externí tabulky nejsou aktuálně podporovány. Tabulky statistiky jsou podporovány, ale musí ručně vytvořit.

## <a name="feedback"></a>Váš názor
Předejte zpětná vazba týkající se vašich zkušeností s elastické dotazy s námi Livefyre níže, fórech MSDN nebo v zásobníku. Snažíme se ve všech druhy zpětnou vazbu o službu (závad, případné nedostatky, funkce mezery).

## <a name="next-steps"></a>Další postup

* Vertikální dělení kurzu, najdete v části [Začínáme s mezidatabázové dotazu (vertikální dělení)](sql-database-elastic-query-getting-started-vertical.md).
* Syntaxe a ukázkové dotazy pro svisle oddílů data, najdete v části [dotazování svisle na oddíly dat)](sql-database-elastic-query-vertical-partitioning.md)
* Podívejte se kurz vodorovné rozdělení do oddílů (horizontálního dělení) [Začínáme s elastické dotazu pro vodorovné rozdělení do oddílů (horizontálního dělení)](sql-database-elastic-query-getting-started.md).
* Syntaxe a ukázkové dotazy pro horizontálně dělenou data, najdete v části [dotazování vodorovně rozdělena na oddíly dat)](sql-database-elastic-query-horizontal-partitioning.md)
* V tématu [sp\_provést \_vzdáleného](https://msdn.microsoft.com/library/mt703714) pro uloženou proceduru, který spouští příkaz jazyka Transact-SQL na jedné vzdálené databáze SQL Azure nebo sadu databází, které slouží jako horizontálních oddílů v vodorovné schéma rozdělení oddílů.

<!--Image references-->
[1]: ./media/sql-database-elastic-query-overview/overview.png
[2]: ./media/sql-database-elastic-query-overview/topology1.png
[3]: ./media/sql-database-elastic-query-overview/vertpartrrefdata.png
[4]: ./media/sql-database-elastic-query-overview/verticalpartitioning.png
[5]: ./media/sql-database-elastic-query-overview/horizontalpartitioning.png

<!--anchors-->
