---
title: Kurz Power BI pro konektor Azure Cosmos DB
description: Tento Power BI kurz použijte k importu JSON, vytvoření sestav přehledné a vizualizaci dat pomocí konektoru Azure Cosmos DB a Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: fc3ca5fdde464ba63671512a6ebecd2c314cb192
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91570842"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Vizualizace dat služby Azure Cosmos DB s využitím konektoru Power BI

[Power BI](https://powerbi.microsoft.com/) je online služba, kde můžete vytvářet a sdílet řídicí panely a sestavy. Power BI Desktop je nástroj pro vytváření sestav, který umožňuje načítat data z různých zdrojů dat. Azure Cosmos DB je jedním ze zdrojů dat, které můžete použít s Power BI Desktopem. Pomocí konektoru Azure Cosmos DB pro Power BI můžete propojit Power BI Desktop s účtem služby Azure Cosmos DB.  Po importu dat služby Azure Cosmos DB do Power BI je můžete transformovat, vytvářet z nich sestavy a tyto sestavy publikovat v Power BI.   

Tento článek popisuje kroky potřebné k propojení účtu služby Azure Cosmos DB s Power BI Desktopem. Po propojení můžete přejít ke kolekci, extrahovat data, transformovat data JSON do tabulkového formátu a publikovat sestavy v Power BI.

> [!NOTE]
> Konektor Power BI pro Azure Cosmos DB se připojuje k Power BI Desktop. Sestavy vytvořené v Power BI Desktop mohou být publikovány do PowerBI.com. Přímou extrakci Azure Cosmos DB dat nelze provést z PowerBI.com. 

> [!NOTE]
> Připojení k Azure Cosmos DB pomocí konektoru Power BI se v současné době podporuje jenom pro Azure Cosmos DB účty rozhraní SQL API a Gremlin API.

## <a name="prerequisites"></a>Požadavky
Než budete postupovat podle pokynů v tomto Power BI kurzu, ujistěte se, že máte přístup k následujícím prostředkům:

* [Stáhněte si nejnovější verzi Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Stáhněte si [ukázková data Volcano](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) z GitHubu.

* [Vytvořte účet databáze Azure Cosmos](https://azure.microsoft.com/documentation/articles/create-account/) a importujte data Volcano pomocí [Nástroje pro migraci dat Azure Cosmos DB](import-data.md). Při importu dat zvažte následující nastavení zdroje a cíle v nástroji pro migraci dat:

   * **Zdrojové parametry** 

       * **Importovat z:** Soubory JSON

   * **Cílové parametry** 

      * **Připojovací řetězec:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Klíč oddílu:** /Country 

      * **Propustnost kolekce:** 1000 

Pokud chcete sestavy sdílet v PowerBI.com, musíte mít účet v PowerBI.com.  Další informace o Power BI a Power BI Pro najdete v tématu [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing) .

## <a name="lets-get-started"></a>Pojďme začít
V tomto kurzu si představte, že jste Geologist studiu Volcanoes po celém světě. Data Volcano jsou uložená v účtu Azure Cosmos DB a formát dokumentu JSON je následující:

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Načtete data Volcano z účtu Azure Cosmos DB a vizualizujete data v interaktivní sestavě Power BI.

1. Spusťte Power BI Desktop.

2. Můžete **získat data**, zobrazit **Poslední zdroje**nebo **otevřít jiné sestavy** přímo z úvodní obrazovky. Kliknutím na X v pravém horním rohu zavřete obrazovku. Zobrazí se zobrazení Power BI Desktop **sestavy** .
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

3. Vyberte pás karet **Domů** a pak klikněte na **získat data**.  Mělo by se zobrazit okno **získat data** .

4. Klikněte na **Azure**, vyberte **Azure Cosmos dB (beta verze)** a pak klikněte na **připojit**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

5. Na stránce **Náhled konektoru** klikněte na **pokračovat**. Zobrazí se okno **Azure Cosmos DB** .

6. Zadejte adresu URL koncového bodu Azure Cosmos DB účtu, ze kterého chcete načíst data, jak je znázorněno níže, a pak klikněte na **OK**. Pokud chcete použít vlastní účet, můžete adresu URL načíst z pole URI v okně **klíče** Azure Portal. Volitelně můžete zadat název databáze, název kolekce nebo použít Navigátor k výběru databáze a kolekce k určení místa, odkud data pocházejí.
   
7. Pokud se k tomuto koncovému bodu připojujete poprvé, zobrazí se výzva k zadání klíče účtu. Pro vlastní účet načtěte klíč z pole **primární klíč** v okně **klíče jen pro čtení** Azure Portal. Zadejte příslušný klíč a pak klikněte na **připojit**.
   
   Při vytváření sestav doporučujeme použít klíč jen pro čtení. Tím se zabrání zbytečnému úniku primárního klíče k potenciálním bezpečnostním rizikům. Klíč, který je jen pro čtení, je k dispozici v okně **klíče** Azure Portal. 
    
8. Po úspěšném připojení účtu se zobrazí podokno **navigátor** . **Navigátor** zobrazuje seznam databází pod účtem.

9. Klikněte na a rozbalte databázi, ze které pochází data sestavy, vyberte **volcanodb** (název databáze může být jiný).   

10. Teď vyberte kolekci obsahující data, která se mají načíst, a vyberte **volcano1** (název kolekce může být jiný).
    
    V podokně náhledu se zobrazuje seznam položek **záznamu** .  Dokument je reprezentován jako typ **záznamu** v Power BI. Podobně vnořený blok JSON uvnitř dokumentu je také **záznam**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

12. Klikněte na tlačítko **Upravit** a spusťte Editor dotazů v novém okně pro transformaci dat.

## <a name="flattening-and-transforming-json-documents"></a>Sloučení a transformace dokumentů JSON
1. Přepněte do okna Editor dotazů Power BI, ve kterém se nachází sloupec **dokument** v prostředním podokně.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

1. Klikněte na rozšíření na pravé straně záhlaví sloupce **dokumentu** .  Zobrazí se kontextová nabídka se seznamem polí.  Vyberte pole, která pro sestavu potřebujete, například Volcano název, země, oblast, umístění, zvýšení oprávnění, typ, stav a poslední know Eruption. Zrušte označení pole **použít původní název sloupce jako předponu** a potom klikněte na tlačítko **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

1. V prostředním podokně se zobrazí náhled výsledku s vybranými poli.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

1. V našem příkladu je vlastnost Location blok geografického formátu JSON v dokumentu.  Jak vidíte, umístění je reprezentované jako typ **záznamu** v Power BI Desktop.  

1. Klikněte na rozšíření na pravé straně dokumentu záhlaví sloupce. umístění.  Zobrazí se kontextová nabídka s poli Typ a souřadnice.  Pojďme vybrat pole souřadnice, zajistěte, aby se **použil původní název sloupce jako předpona** , a klikněte na **OK**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

1. Ve středovém podokně se teď zobrazuje sloupec souřadnic typu **seznamu** .  Jak je vidět na začátku kurzu, data o zeměpisném formátu JSON v tomto kurzu jsou typu Point s hodnotami Zeměpisná šířka a délka zaznamenaná v poli souřadnice.
   
   Element souřadnic [0] představuje zeměpisnou délku, zatímco souřadnice [1] představují zeměpisnou šířku.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

1. Chcete-li sloučit pole souřadnic, vytvořte **vlastní sloupec** s názvem latlong.  Vyberte pás karet **Přidat sloupec** a klikněte na **vlastní sloupec**.  Zobrazí se okno **vlastní sloupec** .

1. Zadejte název nového sloupce, např. LatLong.

1. Potom zadejte vlastní vzorec pro nový sloupec.  V našem příkladu budeme zřetězit hodnoty zeměpisné šířky a délky oddělené čárkou, jak je znázorněno v následujícím vzorci: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})` . Klikněte na **OK**.
   
   Další informace o výrazech data Analysis Expressions (DAX), včetně funkcí DAX, najdete [v Power BI Desktop základy jazyka DAX](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

1. Nyní se v prostředním podokně zobrazí nové LatLong sloupce vyplněné hodnotami.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::
    
    Pokud se zobrazí chyba v novém sloupci, ujistěte se, že použité kroky v části nastavení dotazu odpovídají následujícímu obrázku:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::
    
    Pokud se vaše kroky liší, odstraňte nadbytečné kroky a zkuste vlastní sloupec přidat znovu. 

1. Kliknutím na **Zavřít a použít** uložte datový model.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Sestavení sestav

Power BI Desktop zobrazení sestav je místo, kde můžete začít vytvářet sestavy pro vizualizaci dat.  Sestavy můžete vytvářet přetažením a přetažením polí na plátno pro **sestavy** .

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

V zobrazení sestavy byste měli najít tyto informace:

1. V podokně **pole** můžete zobrazit seznam datových modelů s poli, která můžete použít pro sestavy.
1. Podokno **vizualizace** . Sestava může obsahovat jednu nebo více vizualizací.  V podokně **vizualizace** vyberte typy vizuálů, které chcete přizpůsobit vašim potřebám.
1. Na plátně pro **sestavy** je místo, kde sestavíte vizuály pro sestavu.
1. Stránka **sestavy** V Power BI Desktop můžete přidat více stránek sestavy.

Níže jsou uvedeny základní kroky pro vytvoření jednoduché interaktivní sestavy zobrazení mapy.

1. V našem příkladu vytvoříme zobrazení mapy zobrazující umístění jednotlivých Volcano.  V podokně **vizualizace** klikněte výše na typ vizuálu mapy, který je zvýrazněný na snímku obrazovky výše.  Na plátně pro **sestavy** by měl být zobrazený vizuální typ mapy.  V podokně **vizualizace** by se měla zobrazit také sada vlastností souvisejících s typem vizuálu mapy.
1. Nyní přetáhněte pole LatLong z podokna **pole** do vlastnosti **umístění** v podokně **vizualizace** .
1. Dále přetáhněte pole název Volcano do vlastnosti **Legenda** .  
1. Pak přetáhněte pole zvýšení úrovně na vlastnost **Size** .  
1. Nyní byste měli vidět vizuál mapy zobrazující sadu bublin, které označují umístění každého Volcano a velikost bubliny, která odpovídá zvýšení úrovně Volcano.
1. Nyní jste vytvořili základní sestavu.  Sestavu můžete dále upravit přidáním dalších vizualizací.  V našem případě jsme přidali průřez typu Volcano, který sestaví sestavu jako interaktivní.  
   
1. V nabídce soubor klikněte na **Uložit** a uložte soubor jako PowerBITutorial. pbix.

## <a name="publish-and-share-your-report"></a>Publikování a sdílení sestavy
Pokud chcete sestavu sdílet, musíte mít v PowerBI.com účet.

1. V Power BI Desktop klikněte na pás karet **Domů** .
1. Klikněte na **Publikovat**.  Zobrazí se výzva k zadání uživatelského jména a hesla pro svůj účet PowerBI.com.
1. Po ověření přihlašovacích údajů se sestava publikuje do vašeho vybraného cíle.
1. Kliknutím na **otevřít PowerBITutorial. pbix v Power BI** zobrazíte a nasdílíte sestavu na PowerBI.com.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Vytvořte řídicí panel na PowerBI.com
Teď, když máte sestavu, ji můžete sdílet na PowerBI.com

Při publikování sestavy z Power BI Desktop do PowerBI.com vygeneruje **sestavu** a **datovou sadu** v tenantovi PowerBI.com. Například po publikování sestavy s názvem **PowerBITutorial** do PowerBI.com se zobrazí PowerBITutorial v oddílech **sestav** a **datových sad** v PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

Řídicí panel můžete vytvořit tak, že v sestavě PowerBI.com kliknete na tlačítko **připnout živou stránku** .

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

Pak postupujte podle pokynů v tématu [připnutí dlaždice ze sestavy](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) a vytvořte nový řídicí panel. 

Před vytvořením řídicího panelu můžete také provést změny v ad hoc. Doporučuje se ale použít Power BI Desktop k provedení úprav a opětovném publikování sestavy do PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

:::image type="content" source="./media/powerbi-visualize/power-bi-refresh-now.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    :::image type="content" source="./media/powerbi-visualize/power-bi-schedule-refresh.png" alt-text="Power BI Desktop zobrazení sestav – konektor Power BI":::
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Další kroky
* Další informace o Power BI najdete v tématu [Začínáme s Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Další informace o Azure Cosmos DB najdete na [úvodní stránce dokumentace k Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

