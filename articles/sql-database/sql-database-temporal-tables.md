---
title: Začínáme s dočasnými tabulkami
description: Zjistěte, jak začít s používáním dočasných tabulek v Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
ms.date: 06/26/2019
ms.openlocfilehash: 98fd2658f3fbcb0e7e29114d29f8dc6ed39eedf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820722"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Začínáme s dočasnými tabulkami v Azure SQL Database

Časové tabulky jsou nová funkce programovatelnosti azure SQL database, která umožňuje sledovat a analyzovat úplnou historii změn v datech, bez nutnosti vlastního kódování. Časové tabulky uchovávají data úzce související s časovým kontextem, takže uložená fakta mohou být interpretována jako platná pouze v určitém období. Tato vlastnost temporálních tabulek umožňuje efektivní analýzu založenou na čase a získávání poznatků z vývoje dat.

## <a name="temporal-scenario"></a>Časový scénář

Tento článek ilustruje kroky k využití časové tabulky ve scénáři aplikace. Předpokládejme, že chcete sledovat aktivitu uživatelů na novém webu, který je vyvíjen od začátku, nebo na existujícím webu, který chcete rozšířit pomocí analýzy aktivit uživatelů. V tomto zjednodušeném příkladu předpokládáme, že počet navštívených webových stránek během určitého časového období je indikátor, který je potřeba zachytit a sledovat v databázi webu, která je hostovaná v Azure SQL Database. Cílem historické analýzy aktivity uživatelů je získat vstupy pro redesign webových stránek a poskytnout návštěvníkům lepší zážitek.

Databázový model pro tento scénář je velmi jednoduchý - metrika aktivity uživatele je reprezentována jedním celočíselným polem **PageVisited**a je zachycena spolu se základními informacemi o profilu uživatele. Kromě toho pro analýzu založenou na čase byste zachovat řadu řádků pro každého uživatele, kde každý řádek představuje počet stránek, které konkrétní uživatel navštívil v určitém časovém období.

