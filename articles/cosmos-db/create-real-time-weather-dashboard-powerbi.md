---
title: Vytvoření řídicího panelu v reálném čase pomocí Azure Cosmos DB, Azure Analysis Services a Power BI
description: Zjistěte, jak vytvořit řídicí panel živého počasí v Power BI pomocí Azure Cosmos DB a Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376590"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Vytvoření řídicího panelu v reálném čase pomocí Azure Cosmos DB a Power BI

Tento článek popisuje kroky potřebné k vytvoření řídicího panelu živého počasí v Power BI pomocí Azure Cosmos DB a Azure Analysis Services. Řídicí panel Power BI zobrazí grafy zobrazující informace o teplotě a srážkách v oblasti v reálném čase.

## <a name="reporting-scenarios"></a>Scénáře vykazování

Existuje několik způsobů, jak nastavit řídicí panely sestav na data uložená v Azure Cosmos DB. V závislosti na požadavcích na neaktuálnost a velikosti dat popisuje následující tabulka nastavení vykazování pro každý scénář:


|Scénář |Nastavení |
|---------|---------|
|1. Generování ad-hoc sestav (bez aktualizace)    |  [Konektor Power BI Cosmos DB s režimem importu](powerbi-visualize.md)       |
|2. Generování ad hoc sestav s periodickou aktualizací   |  [Konektor Power BI Cosmos DB s režimem importu (plánovaná pravidelná aktualizace)](powerbi-visualize.md)       |
|3. Podávání zpráv o velkých datových sadách (< 10 GB)     |  Konektor Power BI Cosmos DB s přírůstkovou aktualizací       |
|4. Vykazování v reálném čase na velkých datových souborech    |  Konektor služby Power BI Azure Analysis Services s přímým dotazem + Službou Azure Analysis Services (konektor Azure Cosmos DB)       |
|5. Podávání zpráv o živých datech s agregáty     |  [Konektor Power BI Spark s přímým dotazem + konektorem Azure Databricks + Cosmos DB Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Podávání zpráv o živých datech s agregací na velkých datových souborech   |  Konektor Služby Power BI Azure Analysis Services s přímým dotazem + Azure Analysis Services + Konektor Azure Databricks + Cosmos DB Spark.       |

Scénáře 1 a 2 lze snadno nastavit pomocí konektoru Azure Cosmos DB Power BI. Tento článek popisuje nastavení pro scénáře 3 a 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI s přírůstkovou aktualizací

Power BI má režim, ve kterém lze nakonfigurovat přírůstkovou aktualizaci. Tento režim eliminuje potřebu vytvářet a spravovat oddíly Služby Azure Analysis Services. Přírůstkovou aktualizaci lze nastavit tak, aby filtrovala pouze nejnovější aktualizace ve velkých datových sadách. Tento režim však funguje jenom se službou Power BI Premium, která má omezení datové sady 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Konektor analýzy Power BI Azure + Azure Analysis Services

Azure Analysis Services poskytuje plně spravovanou platformu jako službu, která hostuje datové modely na podnikové úrovni v cloudu. Masivní datové sady se dá načíst z Azure Cosmos DB do Služby Azure Analysis Services. Chcete-li se vyhnout dotazování na celou datovou sadu po celou dobu, datové sady lze rozdělit do oddílů Azure Analysis Services, které lze aktualizovat nezávisle na různých frekvencích.

## <a name="power-bi-incremental-refresh"></a>Přírůstková aktualizace Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingestování dat o počasí do Azure Cosmos DB

Nastavte kanál pro ingestování pro načtení [dat o počasí](https://catalog.data.gov/dataset/local-weather-archive) do Azure Cosmos DB. Můžete nastavit úlohu [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) pravidelně načítat nejnovější data o počasí do Azure Cosmos DB pomocí zdroje HTTP a jímky Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Připojení Power BI k Azure Cosmos DB

1. **Připojení účtu Azure Cosmos k Power BI** – Otevřete desktop Power BI a pomocí konektoru Azure Cosmos DB vyberte správnou databázi a kontejner.

   ![Konektor Power BI pro Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Konfigurace přírůstkové aktualizace** – Pomocí kroků v [článku přírůstkové aktualizace pomocí Článku Power BI](/power-bi/service-premium-incremental-refresh) nakonfigurujte přírůstkovou aktualizaci pro datovou sadu. Přidejte parametry **RangeStart** a **RangeEnd,** jak je znázorněno na následujícím snímku obrazovky:

   ![Konfigurace parametrů rozsahu](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Vzhledem k tomu, že datová sada obsahuje sloupec Datum, který je v textové podobě, parametry **RangeStart** a **RangeEnd** by měly být transformovány tak, aby používaly následující filtr. V podokně **Rozšířený editor** upravte dotaz, přidejte následující text pro filtrování řádků na základě parametrů RangeStart a RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   V závislosti na tom, který sloupec a datový typ se nachází ve zdrojové datové sadě, můžete odpovídajícím způsobem změnit pole RangeStart a RangeEnd.

   
   |Vlastnost  |Datový typ  |Filtr  |
   |---------|---------|---------|
   |_ts     |   Numeric      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 0, 0, 0)) a [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Datum (například:- 2019-08-19)     |   Řetězec      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") a [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Datum (například:- 2019-08-11 12:00:00)   |  Řetězec       |  [Document.date]> DateTime.ToText(RangeStart,"yyyy-mm-dd HH:mm:ss") a [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **Definujte zásadu aktualizace** – Definujte zásadu aktualizace přechodem na kartu **Přírůstková aktualizace** v **místní** nabídce tabulky. Nastavte zásady aktualizace tak, aby se **aktualizovaly každý den** a ukládalo data za poslední měsíc.

   ![Definovat zásady aktualizace](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignorujte upozornění, že *dotaz M nelze potvrdit jako složený*. Konektor Azure Cosmos DB složí dotazy filtru.

1. **Načtěte data a vygenerujte sestavy** – pomocí dříve načtených dat vytvořte grafy pro vykazování teploty a srážek.

   ![Načtení dat a generování sestavy](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Publikování sestavy do Power BI premium** – Vzhledem k tomu, že přírůstková aktualizace je jenom pro Premium, umožňuje dialogové okno publikování jenom výběr pracovního prostoru na úrovni Premium. První aktualizace může trvat déle, protože se importují historická data. Následné aktualizace dat jsou mnohem rychlejší, protože používají přírůstkové aktualizace.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Konektor analýzy Power BI Azure + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingestování dat o počasí do Azure Cosmos DB 

Nastavte kanál pro ingestování pro načtení [dat o počasí](https://catalog.data.gov/dataset/local-weather-archive) do Azure Cosmos DB. Můžete nastavit úlohu Azure Data Factory (ADF) pravidelně načítat nejnovější data o počasí do Azure Cosmos DB pomocí zdroje HTTP a Cosmos DB Jímky.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Připojení služby Azure Analysis Services k účtu Azure Cosmos

1. **Vytvořte nový cluster** - Azure Analysis Services[Vytvořte instanci služeb Azure Analysis](../analysis-services/analysis-services-create-server.md) ve stejné oblasti jako účet Azure Cosmos a cluster Databricks.

1. **Vytvořte nový tabulkový projekt služby Analysis Services v sadě Visual Studio** -  [Nainstalujte datové nástroje serveru SQL Server (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) a vytvořte tabulkový projekt služby Analysis Services v sadě Visual Studio.

   ![Vytvoření projektu Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Zvolte **instanci integrovaného pracovního prostoru** a nastavte úroveň kompatibility na SQL Server **2017 / Azure Analysis Services (1400)**

   ![Návrhář tabulkových modelů služby Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Přidejte zdroj dat Azure Cosmos DB** – přejděte na nové**zdroje** > **dat** **modely**> a přidejte zdroj dat Azure Cosmos DB, jak je znázorněno na následujícím snímku obrazovky:

   ![Přidání zdroje dat Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Připojte se k Azure Cosmos DB poskytnutím **identifikátoru URI účtu**, **názvu databáze**a **názvu kontejneru**. Teď uvidíte, že data z kontejneru Azure Cosmos se importují do Power BI.

   ![Náhled dat Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Konstrukce modelu Analysis Services** – Otevřete editor dotazů, proveďte požadované operace pro optimalizaci načtené datové sady:

   * Extrahujte pouze kolony související s počasím (teplota a srážky)

   * Extrahujte informace o měsíci z tabulky. Tato data jsou užitečná při vytváření oddílů, jak je popsáno v další části.

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

   Kromě toho změňte datový typ sloupců teploty na Decimal a ujistěte se, že tyto hodnoty lze vYkreslit v Power BI.

1. **Vytvoření oddílů Azure Analysis** – vytvořte oddíly ve službě Azure Analysis Services a rozdělte datovou sadu na logické oddíly, které lze aktualizovat nezávisle a na různých frekvencích. V tomto příkladu vytvoříte dva oddíly, které by rozdělily datovou sadu na data posledního měsíce a vše ostatní.

   ![Vytvoření oddílů analytických služeb](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Vytvořte následující dva oddíly ve službě Azure Analysis Services:

   * **Poslední měsíc** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Historické** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Nasazení modelu na Azure Analysis Server** – klikněte pravým tlačítkem myši na projekt Azure Analysis Services a zvolte **Nasadit**. Přidejte název serveru do podokna **vlastností serveru pro nasazení.**

   ![Nasazení modelu služby Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Konfigurace aktualizací a sloučení oddílů** – Služba Azure Analysis Services umožňuje nezávislé zpracování oddílů. Vzhledem k tomu, že chceme, aby byl oddíl **Poslední měsíc** neustále aktualizován nejnovějšími daty, nastavte interval aktualizace na 5 minut. Není nutné aktualizovat data v historickém oddílu. Kromě toho je třeba napsat nějaký kód konsolidovat poslední měsíc oddíl do historického oddílu a vytvořit nový poslední měsíc oddíl.


## <a name="connect-power-bi-to-analysis-services"></a>Připojení Power BI ke službám analýzy

1. **Připojte se k Serveru Azure Analysis Server pomocí konektoru databáze Azure Analysis Services** – zvolte režim **Live** a připojte se k instanci Azure Analysis Services, jak je znázorněno na následujícím snímku obrazovky:

   ![Získání dat ze služby Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Načtěte data a vygenerujte sestavy** – pomocí dříve načtených dat vytvořte grafy pro vykazování teploty a srážek. Vzhledem k tomu, že vytváříte živé připojení, dotazy by měly být provedeny na data v modelu Služby Azure Analysis Services, které jste nasadili v předchozím kroku. Teplotní grafy se aktualizují do pěti minut po načtení nových dat do Azure Cosmos DB.

   ![Načtení dat a generování sestav](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Další kroky

* Další informace o Power BI najdete [v tématu Začínáme s Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Připojte Qlik Sense k Azure Cosmos DB a vizualizovat svá data](visualize-qlik-sense.md)