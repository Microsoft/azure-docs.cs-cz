---
title: Vytvoření řídicího panelu v reálném čase pomocí Azure Cosmos DB, Azure Analysis Services a Power BI
description: Naučte se, jak vytvořit živý řídicí panel počasí v Power BI pomocí Azure Cosmos DB a Azure Analysis Services.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/04/2019
ms.reviewer: sngun
ms.openlocfilehash: 73251fcbe9f149979d3fd62d14bbca86d77027f2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105640144"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Vytvoření řídicího panelu v reálném čase pomocí Azure Cosmos DB a Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje kroky potřebné k vytvoření živého řídicího panelu počasí v Power BI pomocí Azure Cosmos DB a Azure Analysis Services. Řídicí panel Power BI zobrazí grafy, ve kterých se v reálném čase zobrazí informace o teplotě a vodních srážek v oblasti.

## <a name="reporting-scenarios"></a>Scénáře vytváření sestav

Existují různé způsoby, jak nastavit řídicí panely pro vytváření sestav pro data uložená v Azure Cosmos DB. V závislosti na požadavcích na neaktuálnost a velikost dat popisuje následující tabulka nastavení vytváření sestav pro jednotlivé scénáře:


|Scenario |Nastavení |
|---------|---------|
|1. generování sestav ad hoc (bez aktualizace)    |  [Konektor Power BI Azure Cosmos DB s režimem importu](powerbi-visualize.md)       |
|2. generování sestav ad hoc s pravidelným obnovením   |  [Konektor Power BI Azure Cosmos DB s režimem importu (plánovaná pravidelná aktualizace)](powerbi-visualize.md)       |
|3. vytváření sestav o velkých datových sadách (< 10 GB)     |  Konektor Power BI Azure Cosmos DB s přírůstkovou aktualizací       |
|4. vytváření sestav v reálném čase u velkých datových sad    |  Konektor Power BI Azure Analysis Services s přímým dotazem + Azure Analysis Services (konektor Azure Cosmos DB)       |
|5. vytváření sestav pro živá data s agregacemi     |  [Power BI konektor Spark s přímým dotazem + Azure Databricks + Cosmos DB Spark Connector.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. vytváření sestav o živých datech s agregacemi u velkých datových sad   |  Konektor Power BI Azure Analysis Services s přímým dotazem + Azure Analysis Services + Azure Databricks a Cosmos DB Spark Connector.       |

Scénáře 1 a 2 lze snadno nastavit pomocí konektoru Azure Cosmos DB Power BI. Tento článek popisuje nastavení scénářů 3 a 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI s přírůstkovou aktualizací

Power BI má režim, ve kterém se dá nakonfigurovat přírůstková aktualizace. Tento režim eliminuje nutnost vytvářet a spravovat Azure Analysis Services oddíly. Přírůstková aktualizace se dá nastavit tak, aby se vyfiltroval jenom nejnovější aktualizace ve velkých datových sadách. Tento režim ale funguje jenom s Power BI Premium služby, která má omezení datové sady 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services

Azure Analysis Services poskytuje plně spravovanou platformu jako službu, která je hostitelem datových modelů na podnikové úrovni v cloudu. Obrovské sady dat lze z Azure Cosmos DB načíst do Azure Analysis Services. Aby se zabránilo dotazování celé datové sady na celou dobu, mohou být datové sady rozděleny na Azure Analysis Services oddíly, které lze aktualizovat nezávisle v různých frekvencích.

## <a name="power-bi-incremental-refresh"></a>Přírůstková aktualizace Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingestování dat počasí do Azure Cosmos DB

Nastavte kanál pro příjem dat, který načte [data o počasí](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) do Azure Cosmos DB. Můžete nastavit úlohu [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) , aby pravidelně načetla nejnovější data o počasí do Azure Cosmos DB pomocí zdroje HTTP a Cosmos DB jímky.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Připojit Power BI k Azure Cosmos DB

1. **Připojte účet Azure Cosmos k Power BI** – otevřete Power BI Desktop a pomocí konektoru Azure Cosmos DB vyberte správnou databázi a kontejner.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png" alt-text="Konektor Power BI pro Azure Cosmos DB":::

1. **Konfigurovat přírůstkovou aktualizaci** – při konfiguraci přírůstkové aktualizace pro datovou sadu použijte postup v článku [přírůstková aktualizace s Power BI](/power-bi/service-premium-incremental-refresh) . Přidejte parametry **RangeStart** a **RangeEnd** , jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png" alt-text="Konfigurovat parametry rozsahu":::

   Vzhledem k tomu, že datová sada obsahuje sloupec data, který je v textovém formátu, parametry **RangeStart** a **RangeEnd** by měly být transformovány, aby bylo možné použít následující filtr. V podokně **Rozšířený editor** Upravte dotaz přidáním následujícího textu pro filtrování řádků na základě parametrů RangeStart a RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   V závislosti na tom, který sloupec a datový typ se nachází ve zdrojové datové sadě, můžete pole RangeStart a RangeEnd změnit odpovídajícím způsobem.

   
   |Vlastnost  |Datový typ  |Filtrovat  |
   |---------|---------|---------|
   |_ts        |   Číselný      |  [_ts] > Duration. TotalSeconds (RangeStart-#datetime (1970, 1, 1, 0, 0, 0)) a [_ts] < Duration. TotalSeconds (RangeEnd-#datetime (1970, 1, 1, 0, 0, 0)))       |
   |Datum (například:-2019-08-19)     |   Řetězec      | [Document. Date] > DateTime. ToText (RangeStart, "rrrr-MM-DD") a [Document. Date] < DateTime. ToText (RangeEnd, "rrrr-MM-DD")        |
   |Datum (například:-2019-08-11 12:00:00)   |  Řetězec       |  [Document. Date] > DateTime. ToText (RangeStart, "rrrr-mm-dd HH: mm: SS") a [Document. Date] < DateTime. ToText (RangeEnd, "rrrr-mm-dd HH: mm: SS")       |


1. **Definujte zásadu aktualizace** – Definujte zásadu aktualizace tak, že přejdete na kartu **přírůstková aktualizace** v **místní** nabídce pro tabulku. Nastavte zásady aktualizace tak, aby se aktualizovaly **každý den** , a uložte data posledních měsíců.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png" alt-text="Definovat zásadu aktualizace":::

   Ignorujte upozornění oznamující, že *dotaz M nelze považovat za přeložený*. Konektory Azure Cosmos DB přeloží dotazy filtru.

1. **Načtěte data a generujte sestavy** – pomocí dat, která jste načetli dříve, vytvořte grafy pro hlášení teploty a vodních srážek.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Načtení dat a generování sestavy":::

1. **Publikování sestavy pro Power BI Premium** – protože přírůstková aktualizace je funkcí jenom pro prémii, dialog publikování umožňuje pouze výběr pracovního prostoru na kapacitě Premium. První aktualizace může trvat déle, protože se importují historická data. Další aktualizační data jsou mnohem rychlejší, protože používají přírůstkovou aktualizaci.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis Connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingestování dat počasí do Azure Cosmos DB 

Nastavte kanál pro příjem dat, který načte [data o počasí](https://catalog.data.gov/dataset?groups=climate5434&#topic=climate_navigation) do Azure Cosmos DB. Můžete nastavit úlohu Azure Data Factory (ADF), aby pravidelně načetla nejnovější data o počasí do Azure Cosmos DB pomocí zdroje HTTP a Cosmos DB jímky.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Připojit Azure Analysis Services k účtu Azure Cosmos

1. **Vytvořit nový cluster Azure Analysis Services**  -  [Vytvořte instanci služby Azure Analysis Services](../analysis-services/analysis-services-create-server.md) ve stejné oblasti jako účet Azure Cosmos a cluster datacihly.

1. **Vytvoření nového Analysis Services tabulkového projektu v aplikaci Visual Studio**  -   [Nainstalujte nástroje SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) a vytvořte Analysis Services tabulkový projekt v aplikaci Visual Studio.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png" alt-text="Vytvořit Azure Analysis Services projekt":::

   Vyberte instanci **integrovaného pracovního prostoru** a nastavte úroveň kompatibility na **SQL Server 2017/Azure Analysis Services (1400)** .

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png" alt-text="Návrhář tabulkových modelů Azure Analysis Services":::

1. **Přidejte Azure Cosmos DB zdroj dat** – přejděte do části **modely** Data Sources ( >    >  **nový zdroj dat** ) a přidejte Azure Cosmos DB zdroj dat, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/add-data-source.png" alt-text="Přidat Cosmos DB zdroj dat":::

   Připojte se k Azure Cosmos DB zadáním **identifikátoru URI účtu**, **názvu databáze** a **názvu kontejneru**. Teď můžete vidět data z kontejneru Azure Cosmos, která se importují do Power BI.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png" alt-text="Náhled Azure Cosmos DB dat":::

1. **Vytvoření modelu Analysis Services** – otevřete Editor dotazů a proveďte potřebné operace pro optimalizaci načtené datové sady:

   * Extrahovat pouze sloupce související s počasí (teplota a srážky)

   * Extrahuje z tabulky informace o měsíci. Tato data jsou užitečná při vytváření oddílů, jak je popsáno v následující části.

   * Převést sloupce teploty na číslo

   Výsledný výraz M je následující:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Kromě toho změňte datový typ pro sloupce teploty na Decimal a ujistěte se, že se tyto hodnoty dají vykreslovat v Power BI.

1. **Vytvoření oddílů Azure Analysis** – vytvořte oddíly v Azure Analysis Services, abyste mohli datovou sadu rozdělit na logické oddíly, které se dají aktualizovat nezávisle a v různých frekvencích. V tomto příkladu vytvoříte dva oddíly, které by tuto datovou sadu rozdělily na data nejaktuálnějšího měsíce a všechno ostatní.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png" alt-text="Vytváření oddílů služby Analysis Services":::

   V Azure Analysis Services vytvořte následující dva oddíly:

   * **Poslední měsíc** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Dosavadní** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Nasaďte model do Azure Analysis Server** – klikněte pravým tlačítkem na projekt Azure Analysis Services a vyberte **nasadit**. Přidejte název serveru v podokně **Vlastnosti serveru nasazení** .

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png" alt-text="Nasazení Azure Analysis Services modelu":::

1. **Konfigurace aktualizací oddílů a sloučení** – Azure Analysis Services umožňuje nezávislé zpracování oddílů. Vzhledem k tomu, že chceme, aby se oddíl **nejnovějšího měsíce** průběžně aktualizoval s nejnovějšími daty, nastavte interval aktualizace na 5 minut. Data můžete aktualizovat pomocí [REST API](../analysis-services/analysis-services-async-refresh.md), [Azure Automation](../analysis-services/analysis-services-refresh-azure-automation.md)nebo [Aplikace logiky](../analysis-services/analysis-services-refresh-logic-app.md). Není nutné aktualizovat data v historických oddílech. Kromě toho musíte napsat nějaký kód pro konsolidaci posledního měsíce oddílu do historického oddílu a vytvořit nový oddíl poslední měsíc.

## <a name="connect-power-bi-to-analysis-services"></a>Připojit Power BI k Analysis Services

1. **Připojte se k Azure Analysis Server pomocí konektoru Azure Analysis Services Database** – vyberte **živý režim** a připojte se k instanci Azure Analysis Services, jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png" alt-text="Získání dat ze služby Azure Analysis Services":::

1. **Načtěte data a generujte sestavy** – pomocí dat, která jste načetli dříve, vytvořte grafy pro hlášení teploty a vodních srážek. Vzhledem k tomu, že vytváříte živé připojení, dotazy by se měly provádět na datech v modelu Azure Analysis Services, který jste nasadili v předchozím kroku. Grafy teploty budou aktualizovány do pěti minut po načtení nových dat do Azure Cosmos DB.

   :::image type="content" source="./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png" alt-text="Načtení dat a generování sestav":::

## <a name="next-steps"></a>Další kroky

* Další informace o Power BI najdete v tématu [Začínáme s Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Připojte Qlik smysl pro Azure Cosmos DB a vizualizaci dat](visualize-qlik-sense.md)
