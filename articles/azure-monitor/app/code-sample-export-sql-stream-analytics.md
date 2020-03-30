---
title: Export do SQL z Přehledů aplikací Azure | Dokumenty společnosti Microsoft
description: Průběžně exportujte data Application Insights do SQL pomocí Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 3ef0420cdab64f11b699fd4031ed2b0134f18609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663671"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Návod: Export do SQL z přehledů aplikací pomocí Stream Analytics
Tento článek ukazuje, jak přesunout telemetrická data z [Azure Application Insights][start] do databáze Azure SQL pomocí [průběžného exportu][export] a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Nepřetržitý export přesune vaše telemetrická data do Služby Azure Storage ve formátu JSON. Objekty JSON analyzujeme pomocí Azure Stream Analytics a vytvoříme řádky v databázové tabulce.

(Obecněji řečeno, nepřetržitý export je způsob, jak provést vlastní analýzu telemetrie, kterou vaše aplikace odesílají do Application Insights. Tuto ukázku kódu můžete přizpůsobit k jiným věcem s exportovnou telemetrií, jako je například agregace dat.)

Začneme s předpokladem, že již máte aplikaci, kterou chcete sledovat.

V tomto příkladu budeme používat data zobrazení stránky, ale stejný vzor lze snadno rozšířit na jiné datové typy, jako jsou vlastní události a výjimky. 

## <a name="add-application-insights-to-your-application"></a>Přidání přehledů aplikací do aplikace
Jak začít:

1. [Nastavte přehledy aplikací pro vaše webové stránky](../../azure-monitor/app/javascript.md). 
   
    (V tomto příkladu se zaměříme na zpracování dat zobrazení stránky z klientských prohlížečů, ale můžete také nastavit Application Insights pro serverovou stranu vašeho [java](../../azure-monitor/app/java-get-started.md) nebo [ASP.NET](../../azure-monitor/app/asp-net.md) žádosti o aplikaci a proces, závislost a další telemetrii serveru.)
2. Publikujte aplikaci a sledujte telemetrická data, která se zobrazují ve vašem prostředku Application Insights.

## <a name="create-storage-in-azure"></a>Vytvoření úložiště v Azure
Nepřetržitý export vždy vydezkuje data do účtu Azure Storage, takže nejdřív musíte vytvořit úložiště.

