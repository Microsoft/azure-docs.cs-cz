---
title: Začínáme s dočasnými tabulkami
description: Naučte se, jak začít používat dočasné tabulky v Azure SQL Database a v Azure SQL Managed instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 06/26/2019
ms.openlocfilehash: ea037d12417c8fad9d80b77df69285ed2c8df31b
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91618654"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Začínáme s dočasnými tabulkami v Azure SQL Database a Azure SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Dočasné tabulky jsou programovatelností Azure SQL Database a spravované instance Azure SQL, která umožňuje sledovat a analyzovat úplnou historii změn ve vašich datech bez nutnosti vlastního kódování. Dočasné tabulky udržují data úzce související s časovým kontextem, takže uložené fakta lze interpretovat jako platné pouze v rámci konkrétního období. Tato vlastnost dočasných tabulek umožňuje efektivně analyzovat časovou analýzu a získávat přehledy z vývoje dat.

## <a name="temporal-scenario"></a>Dočasný scénář

Tento článek popisuje kroky, jak využít dočasné tabulky ve scénáři použití aplikace. Předpokládejme, že chcete sledovat aktivitu uživatelů na novém webu, který je vyvíjen od začátku, nebo na stávajícím webu, který chcete s analýzou aktivity od uživatele zvětšit. V tomto zjednodušeném příkladu předpokládáme, že počet navštívených webových stránek během časového období je ukazatel, který se musí zachytit a monitorovat v databázi webu, která je hostovaná v Azure SQL Database nebo Azure SQL Managed instance. Cílem historické analýzy aktivity uživatelů je získat vstupy pro změnu návrhu webu a poskytování lepší zkušenosti pro návštěvníky.

Databázový model pro tento scénář je velmi jednoduchý – metrika aktivity uživatele je reprezentovaná jediným polem typu Integer, **PageVisited**a je zachyceno spolu se základními informacemi o profilu uživatele. V případě analýzy založené na čase byste navíc měli pro každého uživatele zachovávat řadu řádků, kde každý řádek představuje počet stránek, které konkrétní uživatel navštívil během konkrétního časového období.

![Schéma](./media/temporal-tables/AzureTemporal1.png)

Naštěstí nemusíte do vaší aplikace nadávat žádné úsilí, abyste zachovali informace o této aktivitě. Díky dočasným tabulkám je tento proces automatizovaný – poskytuje plnou flexibilitu při návrhu webů a další čas na analýzu dat, která je samotná. Jedinou věcí, kterou musíte udělat, je zajistit, aby byla tabulka **WebSiteInfo** nakonfigurovaná jako [dočasná systémovou správou verzí](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table). Přesné kroky k využití dočasných tabulek v tomto scénáři jsou popsány níže.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1: Konfigurace tabulek jako dočasné

V závislosti na tom, jestli spouštíte nový vývoj nebo upgradujete stávající aplikaci, vytvoříte dočasné tabulky nebo upravíte stávající, a to přidáním dočasných atributů. V obecném případě může být váš scénář kombinací těchto dvou možností. Tuto akci proveďte pomocí [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT), [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)nebo jakéhokoli jiného vývojového nástroje Transact-SQL.

