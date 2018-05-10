---
title: Exportovat do SQL z Azure Application Insights | Microsoft Docs
description: Nepřetržitě exportujte data Application Insights do SQL pomocí služby Stream Analytics.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/06/2015
ms.author: mbullwin
ms.openlocfilehash: e410bb87d017a7659c3eaffbedef378aaf7f5716
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Návod: Export do SQL z Application Insights pomocí služby Stream Analytics
Tento článek ukazuje, jak přesunout data telemetrie z [Azure Application Insights] [ start] do Azure SQL database pomocí [průběžné exportovat] [ export] a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Průběžné export přesune telemetrická data do úložiště Azure ve formátu JSON. Jsme budete objekty JSON pomocí Azure Stream Analytics analyzovat a vytvořte řádky v tabulce databáze.

(Obecně platí, průběžné Export je způsob, jak provést vlastní analýzu telemetrie aplikace odesílání Application Insights. Vám může přizpůsobit této ukázce kódu provádět další akce s exportovaný telemetrických dat, jako je například agregace dat.)

Začneme s se předpokládá, že už máte aplikaci, kterou chcete monitorovat.

V tomto příkladu použijeme data zobrazit na stránce, ale stejného vzoru lze snadno rozšířit na jiné datové typy, jako jsou vlastní události a výjimky. 

## <a name="add-application-insights-to-your-application"></a>Přidejte Application Insights do vaší aplikace
Jak začít:

1. [Nastavte Application Insights pro webové stránky](app-insights-javascript.md). 
   
    (V tomto příkladu budete zaměříme na zpracování stránky zobrazení dat v prohlížečích klienta, ale můžete také nastavit Application Insights pro na straně serveru vaší [Java](app-insights-java-get-started.md) nebo [ASP.NET](app-insights-asp-net.md) aplikace a proces žádosti závislosti a další telemetrií serveru.)
2. Publikování aplikace a sledujte telemetrii dat zobrazovaných v prostředku Application Insights.

## <a name="create-storage-in-azure"></a>Vytvořte úložiště v Azure
Průběžné export vždy poskytuje data pro účet úložiště Azure, musíte nejprve vytvořit úložiště.

1. Vytvořit účet úložiště v rámci vašeho předplatného v [portál Azure][portal].
   
    ![Na portálu Azure zvolte nový, Data, úložiště. Vyberte Classic, vyberte vytvořit. Zadejte název úložiště.](./media/app-insights-code-sample-export-sql-stream-analytics/040-store.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte kontejnery, klikněte na dlaždici kontejnery a potom přidat](./media/app-insights-code-sample-export-sql-stream-analytics/050-container.png)
