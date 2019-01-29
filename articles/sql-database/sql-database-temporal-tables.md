---
title: Začínáme s dočasnými tabulkami ve službě Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak začít s používáním dočasných tabulek ve službě Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab
manager: craigg
ms.date: 03/21/2018
ms.openlocfilehash: ce489bae3a59da47ad6f3677ef493618d01fd6b6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55196646"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Začínáme s dočasnými tabulkami ve službě Azure SQL Database
Dočasné tabulky jsou novou funkcí programovatelnosti služby Azure SQL Database, která umožňuje sledovat a analyzovat kompletní historii změn ve vašich datech, bez nutnosti psaní vlastního kódu. Dočasných tabulek se zachovat data úzce souvisí s místní čas tak, aby uložené údaje se dá interpretovat jako platný pouze v rámci konkrétní období. Tato vlastnost dočasných tabulek se umožňuje efektivní analýzy založené na čase a získávání informací z dat vývoj.

## <a name="temporal-scenario"></a>Dočasné scénář
Tento článek ukazuje postup využívat dočasných tabulek se ve scénáři aplikace. Předpokládejme, že chcete na nový web, který je vyvíjen od začátku nebo na existující web, který chcete rozšířit pomocí analýzy chování uživatelů aktivity sledování činnosti uživatelů. V tomto příkladu zjednodušené předpokládáme, že počet navštívených webových stránek během časového období je indikátor, který je potřeba zachytit a sledovat v databázi webu, který je hostovaný na Azure SQL Database. Cílem historické analýzy aktivity uživatelů je zobrazíte vstupy přepracovat webu a poskytovat lepší prostředí pro návštěvníkům.

Model databáze pro tento scénář je velmi jednoduché – s polem celá čísla, je reprezentována metriky aktivity uživatele **PageVisited**a je zachycená základní informace v profilu uživatele. Kromě toho pro analýzu podle času byste udržovali sadu řádků pro každého uživatele, kde každý řádek představuje počet stránek konkrétní uživatel navštívil v konkrétním časovém období.