> [!IMPORTANT]
> Doporučujeme vždy používat nejnovější verzi Management Studio, aby zůstala synchronizovaná s aktualizacemi Azure SQL Database a Azure SQL Managed instance. [Aktualizovat aplikaci SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>Vytvořit novou tabulku

Pomocí položky místní nabídky "nová tabulka se systémovou správou" v SSMS Průzkumník objektů otevřete Editor dotazů s dočasnou šablonou tabulky a pak použijte "zadejte hodnoty pro parametry šablony" (CTRL + SHIFT + M) k naplnění šablony:

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

V SSDT vyberte šablonu "dočasná tabulka (systémovou verzi)" při přidávání nových položek do projektu databáze. Otevře se Návrhář tabulky a umožní vám snadno zadat rozložení tabulky:

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

Můžete také vytvořit dočasnou tabulku zadáním příkazu jazyka Transact-SQL přímo, jak je znázorněno v následujícím příkladu. Všimněte si, že povinné prvky každé dočasné tabulky jsou definice období a klauzule SYSTEM_VERSIONING s odkazem na jinou uživatelskou tabulku, která bude ukládat verze historických řádků:

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

Když vytvoříte dočasnou tabulku se systémovou správou verzí, bude automaticky vytvořena dočasná tabulka historie s výchozí konfigurací. Výchozí tabulka historie obsahuje clusterovaný index B-Tree ve sloupcích období (konec, začátek) s povolenou kompresí stránky. Tato konfigurace je ideální pro většinu scénářů, ve kterých se používají dočasné tabulky, zejména pro [Auditování dat](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit).

V tomto konkrétním případě se zaměřujeme na analýzu trendů na základě času s delší historií dat a většími datovými sadami, takže volba úložiště pro tabulku historie je clusterovaný index columnstore. Clusterovaný cluster columnstore poskytuje pro analytické dotazy velmi dobrou kompresi a výkon. Dočasné tabulky poskytují flexibilitu pro zcela nezávisle nakonfigurovat indexy pro aktuální a dočasné tabulky.

> [!NOTE]
> Indexy columnstore jsou dostupné v úrovních Pro důležité obchodní informace, Pro obecné účely a Premium a na úrovni Standard, S3 a vyšší.

Následující skript ukazuje, jak se výchozí index v tabulce historie dá změnit na clusterovaný columnstore:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Dočasné tabulky jsou reprezentovány v Průzkumník objektů s konkrétní ikonou pro snazší identifikaci, zatímco její tabulka historie se zobrazuje jako podřízený uzel.

![Alter – příkaz](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Změnit existující tabulku na dočasná

Podíváme se na alternativní scénář, ve kterém už tabulka WebsiteUserInfo existuje, ale není navržená tak, aby udržovala historii změn. V takovém případě můžete jednoduše zvětšit existující tabulku tak, aby se stala dočasná, jak je znázorněno v následujícím příkladu:

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>Krok 2: pravidelně spouštějte vaše úlohy

Hlavní výhodou dočasných tabulek je, že nemusíte měnit ani upravovat svůj web jakýmkoli způsobem, abyste mohli provádět sledování změn. Po vytvoření dočasné tabulky transparentně uchovávají předchozí verze řádků pokaždé, když provedete úpravy dat.

Aby bylo možné využít automatické sledování změn pro tento konkrétní scénář, můžeme jenom aktualizovat sloupec **PagesVisited** pokaždé, když uživatel ukončí svou relaci na webu:

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

Je důležité si všimnout, že aktualizační dotaz nemusí znát přesný čas, kdy došlo ke skutečné operaci, a jak budou uchována historická data pro budoucí analýzu. Obě tyto aspekty se automaticky zpracovávají pomocí Azure SQL Database a spravované instance Azure SQL. Následující diagram znázorňuje, jak se generují data historie při každé aktualizaci.

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3: provedení historických analýz dat

Teď, když je zapnutá časová verze systému, je historická analýza dat jenom jedním dotazem. V tomto článku budeme poskytovat několik příkladů, které řeší běžné scénáře analýzy – Pokud se chcete dozvědět víc, prozkoumejte různé možnosti, které se zavedly s klauzulí [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data) .

Pokud chcete zobrazit prvních 10 uživatelů seřazených podle počtu navštívených webových stránek před hodinou, spusťte tento dotaz:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Tento dotaz můžete snadno upravit, abyste mohli analyzovat návštěvy webu před dnešním dnem, před měsícem nebo v jakémkoli okamžiku v minulosti.

Chcete-li provést základní statistickou analýzu předchozího dne, použijte následující příklad:

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Pokud chcete v časovém intervalu vyhledat aktivity konkrétního uživatele, použijte klauzuli OBSAŽENou v:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafická vizualizace je zvláště užitečná pro dočasné dotazy, protože můžete snadno snadno zobrazit trendy a způsoby použití:

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Vývoj schématu tabulky

Obvykle budete muset při vývoji aplikací změnit schéma dočasné tabulky. V takovém případě stačí spustit příkazy ALTER TABLE a Azure SQL Database nebo spravovaná instance Azure SQL vhodně rozšíří změny v tabulce historie. Následující skript ukazuje, jak můžete přidat další atribut pro sledování:

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Podobně můžete změnit definici sloupce, když je vaše zatížení aktivní:

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Nakonec můžete odebrat sloupec, který už nepotřebujete.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

Případně můžete použít nejnovější [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) ke změně schématu dočasná tabulky, když jste připojeni k databázi (online režim), nebo jako součást projektu databáze (offline režim).

## <a name="controlling-retention-of-historical-data"></a>Řízení uchovávání historických dat

S dočasnými tabulkami se systémovou správou verzí může tabulka historie zvýšit velikost databáze na více než běžných tabulkách. Ve velké a stále rostoucí tabulce historie se může jednat o problém, který je v důsledku čistých nákladů na úložiště a také při dočasném dotazování na výkon. Proto je vývoj zásad uchovávání dat pro správu dat v tabulce historie důležitým aspektem plánování a správy životního cyklu každé dočasné tabulky. Díky Azure SQL Database a službě Azure SQL Managed instance máte k dispozici následující přístupy ke správě historických dat v dočasné tabulce:

- [Dělení tabulky](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [Vlastní čisticí skript](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>Další kroky

- Další informace o dočasných tabulkách najdete v tématu věnovaném rezervám [dočasné tabulky](/sql/relational-databases/tables/temporal-tables).
- Navštivte kanál 9, kde se dozvíte, jak se [zákazníkem dočasná implementace](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) doznala a sledujte [živý dočasný názor](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
