---
title: Začínáme s dočasné tabulky v databázi Azure SQL Database | Microsoft Docs
description: Zjistěte, jak začít s použitím dočasné tabulky v databázi SQL Azure.
services: sql-database
author: bonova
ms.date: 03/21/2018
manager: craigg
ms.service: sql-database
ms.custom: develop databases
ms.topic: conceptual
ms.author: bonova
ms.openlocfilehash: 140d2c9f6c334cec7d2761d05d7b20eb7106b9fd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649036"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database"></a>Začínáme s dočasné tabulky v databázi Azure SQL
Dočasné tabulky jsou novou funkcí programovatelnosti databáze SQL Azure, která umožňuje sledovat a analyzovat úplnou historii změn ve vašich datech bez nutnosti vlastní kódování. Dočasné tabulky zachovat data úzce související čas kontextu tak, aby uložené fakty lze interpretovat jako platný jen v určitou dobu. Tato vlastnost dočasné tabulky umožňuje efektivní analýzu založené na čase a získávání přehledy z dat vývoj.

## <a name="temporal-scenario"></a>Dočasné scénář
Tento článek ukazuje postup využívat dočasných tabulek se v případě pomocí aplikace. Předpokládejme, že chcete sledovat aktivity uživatelů na nový web, který je vyvíjen od začátku nebo na existující web, který chcete rozšířit pomocí analýzy chování aktivity uživatelů. V tomto příkladu zjednodušené budeme předpokládat, že je počet navštívené webové stránky během v časovém intervalu indikátor, který je třeba zaznamenat a sledovat v databázi webu, který je hostován na Azure SQL Database. Cílem historické analýzy aktivity uživatelů, je získat vstupy přepracovat webu a poskytuje lepší prostředí pro návštěvníkům.

Model databáze pro tento scénář je velmi jednoduchý – metrika aktivity uživatele je reprezentována s polem jeden celé číslo, **PageVisited**a zachytí společně s základní informace o profilu uživatele. Kromě toho pro čas na základě analýzy, byste udržovali řadu řádků pro každého uživatele, přičemž každý řádek představuje počet stránek konkrétní uživatel navštívil v konkrétním časovém období.

![Schéma](./media/sql-database-temporal-tables/AzureTemporal1.png)

Naštěstí není nutné uvést všechny úsilí v aplikaci, kterou chcete spravovat informace o této aktivity. U dočasných tabulek je tento proces automatizované - budete úplnou flexibilitu při návrhu webu a delší dobu a zaměřit se na samotný analýzy dat. Jediné, co musíte udělat, je zajistit, aby **WebSiteInfo** tabulka je nakonfigurovaný jako [dočasné systémovou správou verzí](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_0). Přesný postup využívat dočasných tabulek se v tomto scénáři jsou popsané níže.

## <a name="step-1-configure-tables-as-temporal"></a>Krok 1: Konfigurace jako dočasné tabulky
V závislosti na tom, jestli jsou spuštění vývoj nových nebo upgrade stávající aplikaci bude dočasné tabulky umožňuje vytvořit nebo upravit existující přidáním dočasné atributy. Obecně případu, váš scénář může jednat o kombinaci těchto dvou možností. Proveďte tyto akce pomocí [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) (SSMS), [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) (SSDT) nebo jakýkoli jiný nástroj pro vývoj Transact-SQL.

