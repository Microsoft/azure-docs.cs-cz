---
title: Export do SQL ze služby Azure Application Insights | Dokumentace Microsoftu
description: Průběžně exportujte data Application Insights do SQL s použitím Stream Analytics.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 48903032-2c99-4987-9948-d6e4559b4a63
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: mbullwin
ms.openlocfilehash: f9b1096f2d409f46e3b9e48a2d59420930150c1a
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974552"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Průvodce: Export do SQL z Application Insights s použitím Stream Analytics
Tento článek popisuje, jak přesunout vaše telemetrická data z [Azure Application Insights] [ start] do služby Azure SQL database s použitím [průběžný Export] [ export] a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Průběžný export posune vaše telemetrická data do služby Azure Storage ve formátu JSON. Vytvoříme parsovat JSON objektů pomocí Azure Stream Analytics a vytvářet řádky v tabulce databáze.

(Více obecně průběžný Export je způsob, jak provádět analýzu telemetrie aplikace posílat do Application Insights. Vám může přizpůsobit tento vzorový kód k provádění jiných operací s exportované telemetrická data, jako jsou agregace dat.)

Začneme za předpokladu, že již máte aplikaci, kterou chcete monitorovat.

V tomto příkladu budeme používat data o zobrazení stránek, ale stejný vzor lze snadno rozšířit na jiné datové typy, jako jsou vlastní události a výjimky. 

## <a name="add-application-insights-to-your-application"></a>Přidejte Application Insights do vaší aplikace
Jak začít:

1. [Nastavení Application Insights pro webové stránky](../../azure-monitor/app/javascript.md). 
   
    (V tomto příkladu se zaměříme na zpracování dat zobrazení stránky v prohlížečích klienta, ale můžete také nastavit Application Insights pro na straně serveru vaší [Java](../../azure-monitor/app/java-get-started.md) nebo [ASP.NET](../../azure-monitor/app/asp-net.md) aplikace a proces žádosti závislosti a jiné telemetrie serveru.)
2. Publikování aplikace a podívejte se na telemetrická data v prostředku Application Insights.

## <a name="create-storage-in-azure"></a>Vytvoření úložiště v Azure
Průběžný export vždy výstupů dat do účtu služby Azure Storage, musíte nejprve vytvořit úložiště.

