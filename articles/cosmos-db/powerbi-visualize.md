---
title: Kurz Power BI pro konektor Azure Cosmos DB
description: Tento kurz Power BI můžete použít k importu JSON, vytváření přehledných sestav a vizualizaci dat pomocí konektoru Azure Cosmos DB a Power BI.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: 1dbdd428a54ebf38c7b880bb9530935c0f748226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616815"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Vizualizace dat služby Azure Cosmos DB s využitím konektoru Power BI

[Power BI](https://powerbi.microsoft.com/) je online služba, kde můžete vytvářet a sdílet řídicí panely a sestavy. Power BI Desktop je nástroj pro vytváření sestav, který umožňuje načítat data z různých zdrojů dat. Azure Cosmos DB je jedním ze zdrojů dat, které můžete použít s Power BI Desktopem. Pomocí konektoru Azure Cosmos DB pro Power BI můžete propojit Power BI Desktop s účtem služby Azure Cosmos DB.  Po importu dat služby Azure Cosmos DB do Power BI je můžete transformovat, vytvářet z nich sestavy a tyto sestavy publikovat v Power BI.   

Tento článek popisuje kroky potřebné k propojení účtu služby Azure Cosmos DB s Power BI Desktopem. Po propojení můžete přejít ke kolekci, extrahovat data, transformovat data JSON do tabulkového formátu a publikovat sestavy v Power BI.

> [!NOTE]
> Konektor Power BI pro Azure Cosmos DB se připojuje k Power BI Desktopu. Sestavy vytvořené v Power BI Desktopu můžou být publikovány PowerBI.com. Přímé extrakce dat Azure Cosmos DB nelze provést z PowerBI.com. 

> [!NOTE]
> Připojení k Azure Cosmos DB pomocí konektoru Power BI je momentálně podporované pro účty SQL API Azure Cosmos DB a gremlin api.

## <a name="prerequisites"></a>Požadavky
Než budete podle pokynů v tomto kurzu Power BI, ujistěte se, že máte přístup k následujícím zdrojům:

* [Stáhněte si nejnovější verzi Power BI Desktopu](https://powerbi.microsoft.com/desktop).

* Stáhněte si [ukázková data o sopkách](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/master/SampleData/VolcanoData.json) z GitHubu.

* [Vytvořte databázový účet Azure Cosmos](https://azure.microsoft.com/documentation/articles/create-account/) a importujte data sopky pomocí [nástroje pro migraci dat Azure Cosmos DB](import-data.md). Při importu dat zvažte následující nastavení pro zdroj a cíle v nástroji pro migraci dat:

   * **Parametry zdroje** 

       * **Import z:** Soubory JSON

   * **Cílové parametry** 

      * **Připojovací řetězec:**`AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Klíč oddílu:** /Země 

      * **Propustnost kolekce:** 1000 

Chcete-li přehledy sdílet v PowerBI.com, musíte mít účet v PowerBI.com.  Další informace o Power BI a [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing)Power BI Pro najdete v tématu .

## <a name="lets-get-started"></a>Začneme
V tomto tutoriálu si představte, že jste geolog, který studuje sopky po celém světě. Data sopky se ukládají v účtu Azure Cosmos DB a formát dokumentu JSON je následující:

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

Data sopky načtete z účtu Azure Cosmos DB a vizualizujete data v interaktivní sestavě Power BI.

1. Spusťte Power BI Desktop.

2. Můžete **získat data**, viz **Nedávné zdroje**nebo Otevřít **další sestavy** přímo z úvodní obrazovky. Vyberte "X" v pravém horním rohu pro zavření obrazovky. Zobrazí se zobrazení **Sestavy** power bi desktopu.
   
   ![Zobrazení sestavy power BI plochy – konektor Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)

3. Vyberte pás karet **Domů** a klikněte na **Získat data**.  Měl by se zobrazit okno **Získat data.**

4. Klikněte na **Azure**, vyberte **Azure Cosmos DB (Beta)** a klikněte na **Připojit**. 

    ![Power BI Desktop Get Data – konektor Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   

5. Na stránce **Náhled konektoru** klikněte na **Pokračovat**. Zobrazí se okno **Azure Cosmos DB.**

6. Zadejte adresu URL koncového bodu účtu Azure Cosmos DB, ze které chcete načíst data, jak je znázorněno níže, a klikněte na **OK**. Chcete-li použít svůj vlastní účet, můžete načíst adresu URL z pole URI v okně **Klíče** na webu Azure Portal. Volitelně můžete zadat název databáze, název kolekce nebo pomocí navigátoru vybrat databázi a kolekci k identifikaci, odkud data pocházejí.
   
7. Pokud se připojujete k tomuto koncovému bodu poprvé, budete vyzváni ke klíči účtu. Pro svůj vlastní účet načtěte klíč z pole **Primární klíč** v okně **Klíče jen pro čtení** na webu Azure Portal. Zadejte příslušnou klávesu a klepněte na tlačítko **Připojit**.
   
   Doporučujeme použít klíč jen pro čtení při vytváření sestav. Tím se zabrání zbytečnému vystavení hlavního klíče potenciálním bezpečnostním rizikům. Klíč jen pro čtení je k dispozici v okně **Klíče** na webu Azure Portal. 
    
8. Po úspěšném připojení účtu se zobrazí podokno **Navigátor.** **Navigátor** zobrazí seznam databází pod účtem.

9. Klikněte a rozbalte databázi, odkud data sestavy pocházejí, vyberte **volcanodb** (název databáze se může lišit).   

10. Nyní vyberte kolekci, která obsahuje data k načtení, vyberte **volcano1** (název kolekce se může lišit).
    
    Podokno Náhled zobrazuje seznam **položek záznamu.**  Dokument je v Power BI reprezentován jako typ **záznamu.** Podobně vnořený blok JSON uvnitř dokumentu je také **Záznam**.
    
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – okno Navigator](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Kliknutím na **Upravit** spusťte Editor dotazů v novém okně a transformujte data.

## <a name="flattening-and-transforming-json-documents"></a>Sloučení a transformace dokumentů JSON
1. Přepněte do okna Editoru dotazů Power BI, kde je sloupec **Dokument** v prostředním podokně.
   ![Editor dotazů v Power BI Desktopu](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klikněte na rozbalovací pole na pravé straně záhlaví sloupce **Dokument.**  Zobrazí se kontextová nabídka se seznamem polí.  Vyberte pole, která potřebujete pro sestavu, například Název sopky, Země, Oblast, Umístění, Nadmořská výška, Typ, Stav a Poslední známá erupce. Zrušte zaškrtnutí políčka **Použít původní název sloupce jako předponu** a klepněte na tlačítko **OK**.
   
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – rozbalte dokumenty](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. Prostřední podokno zobrazuje náhled výsledku s vybranými poli.
   
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – slomení výsledků](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. V našem příkladu location vlastnost je blok GeoJSON v dokumentu.  Jak můžete vidět, umístění je reprezentováno jako typ **záznamu** v Power BI Desktopu.  
5. Klikněte na rozbalovací pole na pravé straně záhlaví sloupce Document.Location.  Zobrazí se kontextová nabídka s poli typu a souřadnic.  Vyberte pole souřadnic, ujistěte se, že není vybrán **název původního sloupce jako předpona** a klepněte na **OK**.
   
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – záznam umístění](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. Prostřední podokno nyní zobrazuje sloupec souřadnic typu **Seznam.**  Jak je znázorněno na začátku kurzu, data GeoJSON v tomto kurzu je typu Point s hodnotami zeměpisné šířky a délky zaznamenané v poli souřadnic.
   
    Prvek coordinates[0] představuje zeměpisnou délku, zatímco souřadnice[1] představují zeměpisnou šířku.
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – seznam souřadnic](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Chcete-li sloučí souřadnice pole, vytvořte **vlastní sloupec** s názvem LatLong.  Vyberte pás karet **Přidat sloupec** a klikněte na **Vlastní sloupec**.  Zobrazí se okno **Vlastní sloupec.**
8. Zadejte název nového sloupce, například LatLong.
9. Dále zadejte vlastní vzorec pro nový sloupec.  V našem příkladu zřetězíme hodnoty zeměpisné šířky a délky oddělené čárkou, jak `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`je znázorněno níže, pomocí následujícího vzorce: . Klikněte na tlačítko **OK**.
   
    Další informace o výrazech analýzy dat (DAX) včetně funkcí jazyka DAX najdete na webu [Základy jazyka DAX v Aplikaci Power BI Desktop](https://docs.microsoft.com/power-bi/desktop-quickstart-learn-dax-basics).
   
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – přidání vlastního sloupce](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. Nyní prostřední podokno zobrazuje nové sloupce LatLong naplněné hodnotami.
    
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – sloupec Custom LatLong](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Pokud se v novém sloupci zobrazí chyba, ujistěte se, že použité kroky v části Nastavení dotazu odpovídají následujícímu obrázku:
    
    ![Použité kroky by měly být Zdroj, Navigace, Rozbalený dokument, Rozbalený dokument.Umístění, Přidáno vlastní](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Pokud se vaše kroky liší, odstraňte další kroky a zkuste přidat vlastní sloupec znovu. 

11. Kliknutím na **Zavřít a Použít** uložte datový model.
    
    ![Kurz Power BI pro konektor Azure Cosmos DB Power BI – použít &](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Vytváření sestav
Zobrazení sestavy Power BI na ploše je místo, kde můžete začít vytvářet sestavy pro vizualizaci dat.  Sestavy můžete vytvářet přetažením polí na plátno **sestavy.**

![Zobrazení sestavy power BI plochy – konektor Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

V zobrazení Sestavy byste měli najít:

1. V podokně **Pole** se zobrazí seznam datových modelů s poli, která můžete použít pro sestavy.
2. Podokno **Vizualizace.** Sestava může obsahovat jednu nebo více vizualizací.  Vpodoknu **Vizualizace** vyberte vizuální typy odpovídající vašim potřebám.
3. Na plátně **Sestavy** se vytvářejí vizuály pro sestavu.
4. Stránka **Sestava.** V Power BI Desktopu můžete přidat víc stránek sestav.

Následující text ukazuje základní kroky vytvoření jednoduché interaktivní sestavy zobrazení mapy.

1. Na příklad uvedeme zobrazení mapy zobrazující umístění každé sopky.  V podokně **Vizualizace** klikněte na vizuální typ mapy, jak je zvýrazněný na obrázku obrazovky výše.  Na plátně **Sestavy** by měl být namalován vizuální typ mapy.  V podokně **Vizualizace** by měla být také zobrazena sada vlastností souvisejících s vizuálním typem Mapy.
2. Nyní přetáhněte pole LatLong z podokna **Pole** do vlastnosti **Umístění** v podokně **Vizualizace.**
3. Dále přetáhněte pole Název sopky do vlastnosti **Legend.**  
4. Potom přetáhněte pole Výška do **vlastnosti Velikost.**  
5. Nyní byste měli vidět vizuál Mapy zobrazující sadu bublin označujících umístění každé sopky s velikostí bubliny korelace s nadmořskou výškou sopky.
6. Nyní jste vytvořili základní sestavu.  Sestavu můžete dále přizpůsobit přidáním dalších vizualizací.  V našem případě jsme přidali kráječ Volcano Type, aby byla sestava interaktivní.  
   
7. V nabídce Soubor klikněte na **Uložit** a uložit soubor jako PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publikování a sdílení sestavy
Chcete-li sdílet přehled, musíte mít účet v PowerBI.com.

1. V Power BI Desktopu klikněte na pás karet **Domů.**
2. Klikněte na **Publikovat**.  Budete vyzváni k zadání uživatelského jména a hesla pro váš účet PowerBI.com.
3. Po ověření pověření je sestava publikována do vybraného cíle.
4. Kliknutím na **Otevřít "PowerBITutorial.pbix" v Power BI** zobrazíte a sdílejte sestavu o PowerBI.com.
   
    ![Publikování na Power BI úspěch! Otevření kurzu v Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Vytvořte řídicí panel na PowerBI.com
Nyní, když máte zprávu, umožňuje sdílet na PowerBI.com

Když publikujete sestavu z Power BI Desktopu na PowerBI.com, vygeneruje **sestavu** a **datovou sadu** ve vašem PowerBI.com tenantovi. Například po publikování sestavy s názvem **PowerBITutorial** PowerBI.com, uvidíte PowerBITutorial v **sestavách** a **datových sad** oddíly na PowerBI.com.

   ![Snímek obrazovky s novou sestavou a datovou sadou v PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Pokud chcete vytvořit sharable řídicí panel, klikněte na tlačítko **Připnout živou stránku** v sestavě PowerBI.com.

   ![Snímek obrazovky s novou sestavou a datovou sadou v PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Potom podle pokynů v [části Připnutí dlaždice ze sestavy](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) vytvořte nový řídicí panel. 

Před vytvořením řídicího panelu můžete také provést úpravy ad hoc sestavy. Doporučujeme však použít Power BI Desktop k provedení úprav a znovu publikovat sestavu, aby PowerBI.com.

<!-- ## Refresh data in PowerBI.com
There are two ways to refresh data, ad hoc and scheduled.

For an ad hoc refresh, simply click on the eclipses (…) by the **Dataset**, e.g. PowerBITutorial. You should see a list of actions including **Refresh Now**. Click **Refresh Now** to refresh the data.

![Screenshot of Refresh Now in PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

For a scheduled refresh, do the following.

1. Click **Schedule Refresh** in the action list. 

    ![Screenshot of the Schedule Refresh in PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. In the **Settings** page, expand **Data source credentials**. 
3. Click on **Edit credentials**. 
   
    The Configure popup appears. 
4. Enter the key to connect to the Azure Cosmos DB account for that data set, then click **Sign in**. 
5. Expand **Schedule Refresh** and set up the schedule you want to refresh the dataset. 
6. Click **Apply** and you are done setting up the scheduled refresh.
-->
## <a name="next-steps"></a>Další kroky
* Další informace o Power BI najdete [v tématu Začínáme s Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Další informace o Azure Cosmos DB najdete na [vstupní stránce dokumentace Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