1. Vytvořte si účet úložiště ve svém předplatném na [webu Azure Portal][portal].
   
    ![Na Webu Azure Portal zvolte Nový, Data, Úložiště. Vyberte Klasické, zvolte Vytvořit. Zadejte název úložiště.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte Kontejnery, klikněte na dlaždici Kontejnery a pak přidejte](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Kopírování přístupového klíče úložiště
   
    Budete ji brzy potřebovat k nastavení vstupu do služby analýzy datového proudu.
   
    ![V úložišti otevřete Nastavení, klíče a pořažte kopii primárního přístupového klíče](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spuštění průběžného exportu do úložiště Azure
1. Na webu Azure Portal přejděte k prostředku Application Insights, který jste vytvořili pro svou aplikaci.
   
    ![Zvolte Procházet, Application Insights, vaše aplikace](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Vytvořte nepřetržitý export.
   
    ![Zvolte Nastavení, Nepřetržitý export, Přidat](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Vyberte účet úložiště, který jste vytvořili dříve:

    ![Nastavení cíle exportu](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Výběr typů událostí](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Nechte některá data hromadit. Sednout si a nechat lidi používat vaši žádost na chvíli. Telemetrie přijde a uvidíte statistické grafy v [průzkumníku metrik](../../azure-monitor/app/metrics-explorer.md) a jednotlivé události v [diagnostickévyhledávání](../../azure-monitor/app/diagnostic-search.md). 
   
    A také se data exportují do vašeho úložiště. 
2. Zkontrolujte exportovaná data, buď na portálu – zvolte **Procházet**, vyberte účet úložiště a potom **kontejnery** – nebo v sadě Visual Studio. Ve Visual Studiu zvolte **Zobrazit / Průzkumníka Cloudu**a otevřete Azure / Storage. (Pokud nemáte tuto možnost nabídky, musíte nainstalovat sadu Azure SDK: Otevřete dialogové okno Nový projekt a otevřete Visual C# / Cloud / Get Microsoft Azure SDK pro .NET.)
   
    ![V Sadě Visual Studio otevřete prohlížeč serveru, Azure, úložiště](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Poznamenejte si společnou část názvu cesty, která je odvozena od názvu aplikace a klíče instrumentace. 

Události jsou zapsány do souborů objektů blob ve formátu JSON. Každý soubor může obsahovat jednu nebo více událostí. Takže bychom si rádi přečetli data o událostech a odfiltrovali pole, která chceme. Existují všechny druhy věcí, které bychom mohli dělat s daty, ale náš plán dnes je použít Stream Analytics přesunout data do databáze SQL. To umožní snadné spuštění spousty zajímavých dotazů.

## <a name="create-an-azure-sql-database"></a>Vytvoření databáze Azure SQL Database
Ještě jednou počínaje vaše předplatné na [webu Azure Portal][portal], vytvořte databázi (a nový server, pokud jste již máte jeden), na který budete psát data.

![Nové, Data, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Ujistěte se, že databázový server umožňuje přístup ke službám Azure:

![Procházet, Servery, servery, Nastavení, Brána firewall, Povolit přístup k Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-db"></a>Vytvoření tabulky v Azure SQL DB
Připojte se k databázi vytvořené v předchozí části pomocí upřednostňovaného nástroje pro správu. V tomto návodu budeme používat [nástroje pro správu serveru SQL Server](https://msdn.microsoft.com/ms174173.aspx) (SSMS).

![](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Vytvořte nový dotaz a proveďte následující T-SQL:

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

V této ukázce používáme data ze zobrazení stránky. Chcete-li zobrazit další dostupná data, zkontrolujte výstup JSON a podívejte se na [model exportu dat](../../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance Azure Stream Analytics
Na [webu Azure Portal](https://portal.azure.com/)vyberte službu Azure Stream Analytics a vytvořte novou úlohu Stream Analytics:

![Nastavení analýzy datového proudu](./media/code-sample-export-sql-stream-analytics/SA001.png)

![](./media/code-sample-export-sql-stream-analytics/SA002.png)

Po vytvoření nové úlohy vyberte **přejít na zdroj**.

![Nastavení analýzy datového proudu](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Přidání nového vstupu

![Nastavení analýzy datového proudu](./media/code-sample-export-sql-stream-analytics/SA004.png)

Nastavte ji tak, aby přebíhal vstup z objektu blob průběžného exportu:

![Nastavení analýzy datového proudu](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Teď budete potřebovat primární přístupový klíč ze svého účtu úložiště, který jste si poznamenali dříve. Nastavte to jako klíč účtu úložiště.

#### <a name="set-path-prefix-pattern"></a>Nastavit vzorek předpony cesty

**Nezapomeňte nastavit formát data na YYYY-MM-DD (s pomlčkami).**

Vzor předpony cesty určuje, jak Stream Analytics najde vstupní soubory v úložišti. Je třeba nastavit tak, aby odpovídala způsobu nepřetržitého exportu ukládá data. Nastavte to takto:

    webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}

V tomto příkladu:

* `webapplication27`je název prostředku Application Insights, **vše v malé písmeno**. 
* `1234...`je klíč instrumentace prostředku Application Insights **s odstraněnými pomlčkami**. 
* `PageViews`je typ dat, které chceme analyzovat. Dostupné typy závisí na filtru nastaveném v režimu Nepřetržitý export. Zkontrolujte exportovaná data, abyste viděli další dostupné typy a podívejte se na [model exportu dat](../../azure-monitor/app/export-data-model.md).
* `/{date}/{time}`je vzor psaný doslova.

Chcete-li získat název a iKlíč prostředku Application Insights, otevřete Essentials na jeho stránce s přehledem nebo otevřete Nastavení.

> [!TIP]
> Pomocí ukázkové funkce zkontrolujte, zda jste vstupní cestu nastavili správně. Pokud se nezdaří: Zkontrolujte, zda jsou v úložišti data pro ukázkový časový rozsah, který jste zvolili. Upravte definici vstupu a zkontrolujte, zda správně nastavíte účet úložiště, předponu cesty a formát data.

 
## <a name="set-query"></a>Nastavit dotaz
Otevřete část dotazu:

Nahraďte výchozí dotaz:

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

Všimněte si, že prvních několik vlastností jsou specifické pro data zobrazení stránky. Exporty jiných typů telemetrie budou mít různé vlastnosti. Podívejte se na [podrobný odkaz na datový model pro typy vlastností a hodnoty.](../../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Nastavit výstup do databáze
Jako výstup vyberte SQL.

![V analýze datového proudu vyberte Výstupy](./media/code-sample-export-sql-stream-analytics/SA006.png)

Zadejte databázi SQL.

![Vyplňte podrobnosti o databázi](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zavřete průvodce a počkejte na oznámení, že výstup byl nastaven.

## <a name="start-processing"></a>Zahájit zpracování
Spusťte úlohu z panelu akcí:

![V analýze datového proudu klikněte na Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

Můžete zvolit, zda chcete začít zpracovávat data od této chvíle, nebo začít s dřívějšími daty. Ten je užitečný, pokud jste měli nepřetržitý export již běží na chvíli.

Po několika minutách se vraťte k nástrojům pro správu serveru SQL Server a sledujte, jak data proudí. Použijte například dotaz, jako je tento:

    SELECT TOP 100 *
    FROM [dbo].[PageViewsTable]


## <a name="related-articles"></a>Související články
* [Export do PowerBI pomocí Stream Analytics](../../azure-monitor/app/export-power-bi.md )
* [Podrobný odkaz datového modelu pro typy vlastností a hodnoty.](../../azure-monitor/app/export-data-model.md)
* [Nepřetržitý export v přehledech aplikací](../../azure-monitor/app/export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[export]: ../../azure-monitor/app/export-telemetry.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[portal]: https://portal.azure.com/
[start]: ../../azure-monitor/app/app-insights-overview.md