3. Kopii přístupového klíče úložiště.
   
    Musíte ho brzy k nastavení vstup do služby stream analytics.
   
    ![V úložišti otevře se nastavení klíče a proveďte kopii primární přístupový klíč](./media/app-insights-code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spustit průběžné export do úložiště Azure
1. Na portálu Azure přejděte do prostředku Application Insights, kterou jste vytvořili pro vaši aplikaci.
   
    ![Vyberte možnost Procházet, Application Insights, vaše aplikace](./media/app-insights-code-sample-export-sql-stream-analytics/060-browse.png)
2. Vytvořte průběžné export.
   
    ![Vyberte nastavení, průběžné exportu, přidejte](./media/app-insights-code-sample-export-sql-stream-analytics/070-export.png)

    Vyberte účet úložiště, které jste vytvořili dříve:

    ![Nastavit cíl exportu](./media/app-insights-code-sample-export-sql-stream-analytics/080-add.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Vyberte typy událostí](./media/app-insights-code-sample-export-sql-stream-analytics/085-types.png)


1. Některá data hromadí let. Sledujte a umožnit lidem nějakou dobu používat vaši aplikaci. Telemetrická data se odešlou a zobrazí statistické grafy v [metriky explorer](app-insights-metrics-explorer.md) a jednotlivé události v [diagnostické vyhledávání](app-insights-diagnostic-search.md). 
   
    A navíc bude exportovat data do úložiště. 
2. Zkontrolujte exportovaná data, buď v portálu – volba **Procházet**, vyberte svůj účet úložiště a potom **kontejnery** - nebo v sadě Visual Studio. V sadě Visual Studio, vyberte **zobrazení / cloudu Explorer**a otevřete Azure nebo úložiště. (Pokud nemáte tento příkaz nabídky, budete muset nainstalovat sadu Azure SDK: Otevřete dialogové okno Nový projekt a otevřete Visual C# / Cloud / získat Microsoft Azure SDK pro .NET.)
   
    ![V sadě Visual Studio otevřete prohlížeč Server, Azure, úložiště](./media/app-insights-code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Poznamenejte si část běžný název cesty, které je odvozeno z klíče název a instrumentace aplikací. 

Události se zapisují do objektu blob soubory ve formátu JSON. Každý soubor může obsahovat jeden nebo více událostí. Proto jsme chtěli číst data události a filtrovat pole, která má být. Jsou k dispozici všechny typy věcí, které můžeme udělat s daty, ale pokud chcete přesunout data do databáze SQL pomocí služby Stream Analytics dnes je naše plán. To bude usnadní spouštět velké množství zajímavé dotazy.

## <a name="create-an-azure-sql-database"></a>Vytvoření databáze Azure SQL
Znovu se spouští ze svého předplatného v [portál Azure][portal], vytvořit databázi (a nový server, pokud již máte jeden) kterého budete zapsat data.

![Nový dat, SQL](./media/app-insights-code-sample-export-sql-stream-analytics/090-sql.png)

Ujistěte se, že databázový server umožňuje přístup ke službám Azure:

![Procházet, servery, server, nastavení brány Firewall, povolit přístup k Azure](./media/app-insights-code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Vytvoření tabulky v databázi SQL Azure
Připojte k databázi vytvořené v předchozí části s vaší nástroj pro správu upřednostňované. V tomto návodu použijeme [nástroje správy systému SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/app-insights-code-sample-export-sql-stream-analytics/31-sql-table.png)

Vytvořit nový dotaz a spusťte následující T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![](./media/app-insights-code-sample-export-sql-stream-analytics/34-create-table.png)

V této ukázce používáme data ze zobrazení stránky. Pokud chcete zobrazit dostupné data, zkontrolujte výstupu JSON a najdete v článku [Exportovat datový model](app-insights-export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance služby Azure Stream Analytics
Z [portál Azure](https://portal.azure.com/), vyberte službu Azure Stream Analytics a vytvořit novou úlohu služby Stream Analytics:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA001.png)

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA002.png)

Při vytváření nové úlohy, vyberte **přejděte k prostředku**.

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Přidat nové vstup

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA004.png)

Nastavte ji tak, aby vstupní z objektu blob služby průběžné Export:

![](./media/app-insights-code-sample-export-sql-stream-analytics/SA005.png)

Nyní budete potřebovat primární přístupový klíč z vašeho účtu úložiště, který jste si předtím poznamenali. Nastavením této hodnoty jako klíč účtu úložiště.

#### <a name="set-path-prefix-pattern"></a>Sada cesta předpona vzoru

**Nezapomeňte nastavit formát data do rrrr-MM-DD (s pomlčkami).**

Cesta předpony vzor Určuje, jak Stream Analytics vyhledá vstupní soubory v úložišti. Budete muset nastavit tak, aby odpovídaly jak průběžné exportovat data uloží. Nastavte takto:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

V tomto příkladu:

* `webapplication27` je název prostředku Application Insights **vše na malá písmena**. 
* `1234...` je klíč instrumentace prostředku Application Insights **s pomlčkami odebrat**. 
* `PageViews` je typ dat, který chcete analyzovat. Dostupné typy závisí na filtr, který nastavíte v průběžné exportovat. Zkontrolujte exportovaná data zobrazit dostupné typy a zobrazit [Exportovat datový model](app-insights-export-data-model.md).
* `/{date}/{time}` vzor zapsána oznámena.

Chcete-li získat název a iKey prostředku Application Insights, otevřete Essentials na stránku s jeho přehled nebo otevřete nastavení.

> [!TIP]
> Zkontrolujte, že jste správně nastavili vstupní cesta použijte funkci Sample. Pokud se nezdaří: Zkontrolujte, zda je data v úložišti pro ukázkové časové rozmezí jste zvolili. Upravte definici vstupní a zkontrolujte nastavení účtu úložiště, cesta předponu a datum formátu správně.
> 
> 
## <a name="set-query"></a>Sada dotazu
Otevřete část dotazu:

Nahraďte výchozí dotaz s:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Všimněte si, že první několik vlastností jsou specifické pro data zobrazení stránky. Export jiné typy telemetrických dat bude mít jiné vlastnosti. Najdete v článku [podrobné referenční model dat pro typy vlastností a hodnoty.](app-insights-export-data-model.md)

## <a name="set-up-output-to-database"></a>Nastavit výstup do databáze
Vyberte SQL jako výstup.

![V služby stream analytics vyberte výstupy](./media/app-insights-code-sample-export-sql-stream-analytics/SA006.png)

Zadejte databáze SQL.

![Zadejte podrobnosti databáze](./media/app-insights-code-sample-export-sql-stream-analytics/SA007.png)

Zavřete průvodce a čekat na oznámení, že byla nastavena výstup.

## <a name="start-processing"></a>Spuštění zpracování
Spustíte úlohu z panelu akcí:

![V služby stream analytics klikněte na tlačítko Start](./media/app-insights-code-sample-export-sql-stream-analytics/SA008.png)

Můžete zvolit, jestli se má spustit zpracování dat od teď nebo začínat starší data. Je užitečné, pokud jste předtím průběžné exportovat už běží nějakou dobu.

Po několika minutách vraťte se zpátky a nástroje správy systému SQL Server a sledovat data předávaná v. Například použijte dotaz takto:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Související články
* [Exportovat do PowerBI pomocí služby Stream Analytics](app-insights-export-power-bi.md)
* [Podrobný datový model referenční informace pro vlastnost typů a hodnot.](app-insights-export-data-model.md)
* [Průběžné Export ve službě Application Insights](app-insights-export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[export]: app-insights-export-telemetry.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[start]: app-insights-overview.md

