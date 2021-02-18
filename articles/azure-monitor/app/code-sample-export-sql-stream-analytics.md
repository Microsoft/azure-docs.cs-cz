---
title: Export do SQL z Azure Application Insights | Microsoft Docs
description: Průběžně exportujte Application Insights data do SQL pomocí Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 25d28bb0fc35ef76231c085dc1d9d8a1234a264c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587749"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Návod: Export do jazyka SQL z Application Insights pomocí Stream Analytics
Tento článek popisuje, jak přesunout data telemetrie z [Azure Application Insights][start] do Azure SQL Database pomocí [průběžného exportu][export] a [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Průběžný export přesune data telemetrie do Azure Storage ve formátu JSON. Objekty JSON analyzujeme pomocí Azure Stream Analytics a vytvoří řádky v tabulce databáze.

(Obecně platí, že průběžný export je způsob, jak provádět vlastní analýzu telemetrie, které vaše aplikace odesílá Application Insights. Tuto ukázku kódu můžete přizpůsobit, abyste mohli provádět jiné akce s exportovanou telemetrie, jako je například agregace dat.)

Začneme s předpokladem, že už máte aplikaci, kterou chcete monitorovat.

V tomto příkladu budeme používat data zobrazení stránky, ale stejný vzor se dá snadno rozšířit na jiné datové typy, jako jsou třeba vlastní události a výjimky. 

## <a name="add-application-insights-to-your-application"></a>Přidání Application Insights do aplikace
Jak začít:

1. [Nastavte Application Insights pro své webové stránky](./javascript.md). 
   
    (V tomto příkladu se zaměříme na zpracování dat zobrazení stránky z prohlížečů klienta, ale můžete také nastavit Application Insights na straně serveru aplikace [Java](./java-get-started.md) nebo [ASP.NET](./asp-net.md) a zpracovávat požadavky, závislosti a další telemetrie serveru.)
2. Publikujte aplikaci a sledujte data telemetrie v prostředku Application Insights.

## <a name="create-storage-in-azure"></a>Vytvoření úložiště v Azure
Průběžný export vždycky ukládá data do účtu Azure Storage, takže nejdřív musíte úložiště vytvořit.

1. Vytvořte ve svém předplatném účet úložiště ve [Azure Portal][portal].
   
    ![V Azure Portal vyberte nový, data a úložiště. Vyberte klasický a zvolte vytvořit. Zadejte název úložiště.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Vytvoření kontejneru
   
    ![V novém úložišti vyberte kontejnery, klikněte na dlaždici kontejnery a pak přidat](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. Zkopírování přístupového klíče úložiště
   
    Budete ji potřebovat brzy pro nastavení vstupu ke službě Stream Analytics.
   
    ![V úložišti otevřete nastavení, klíče a proveďte kopii primárního přístupového klíče.](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Spustit průběžný export do služby Azure Storage
1. V Azure Portal vyhledejte prostředek Application Insights, který jste vytvořili pro vaši aplikaci.
   
    ![Vyberte možnost Procházet, Application Insights a aplikace.](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Vytvořte průběžný export.
   
    ![Zvolit nastavení, průběžný export, přidat](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Vyberte účet úložiště, který jste vytvořili dříve:

    ![Nastavit cíl exportu](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Nastavte typy událostí, které chcete zobrazit:

    ![Výběr typů událostí](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Nechte si shromažďovat data. Nasaďte se zpátky a umožněte lidem, aby na chvíli používali vaši aplikaci. Telemetrie se objeví v a v [diagnostickém vyhledávání](./diagnostic-search.md)uvidíte statistické grafy v [Průzkumníkovi metrik](../essentials/metrics-charts.md) a jednotlivé události. 
   
    Data budou také exportována do vašeho úložiště. 
2. Zkontrolujte exportovaná data, a to buď na portálu – zvolte **Procházet**, vyberte svůj účet úložiště a pak **kontejnery** – nebo v aplikaci Visual Studio. V aplikaci Visual Studio vyberte možnost **Zobrazit/Cloud Průzkumník** a otevřete Azure/úložiště. (Pokud nemáte tuto možnost nabídky, musíte nainstalovat sadu Azure SDK: otevřete dialogové okno Nový projekt a otevřete Visual C#/Cloud/získat Microsoft Azure SDK pro .NET.)
   
    ![V aplikaci Visual Studio otevřete Prohlížeč serveru, Azure, úložiště](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Poznamenejte si běžnou část názvu cesty, která je odvozena z názvu aplikace a klíče instrumentace. 

Události se zapisují do souborů BLOB ve formátu JSON. Každý soubor může obsahovat jednu nebo více událostí. Proto bychom chtěli číst data událostí a vyfiltrovat požadovaná pole. Existují nejrůznější věci, které jsme s daty mohli dělat, ale náš plán dnes je použít Stream Analytics k přesunu dat do SQL Database. Díky tomu bude snadné spouštět spoustu zajímavých dotazů.

## <a name="create-an-azure-sql-database"></a>Vytvoření databáze Azure SQL Database
Až se znovu rozhodnete z předplatného v [Azure Portal][portal], vytvořte databázi (a nový server, pokud jste ho ještě nezískali), na který budete data zapisovat.

![Nová data, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Ujistěte se, že server umožňuje přístup ke službám Azure:

![Procházení, servery, server, nastavení, brána firewall, povolení přístupu k Azure](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Vytvořit tabulku v Azure SQL Database
Připojte se k databázi vytvořené v předchozí části pomocí preferovaného nástroje pro správu. V tomto návodu použijeme [SQL Server Management Tools](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

![Připojení k Azure SQL Database](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Vytvořte nový dotaz a spusťte následující příkaz T-SQL:

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

![Vytvořit PageViewsTable](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

V této ukázce používáme data z zobrazení stránky. Pokud chcete zobrazit další dostupná data, zkontrolujte výstup JSON a podívejte se na [model exportu dat](./export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Vytvoření instance Azure Stream Analytics
Z [Azure Portal](https://portal.azure.com/)vyberte službu Azure Stream Analytics a vytvořte novou Stream Analytics úlohu:

![Snímek obrazovky zobrazující stránku úlohy Stream Analytics s zvýrazněným tlačítkem vytvořit.](./media/code-sample-export-sql-stream-analytics/SA001.png)

![Nová úloha Stream Analytics](./media/code-sample-export-sql-stream-analytics/SA002.png)

Po vytvoření nové úlohy vyberte **Přejít k prostředku**.

![Snímek obrazovky se zobrazí zpráva o úspěšném nasazení a tlačítko Přejít na prostředek.](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Přidat nový vstup

![Snímek obrazovky s vybraným tlačítkem přidat zobrazí stránku vstupy.](./media/code-sample-export-sql-stream-analytics/SA004.png)

Nastavte ho tak, aby převzal výstup z objektu BLOB průběžného exportu:

![Snímek obrazovky se zobrazí nové vstupní okno se zvolenými možnostmi rozevírací nabídky vstupní alias, zdroj a účet úložiště.](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Teď budete potřebovat primární přístupový klíč z účtu úložiště, který jste si poznamenali dříve. Nastavte tuto hodnotu jako klíč účtu úložiště.

#### <a name="set-path-prefix-pattern"></a>Nastavit vzor předpony cesty

**Nezapomeňte nastavit formát data na RRRR-MM-DD (s pomlčkami).**

Vzor předpony cesty určuje, jak Stream Analytics najde vstupní soubory v úložišti. Musíte ho nastavit tak, aby odpovídal způsobu průběžného exportu uložených dat. Nastavte to takto:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

V tomto příkladu:

* `webapplication27` je název prostředku Application Insights, a to **vše v malých malých písmenech**. 
* `1234...` je klíč instrumentace prostředku Application Insights **s odebranými pomlčkami**. 
* `PageViews` je typ dat, která chceme analyzovat. Dostupné typy závisí na filtru, který jste nastavili při průběžném exportu. Prohlédněte si exportovaná data a podívejte se na další dostupné typy a podívejte se na [model exportu dat](./export-data-model.md).
* `/{date}/{time}` je vzor psaný doslova.

Pokud chcete získat název a iKey prostředku Application Insights, otevřete na své stránce Přehled základní informace nebo otevřete nastavení.

> [!TIP]
> Pomocí ukázkové funkce ověřte, zda jste správně nastavili vstupní cestu. Pokud se chyba nezdařila: Ověřte, zda je v úložišti k dispozici data pro vybraný rozsah ukázkových časů. Upravte definici vstupu a ověřte, že jste správně nastavili účet úložiště, předponu cesty a formát data.

 
## <a name="set-query"></a>Nastavit dotaz
Otevřete oddíl dotazu:

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

Všimněte si, že první pár vlastností je specifických pro data zobrazení stránky. Export dalších typů telemetrie bude mít různé vlastnosti. Informace o [typech a hodnotách vlastností najdete v referenčních informacích k datovému modelu.](./export-data-model.md)

## <a name="set-up-output-to-database"></a>Nastavit výstup do databáze
Jako výstup vyberte SQL.

![Ve službě Stream Analytics vyberte výstupy.](./media/code-sample-export-sql-stream-analytics/SA006.png)

Zadejte databázi.

![Vyplňte podrobnosti vaší databáze.](./media/code-sample-export-sql-stream-analytics/SA007.png)

Zavřete průvodce a počkejte na oznámení, že byl vytvořen výstup.

## <a name="start-processing"></a>Spustit zpracování
Spusťte úlohu z panelu akcí:

![V Stream Analytics klikněte na Start.](./media/code-sample-export-sql-stream-analytics/SA008.png)

Můžete zvolit, zda chcete začít zpracovávat data od verze Now nebo začít s předchozími daty. Druhá je užitečná v případě, že už nějaký průběžný export už běží.

Po několika minutách se vraťte na SQL Server Management Tools a sledujte tok dat v. Například použijte dotaz podobný tomuto:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="related-articles"></a>Související články
* [Export do Power BI pomocí Stream Analytics](./export-power-bi.md)
* [podrobný odkaz na datový model pro typy a hodnoty vlastností.](./export-data-model.md)
* [Průběžný export v Application Insights](./export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[export]: ./export-telemetry.md
[metrics]: ../essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