1. Vytvoření účtu úložiště ve vašem předplatném v [webu Azure portal][portal].
   
    ![Na webu Azure portal zvolte nový, dat, úložiště. Vyberte Classic, zvolte možnost vytvořit. Zadejte název úložiště.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte kontejnery, klikněte na dlaždici kontejnery a pak přidat](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Zkopírovat přístupový klíč úložiště
   
    Musíte ji nejdříve k nastavení vstupu do služby stream analytics.
   
    ![V úložišti otevřete nastavení, klíče a zkopírujte primární přístupový klíč](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spustit průběžný export do služby Azure storage
1. Na webu Azure Portal přejděte do prostředku Application Insights, který jste vytvořili pro vaši aplikaci.
   
    ![Klikněte na tlačítko Procházet, Application Insights, vaše aplikace](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Vytvoření průběžný export.
   
    ![Zvolte možnost přidat nastavení průběžný Export](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Vyberte účet úložiště, který jste vytvořili dříve:

    ![Nastavte cíl exportu](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Vyberte typy událostí](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Umožnit shromažďování některá dat. Opřete se a umožnit lidem nějakou dobu používat vaši aplikaci. Telemetrická data budou přicházet a zobrazí statistické grafy v [Průzkumník metrik](../../application-insights/app-insights-metrics-explorer.md) a jednotlivé události v [diagnostické vyhledávání](../../azure-monitor/app/diagnostic-search.md). 
   
    A také se exportovat data do úložiště. 
2. Kontrola exportovaná data, buď na portálu – volba **Procházet**, vyberte svůj účet úložiště a pak **kontejnery** - nebo v sadě Visual Studio. V sadě Visual Studio, zvolte **zobrazení / Cloud Explorer**a otevřete Azure / Storage. (Pokud nemáte k dispozici tato možnost nabídky, je potřeba nainstalovat sadu Azure SDK: Otevřete dialogové okno Nový projekt a otevřete vizuál C# / cloudu / získat Microsoft Azure SDK pro .NET.)
   
    ![V sadě Visual Studio otevřete prohlížeč serveru, Azure, úložiště](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Poznamenejte si běžné část názvu cesty, které je odvozeno z názvu a Instrumentační klíče aplikace. 

Události se zapisují do objektu blob soubory ve formátu JSON. Každý soubor může obsahovat jeden nebo více událostí. Tak rádi bychom číst data události a filtrování pole, která chceme. Existují všechny druhy věcí, které můžeme udělat s daty, ale náš plán ještě dnes je použití Stream Analytics pro přesun dat do služby SQL database. To se usnadní ke spuštění hodně zajímavé dotazy.

## <a name="create-an-azure-sql-database"></a>Vytvoření Azure SQL Database
Znovu spouští ze svého předplatného v [webu Azure portal][portal], vytvořit databázi (a nový server, pokud již máte jeden) která bude zapisovat data.

![Nová Data, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Ujistěte se, že databázový server umožňuje přístup ke službám Azure:

![Procházet, servery, serveru, nastavení brány Firewall, povolit přístup k Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Vytvoření tabulky ve službě Azure SQL DB
Připojení k databázi vytvořené v předchozí části s nástrojem pro vaše preferované management. V tomto návodu použijeme [SQL Server Management Tools](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Vytvořit nový dotaz a spusťte následující příkaz T-SQL:

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

![](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

V této ukázce používáme data z zobrazení stránek. Pokud chcete zobrazit další data k dispozici, kontrola výstupu JSON a naleznete v tématu [export datového modelu](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance Azure Stream Analytics
Z [webu Azure portal](https://portal.azure.com/), vyberte službu Azure Stream Analytics a vytvoření nové úlohy Stream Analytics:

![Nastavení Stream analytics](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Když se vytvoří novou úlohu, vyberte **přejít k prostředku**.

![Nastavení Stream analytics](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Přidat nový vstup

![Nastavení Stream analytics](./media/code-sample-export-sql-stream-analytics/SA004.png)

K provedení vstup z objektu blob služby průběžného exportu nastavení:

![Nastavení Stream analytics](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Teď budete potřebovat primární přístupový klíč z vašeho účtu úložiště, který jste si předtím poznamenali. Nastavíte jako klíč účtu úložiště.

#### <a name="set-path-prefix-pattern"></a>Vzor předpony cesty sady

**Ujistěte se, že nastavení formát data rrrr-MM-DD (s pomlčkami).**

Vzor předpony cesty určuje, jak Stream Analytics najde vstupních souborů v úložišti. Je nutné nastavit ji tak, aby odpovídaly průběžný Export způsobu, jakým ukládá data. Nastavte ho následujícím způsobem:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

V tomto příkladu:

* `webapplication27` název prostředku Application Insights je **vše na malá písmena**. 
* `1234...` je Instrumentační klíč tohoto prostředku Application Insights **s pomlčkami odebrat**. 
* `PageViews` je typ dat, které chceme analyzovat. Dostupné typy závisí na filtru, který jste nastavili v průběžný Export. Prozkoumejte exportovaná data zobrazíte dostupné typy a podívejte se [export datového modelu](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` je vzorek doslova zapsán.

Pokud chcete získat název a iKey prostředku Application Insights, otevřete Essentials na stránce Přehled nebo otevřete nastavení.

> [!TIP]
> Použijte ukázkové funkce pro kontrolu vstupní cesta jste správně nastavili. Pokud selže: Zkontrolujte, jestli data v úložišti pro ukázkové časový rozsah, který jste zvolili. Upravte definici vstupní a zkontrolujte nastavení účtu úložiště, předpona cesty a datum formátu správně.
> 
> 
## <a name="set-query"></a>Sady dotazů
Otevřete část dotazu:

Nahraďte výchozí dotaz pomocí:

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

Všimněte si, že několik prvních vlastností jsou specifické pro data o zobrazeních stránek. Exporty ostatních typů telemetrie, budou mít různé vlastnosti. Zobrazit [podrobné referenční informace k modelu dat pro typy vlastností a hodnot.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Nastavte si výstup do databáze
Vyberte SQL jako výstup.

![Ve službě stream analytics vyberte výstupy](./media/code-sample-export-sql-stream-analytics/SA006.png)

Zadejte databázi SQL.

![Zadejte podrobnosti databáze](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zavřete průvodce a počkejte, oznámení, že je nastavený výstup.

## <a name="start-processing"></a>Spuštění zpracování
Spuštění úlohy na panelu akcí:

![Ve službě stream analytics klikněte na tlačítko Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

Můžete zvolit, jestli se má začít zpracovávat data od nyní nebo začínat starší data. Druhá možnost je užitečná, pokud jste už nějakou dobu spuštění průběžný Export.

Za pár minut vraťte se do nástroje pro správu SQL serveru a podívejte se na data v. Například použijte dotaz následujícím způsobem:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Související články
* [Export do Power BI pomocí Stream Analytics](../../application-insights/app-insights-export-power-bi.md)
* [Podrobný datový model referenční informace pro typy vlastností a hodnot.](../../azure-monitor/app/export-data-model.md)
* [Průběžný Export v Application Insights](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../application-insights/app-insights-metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../application-insights/app-insights-overview.md