![Schéma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Naštěstí není potřeba umístit žádné úsilí ve vaší aplikaci k Udržovat informace o této aktivitě. S dočasnými tabulkami je tento proces automatizovat - poskytuje úplnou flexibilitu při návrhu webu a získejte víc času a zaměřte se na analýzu dat, samotného. Jediné, co musíte udělat, je zajistit, aby **WebSiteInfo** tabulka je nakonfigurována jako [dočasné-systémovou](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Přesný postup využívat dočasných tabulek se v tomto scénáři jsou popsané níže.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1: Nakonfigurovat jako dočasné tabulky
V závislosti na tom, jestli jsou počáteční vývoj nových projektů nebo upgrade stávající aplikace bude vytvoření dočasné tabulky nebo upravte stávající tak, že přidáte dočasný atributy. Obecně případu, váš scénář mohou být kombinaci těchto dvou možností. Provést tyto akce pomocí [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) nebo jakýkoli jiný nástroj pro vývoj příkazů jazyka Transact-SQL.

> [!IMPORTANT]
> Doporučujeme vám vždy používat nejnovější verzi aplikace Management Studio, aby se zajistila synchronizovanost s aktualizacemi Microsoft Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Vytvořit novou tabulku
Pomocí položky kontextové nabídky "Novou-systémovou tabulku" v Průzkumníku objektů aplikace SSMS otevřete editor dotazů se skriptem šablony dočasnou tabulku a pak použít "Zadejte hodnoty pro parametry šablony" (Ctrl + Shift + M) pro naplnění šablony:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

V sadě SSDT zvolte šablonu "Dočasnou tabulku (systém správy verzí)" při přidávání nových položek do projektu databáze. Který otevře se Návrhář tabulky a vám umožní snadno určit rozložení tabulky:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Můžete také vytvořit dočasnou tabulku zadáním příkazů jazyka Transact-SQL přímo, jak je znázorněno v následujícím příkladu. Mějte na paměti, že jsou povinné prvky každý dočasné tabulky definice období a tabulku v klauzuli SYSTEM_VERSIONING s odkazem na jiného uživatele, který uloží verze historických řádků:

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

Při vytváření dočasné tabulce se systémovou správou související tabulky historie s výchozí konfigurací je automaticky vytvořen. Tabulky historie výchozí obsahuje clusterovaného indexu B-stromu na sloupce období (konec, start) se stránka komprese zapnuta. Tato konfigurace je ideální pro většinu scénářů, ve kterých se používají dočasných tabulek, zejména u [auditování dat](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

V tomto konkrétním případě usilujeme o k analýze trendů na základě času přes delší historii dat a s větší sady dat, takže volby úložiště pro tabulku historie clusterovaný index columnstore. Clusterované columnstore poskytuje velmi dobrou kompresi a výkon pro analytické dotazy. Dočasné tabulky poskytují flexibilitu zcela nezávisle na sobě konfigurovat indexů na aktuální a dočasné tabulky. 

> [!NOTE]
> Indexy Columnstore jsou k dispozici na úrovni Premium a na úrovni Standard S3 a výše.
>

Tento skript je ukázkou, jak lze změnit výchozí index pro tabulku historie je clusterovaný index columnstore:

```
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
```

Dočasné tabulky jsou reprezentovány v Průzkumníku objektů s konkrétní ikony pro snazší identifikaci během své historie tabulky se zobrazí jako podřízený uzel.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Změnit existující tabulku na dočasnou
Pojďme zahrnují alternativní scénář, ve kterém WebsiteUserInfo tabulce již existuje, ale nebyl navržen, aby uchovávala historie změn. V takovém případě můžete jednoduše rozšířit existující tabulky k dočasné, jak je znázorněno v následujícím příkladu:

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

## <a name="step-2-run-your-workload-regularly"></a>Krok 2: Pravidelně spouštět vaše úlohy
Hlavní výhodou dočasných tabulek je, že není nutné změnit nebo upravit svůj web v žádný způsob, jak provádět sledování změn. Po vytvoření dočasných tabulek se transparentně zachovat předchozí verze řádků pokaždé, když provedete změny na vašich datech. 

Aby bylo možné plně využívat automatické sledování změn pro tento konkrétní scénář, teď právě aktualizovat sloupec **PagesVisited** pokaždé, když se po ukončení jeho/její relace na webu pro uživatele:

```
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
```

Je důležité si všimněte, že dotaz pro aktualizaci nepotřebuje vědět přesný čas, kdy aktuální operace došlo k chybě, ani způsob, jakým se zachovají historická data pro pozdější analýzu. Oba tyto aspekty jsou automaticky zpracovány služba Azure SQL Database. Následující diagram znázorňuje, jak data historie je generována při každé aktualizaci.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3: Analýza historických dat
Teď když je povolené dočasné systému – Správa verzí, analýza historických dat je pouze jeden dotaz od vás. V tomto článku budeme poskytovat pár příkladů, které se vztahují k běžným scénářům analysis – Další informace najdete všechny podrobnosti, prozkoumejte různé možnosti, které nepředchází funkce [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) klauzuli.

Pokud chcete zobrazit 10 nejčastějších uživatelů seřazených podle čísla navštívených webových stránek v době hodinou, spusťte tento dotaz:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Můžete snadno upravit tento dotaz k analýze návštěvy webu od předchozí, den před měsícem, nebo v libovolném okamžiku v minulosti si přejete.

K provádění statistických analýz základní za předchozí den, použijte následující příklad:

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

K vyhledání konkrétního uživatele, aktivity v určitou dobu, použijte klauzuli obsažené:

```
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafická vizualizace je zvlášť vhodné pro dočasných dotazů, jak můžete zobrazit trendy a vzorce používání v intuitivního způsobem velmi snadno:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Vyvíjejí schématu tabulky
Obvykle je potřeba změnit dočasná tabulka schématu, zatímco provádíte vývoj aplikací. K tomu stačí spustit regulární příkaz ALTER TABLE příkazy a Azure SQL Database bude odpovídajícím způsobem šířící změny do tabulky historie. Tento skript je ukázkou, jak můžete přidat další atribut pro sledování:

```
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

Podobně můžete změnit definici sloupce je aktivní úlohy:

```
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Nakonec můžete odebrat sloupec, který už nepotřebujete.

```
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
```

Můžete také použít nejnovější [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) změnit dočasná tabulka schématu jsou připojeny k databázi (online režim) nebo jako součást projektu databáze (offline režim).

## <a name="controlling-retention-of-historical-data"></a>Řízení uchování historických dat.
Pomocí dočasných tabulek se systémovou správou může zvýšit tabulky historie regulární tabulky velikost databáze více než. Tabulky historie velká a neustále se rozšiřujícímu může být problém i z důvodu náklady na úložiště pure, jakož i nastavení výkonu daň z dočasné dotazování. Proto vývoj zásadu uchovávání dat pro správu dat v tabulce historie je důležitou součástí plánování a správu životního cyklu každý dočasnou tabulku. S Azure SQL Database máte pro správu historických dat v dočasné tabulce následujících postupů:

* [Dělení tabulky](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Skript pro vyčištění vlastní](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Další postup
Podrobné informace o dočasných tabulek, projděte si [dokumentaci MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Navštivte web Channel 9 a poslechněte si [skutečné dočasné implementace úspěch příběh a zjistěte](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) a sledujte [live dočasné ukázku](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