![Schéma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Naštěstí nemusíte v aplikaci vynaložit žádné úsilí na údržbu těchto informací o aktivitě. S temporálními tabulkami je tento proces automatizován - poskytuje vám plnou flexibilitu při navrhování webových stránek a více času na zaměření se na samotnou analýzu dat. Jediné, co musíte udělat, je zajistit, aby **webSiteInfo** tabulka byla nakonfigurována jako [časová verze systému](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Přesné kroky pro využití časových tabulek v tomto scénáři jsou popsány níže.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1: Konfigurace tabulek jako dočasné
V závislosti na tom, zda spouštíte nový vývoj nebo upgradujete existující aplikaci, vytvoříte časové tabulky nebo upravíte existující tabulky přidáním časových atributů. Obecně platí, že váš scénář může být kombinací těchto dvou možností. Tyto akce proveďte pomocí [sql server management studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), SQL Server Datové [nástroje](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) nebo jakýkoli jiný vývojový nástroj Transact-SQL.

> [!IMPORTANT]
> Doporučujeme vám vždy používat nejnovější verzi aplikace Management Studio, aby se zajistila synchronizovanost s aktualizacemi Microsoft Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Vytvořit novou tabulku
Pomocí položky kontextové nabídky "Nová tabulka s verzí systému" v Průzkumníku objektů SSMS otevřete editor dotazů pomocí skriptu šablony časové tabulky a potom použijte "Zadat hodnoty pro parametry šablony" (Ctrl+Shift+M) k naplnění šablony:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

V SSDT zvolte šablonu "Temporální tabulka (Systémová verze)" při přidávání nových položek do databázového projektu. To otevře návrháře tabulek a umožní vám snadno určit rozložení tabulky:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Můžete také vytvořit temporální tabulku zadáním Příkazy Transact-SQL přímo, jak je znázorněno v příkladu níže. Všimněte si, že povinné prvky každé časové tabulky jsou definice OBDOBÍ a klauzule SYSTEM_VERSIONING s odkazem na jinou uživatelskou tabulku, která bude ukládat historické verze řádků:

```
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

Při vytváření časové tabulky s verzí systému se automaticky vytvoří doprovodná tabulka historie s výchozí konfigurací. Výchozí tabulka historie obsahuje seskupený index B-stromu ve sloupcích období (konec, začátek) s povolenou kompresí stránky. Tato konfigurace je optimální pro většinu scénářů, ve kterých se používají časové tabulky, zejména pro [auditování dat](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

V tomto konkrétním případě se snažíme provádět analýzu trendů na základě času v průběhu delší historie dat a s většími sadami dat, takže volba úložiště pro tabulku historie je index clusterovaného columnstore. Clustered columnstore poskytuje velmi dobrou kompresi a výkon pro analytické dotazy. Časové tabulky poskytují flexibilitu konfigurace indexů na aktuální a časové tabulky zcela nezávisle. 

> [!NOTE]
> Columnstore indexy jsou k dispozici na úrovni Premium a na úrovni Standard, S3 a vyšší.
>

Následující skript ukazuje, jak lze změnit výchozí index v tabulce historie na clusterované columnstore:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Časové tabulky jsou reprezentovány v Průzkumníku objektů s konkrétní ikonou pro snadnější identifikaci, zatímco jeho tabulka historie je zobrazena jako podřízený uzel.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Změna existující tabulky na časovou
Podívejme se na alternativní scénář, ve kterém tabulka WebsiteUserInfo již existuje, ale nebyla navržena tak, aby uchovávala historii změn. V takovém případě můžete jednoduše rozšířit existující tabulku tak, aby se stala časovou, jak je znázorněno v následujícím příkladu:

```
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

## <a name="step-2-run-your-workload-regularly"></a>Krok 2: Pravidelně spouštějte úlohy
Hlavní výhodou temporálních tabulek je, že nemusíte žádným způsobem měnit nebo upravovat své webové stránky, abyste provedli sledování změn. Po vytvoření časové tabulky transparentně zachovat předchozí verze řádku pokaždé, když provedete změny na data. 

Chcete-li využít automatické sledování změn pro tento konkrétní scénář, aktualizujte sloupec **PagesVisited** pokaždé, když uživatel ukončí svou relaci na webu:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Je důležité si všimnout, že aktualizační dotaz nepotřebuje znát přesný čas, kdy došlo ke skutečné operaci, ani jak budou historická data zachována pro budoucí analýzu. Oba aspekty jsou automaticky zpracovány databází Azure SQL Database. Následující diagram znázorňuje, jak jsou generována data historie při každé aktualizaci.

![Časová architektura](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3: Provedení analýzy historických dat
Nyní, když je povolena časová správa verzí systému, analýza historických dat je jen jeden dotaz od vás. V tomto článku poskytneme několik příkladů, které řeší běžné scénáře analýzy - seznámit se se všemi podrobnostmi, prozkoumat různé možnosti zavedené klauzulí [FOR SYSTEM_TIME.](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3)

Chcete-li zobrazit 10 nejlepších uživatelů seřazených podle počtu navštívených webových stránek před hodinou, spusťte tento dotaz:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Tento dotaz můžete snadno upravit tak, aby analyzoval návštěvy webu před evidencí, před měsícem nebo kdykoli v minulosti, které si přejete.

Chcete-li provést základní statistickou analýzu předchozího dne, použijte následující příklad:

```
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Chcete-li vyhledat aktivity konkrétního uživatele, v časovém období použijte klauzuli CONTAINED IN:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafická vizualizace je obzvláště vhodná pro časové dotazy, protože můžete velmi snadno zobrazit trendy a vzorce používání:

![Časograf](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Vyvíjející se schéma tabulky
Obvykle budete muset změnit schéma časové tabulky při vývoji aplikací. Za tímto účelem jednoduše spusťte pravidelné příkazy ALTER TABLE a Azure SQL Database bude odpovídajícím způsobem šířit změny v tabulce historie. Následující skript ukazuje, jak můžete přidat další atribut pro sledování:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Podobně můžete změnit definici sloupce, když je vaše pracovní vytížení aktivní:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Nakonec můžete odebrat sloupec, který již nepotřebujete.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Případně použijte nejnovější [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) změnit schéma časové tabulky, když jste připojeni k databázi (režim online) nebo jako součást databázového projektu (režim offline).

## <a name="controlling-retention-of-historical-data"></a>Řízení uchovávání historických dat
U časových tabulek s verzí systému může tabulka historie zvětšit velikost databáze více než běžné tabulky. Velká a stále rostoucí historie tabulka se může stát problémem jak z důvodu čisté náklady na úložiště, stejně jako uložení daně z výkonu na časové dotazování. Proto je důležitým aspektem plánování a správy životního cyklu každé časové tabulky vývoj zásad uchovávání dat pro správu dat v tabulce historie. S Azure SQL Database máte následující přístupy pro správu historických dat v časové tabulce:

* [Dělení tabulky](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Vlastní skript pro vyčištění](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Další kroky

- Další informace o dočasných tabulkách najdete v tématu [Časové tabulky](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables).
- Navštivte Channel 9 slyšet [skutečný zákazník časové provádění úspěch a](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) sledovat živé časové [demonstrace](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