> [!IMPORTANT]
> Doporučujeme vám vždy používat nejnovější verzi aplikace Management Studio, aby se zajistila synchronizovanost s aktualizacemi Microsoft Azure a SQL Database. [Aktualizovat aplikaci SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

### <a name="create-new-table"></a>Vytvořit novou tabulku
Pomocí položky kontextové nabídky "Novou systémovou správou verzí tabulku" v Průzkumníku objektů aplikace SSMS otevřete editor dotazů pomocí skriptu dočasná tabulka šablony a potom pomocí "Zadejte hodnoty pro parametry šablony" (Ctrl + Shift + M) k naplnění šablony:

![SSMSNewTable](./media/sql-database-temporal-tables/AzureTemporal2.png)

V sadě SSDT zvolili při přidávání nových položek do k databázovému projektu šablony "Dočasnou tabulku (systémovou správou verzí)". Které otevřete návrháře tabulky a umožňují snadno určit rozložení tabulky:

![SSDTNewTable](./media/sql-database-temporal-tables/AzureTemporal3.png)

Můžete také vytvořit dočasnou tabulku zadáním příkazy jazyka Transact-SQL přímo, jak je znázorněno v následujícím příkladu. Upozorňujeme, že jsou povinné elementy každých dočasná tabulka definice období a klauzuli SYSTEM_VERSIONING s odkazem na jinou tabulku uživatele, které se uloží verze historických řádek:

````
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
````

Při vytváření dočasné tabulce se systémovou správou verzí se automaticky vytvoří doprovodné tabulce historie s výchozí konfigurací. Tabulka historie výchozí obsahuje clusterovaný index B-stromu na sloupce období (end, start) s stránky komprese zapnuta. Tato konfigurace je optimální pro většinu scénářů, ve kterých se používají dočasné tabulky, hlavně pro [auditování dat](https://msdn.microsoft.com/library/mt631669.aspx#Anchor_0). 

V tomto konkrétním případě usilujeme provádět analýzy trendů založené na čase přes delší data historie a s větší sady dat, takže volba úložiště pro tabulku historie je clusterovaný index columnstore. Clusterované columnstore poskytuje velmi dobré komprese a výkon pro analytické dotazy. Dočasné tabulky poskytují flexibilitu: Konfigurace indexy zcela nezávisle na aktuální a dočasné tabulky. 

> [!NOTE]
> Indexy Columnstore jsou dostupné v úrovni Premium a úrovně Standard S3 a vyšší.
>

Tento skript je ukázkou, jak lze změnit výchozí index pro tabulku historie na clusterových columnstore:

````
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON); 
````

Dočasné tabulky jsou reprezentované v Průzkumníku objektů s konkrétní ikonou pro snazší identifikaci při jeho tabulka historie se zobrazí jako podřízený uzel.

![AlterTable](./media/sql-database-temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Změna existující tabulky k dočasné
Pojďme se věnují alternativní scénář, ve kterém WebsiteUserInfo tabulce již existuje, ale nebyl navržen, aby historii změn. V takovém případě můžete jednoduše rozšířit existující tabulky k dočasné, jak je znázorněno v následujícím příkladu:

````
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
````

## <a name="step-2-run-your-workload-regularly"></a>Krok 2: Spuštění úlohy pravidelně
Hlavní výhodou dočasné tabulky je, že není nutné změnit nebo upravit svůj web žádným způsobem provést sledování změn. Po vytvoření dočasných tabulek se pokaždé, když provádíte změny na vaše data transparentně zachovat předchozí verze řádku. 

Aby mohli využít automatické sledování změn pro tento konkrétní scénář, umožňuje pouze aktualizovat sloupec **PagesVisited** pokaždé, když se při ukončení uživatele za své relace na webu:

````
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5 
WHERE [UserID] = 1;
````

Je důležité si všimněte si, že aktualizace dotaz nemusí vědět přesný čas při aktuální operace došlo k chybě, ani jak historická data se zachovají pro budoucí analýzu. Oba tyto aspekty jsou automaticky zpracovávány databáze SQL Azure. Následující diagram znázorňuje, jak má být vygenerován data o historii při každé aktualizaci.

![TemporalArchitecture](./media/sql-database-temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Krok 3: Provedení analýza historických dat
Teď když je povolené dočasné systému – Správa verzí, analýza historických dat je pouze jeden dotazu od vás. V tomto článku, poskytujeme několik příkladů, které řeší běžné scénáře analýzy - další všechny podrobnosti, prozkoumejte různé možnosti zavedené [FOR SYSTEM_TIME](https://msdn.microsoft.com/library/dn935015.aspx#Anchor_3) klauzule.

Chcete-li zobrazit top 10 uživatele seřazených podle čísla navštívené webové stránky od hodinou, spusťte tento dotaz:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
````

Můžete snadno upravit tento dotaz k analýze navštívených stránek od před jedním dnem, měsícem nebo libovolném okamžiku v minulosti chcete.

Pokud chcete provést základní statistické analýzy za předchozí den, použijte následující příklad:

````
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
````

K vyhledání aktivit konkrétního uživatele, v určitou dobu, použijte klauzuli obsažené:

````
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo 
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
````

Grafické vizualizace je zvlášť vhodné pro dočasné dotazy, jak můžete zobrazit trendy a vzorce používání v intuitivní způsob velmi snadno:

![TemporalGraph](./media/sql-database-temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Vyvíjející se schéma tabulky
Obvykle bude muset změnit schéma dočasnou tabulku, při práci vývoj aplikací. Pro tento jednoduše spusťte regulární příkaz ALTER TABLE příkazů a Azure SQL Database správně rozšíří změny do tabulky historie. Tento skript je ukázkou, jak můžete přidat další atribut pro sledování:

````
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo 
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
````

Podobně můžete změnit definici sloupce při aktivní úlohy:

````
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo 
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
````

Nakonec můžete odebrat sloupec, který už nepotřebujete.

````
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo 
    DROP COLUMN TemporaryColumn; 
````

Můžete taky použít nejnovější [SSDT](https://msdn.microsoft.com/library/mt204009.aspx) změnit dočasná tabulka schématu během připojení k databázi (online režim) nebo jako součást k databázovému projektu (offline režim).

## <a name="controlling-retention-of-historical-data"></a>Řízení uchovávání historických dat
Dočasných tabulek se systémovou správou verzí může zvýšit v tabulce historie regulérních tabulkách velikost databáze více než. Tabulky historie velké a stále se rozšiřujícího se může stát problém i z důvodu náklady na úložiště čistá, jakož i nastavení výkonu daňové na dočasné dotazování. Proto vývoj data zásady uchovávání informací pro správu dat v tabulce historie je důležitým aspektem plánování a správu životního cyklu každých dočasnou tabulku. S Azure SQL Database máte následující přístupy pro správu historická data v dočasné tabulce:

* [Vytváření oddílů tabulky](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_2)
* [Skript pro vyčištění vlastní](https://msdn.microsoft.com/library/mt637341.aspx#Anchor_3)

## <a name="next-steps"></a>Další postup
Podrobné informace o dočasné tabulky, podívejte se na [dokumentace MSDN](https://msdn.microsoft.com/library/dn935015.aspx).
Navštivte Channel 9 a poslechnout [scénáře úspěchu dočasné implementace skutečné zákazníka](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) a sledujte [live dočasné ukázkový](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

