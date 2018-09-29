---
title: Kurz k Power BI pro konektor služby Azure Cosmos DB | Dokumentace Microsoftu
description: Pomocí tohoto kurzu Power BI importovat JSON, vytvořit Chytré sestavy a vizualizace dat pomocí konektoru služby Azure Cosmos DB a Power BI.
keywords: Power bi kurz, vizualizace dat, konektor power bi
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: sngun
ms.openlocfilehash: b5b3a96991a2150e553af01e3fda73e04116d7fb
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452407"
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Power BI kurz pro Azure Cosmos DB: vizualizovat data pomocí konektoru Power BI
[PowerBI.com](https://powerbi.microsoft.com/) je online služba, kde můžete vytvářet a sdílet řídicí panely a sestavy s daty, která je pro vás a vaši organizaci důležité.  Power BI Desktopu je vyhrazené sestava vývojového nástroje, který umožňuje načtení dat z různých zdrojů dat, sloučit a transformovat data, vytvářet výkonné sestavy a vizualizace a publikovat sestavy Power BI.  V nejnovější verzi Power BI Desktopu je můžete připojit ke svému účtu Azure Cosmos DB pomocí konektoru služby Azure Cosmos DB pro Power BI.   

V tomto kurzu Power BI projdeme kroky pro připojení k účtu služby Azure Cosmos DB v Power BI Desktopu, přejděte do kolekce, ve kterém chceme extrahování dat pomocí navigátoru, transformujte JSON data na formátu tabulky pomocí editoru dotazů v Power BI Desktopu a vytvářejte a publikujte sestavu do: PowerBI.com.

Po dokončení tohoto kurzu Power BI, budete moci odpovědět na následující otázky:  

* Jak můžu vytvořit sestavy s daty ze služby Azure Cosmos DB pomocí Power BI Desktopu?
* Jak můžu připojit k účtu služby Azure Cosmos DB v Power BI Desktopu?
* Jak může načítat data z kolekce v Power BI Desktopu?
* Jak můžete transformovat vnořené data JSON v Power BI Desktopu?
* Jak může sdílet mé sestavy na webu PowerBI.com a publikovat?

> [!NOTE]
> Konektor Power BI pro službu Azure Cosmos DB se připojuje k Power BI Desktopu pro extrakci a transformovat data. Sestavy vytvořené v Power BI Desktopu pak lze publikovat na webu PowerBI.com. Na webu PowerBI.com nelze provést extrakci s přímým přístupem a transformaci dat Azure Cosmos DB. 

> [!NOTE]
> Připojení ke službě Azure Cosmos DB pomocí konektoru Power BI je aktuálně podporované pro rozhraní SQL API služby Azure Cosmos DB a pouze účty rozhraní Gremlin API.

## <a name="prerequisites"></a>Požadavky
Než budete postupovat podle pokynů v tomto kurzu Power BI, ujistěte se, že máte přístup k následujícím prostředkům:

* [Nejnovější verzi Power BI Desktopu](https://powerbi.microsoft.com/desktop).
* Přístup k naší ukázka účtu nebo data ve vašem účtu Azure Cosmos DB.
  * Ukázkový účet se vyplní sopka data zobrazená v tomto kurzu. Tento ukázkový účet není vázán žádné smlouvy SLA a je určena pouze pro demonstrační účely.  Vyhrazujeme si právo k provádění změn na tuto ukázku účtu, včetně, ale mimo jiné, účet se ukončuje, změně klíče, omezení přístupu, změna jsme odstranit data, kdykoli bez předem nebo důvod.
    * ADRESA URL: `https://analytics.documents.azure.com`
    * Klíč jen pro čtení: `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`
  * Nebo pokud chcete vytvořit svůj vlastní účet, najdete v článku [vytvoření účtu databáze Azure Cosmos DB pomocí webu Azure portal](https://azure.microsoft.com/documentation/articles/create-account/). Potom k získání sopka ukázková data, která je podobná v tomto kurzu se používá (ale neobsahuje bloky GeoJSON) naleznete v tématu [NOAA lokality](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) a k následnému importování dat pomocí [nástroj pro migraci dat Azure Cosmos DB](import-data.md).

Sdílet sestavy v PowerBI.com, musí mít účet na PowerBI.com.  Další informace o Power BI pro zdarma a Power BI Pro, najdete v tématu [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Pusťme se do toho
V tomto kurzu Představte si, že máte geologist zkoumání vulkány po celém světě.  Sopka data uložená v účtu služby Azure Cosmos DB a dokumenty JSON vypadat jako následující ukázkový dokument.

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

Chcete načíst data sopka z účtu služby Azure Cosmos DB a vizualizace dat v interaktivní sestavy Power BI stejně jako na následující sestavu.

![Po dokončení tohoto kurzu Power BI s konektor Power BI, budete mít k vizualizaci dat pomocí sopka sestavy Power BI Desktopu](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Jste připravení vyzkoušejte ho? Pusťme se do práce.

1. Spuštění Power BI Desktopu na pracovní stanici.
2. Po spuštění Power BI Desktopu *úvodní* zobrazenou obrazovku na jinou.
   
    ![Power BI Desktopu úvodní obrazovky – konektor Power BI](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Je možné **získat Data**, naleznete v tématu **poslední zdroje**, nebo **otevřít další sestavy** přímo z *úvodní* obrazovky.  Klikněte na tlačítko X v pravém horním rohu zavřete obrazovky. **Sestavy** zobrazení Power BI desktopu.
   
    ![Power BI Desktopu zobrazení sestavy – konektor Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Vyberte **Domů** pásu karet a potom klikněte na **získat Data**.  **Získat Data** by se zobrazit okno.
5. Klikněte na **Azure**vyberte **služby Azure Cosmos DB (beta verze)** a potom klikněte na tlačítko **připojit**. 

    ![Power BI Desktop získat Data – konektor Power BI](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Na **Předběžná verze konektoru** klikněte na **pokračovat**. **Služby Azure Cosmos DB** zobrazí se okno.
7. Zadejte by rádi načíst data, jak je znázorněno níže a potom klikněte na URL koncového bodu účtu služby Azure Cosmos DB **OK**. Použít svůj vlastní účet, můžete zjistit adresu URL z panelu identifikátor URI v **[klíče](manage-account.md#keys)** okna na webu Azure portal. Chcete-li použít ukázkový účet, zadejte `https://analytics.documents.azure.com` pro adresu URL. 
   
    Ponechejte název databáze, název kolekce a příkazu jazyka SQL prázdné, protože tato pole jsou volitelná.  Místo toho použije Navigátor vyberte databázi a kolekci k identifikaci, odkud data pocházejí z.
   
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – okno připojení plochy](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Pokud se připojujete k tomuto koncovému bodu poprvé, zobrazí se výzva k zadání klíč účtu. Pro vlastní účet načíst klíč z **primární klíč** pole **[klíče jen pro čtení](manage-account.md#keys)** okna na webu Azure portal. Pro ukázkový účet klíč je `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Zadejte příslušný klíč a potom klikněte na tlačítko **připojit**.
   
    Doporučujeme použít klíč jen pro čtení, při vytváření sestav.  To zabraňuje ohrožením hlavního klíče na potenciální rizika zabezpečení. Klíč jen pro čtení je k dispozici [klíče](manage-account.md#keys) okna na webu Azure portal nebo můžete pomocí informací o účtu ukázka výše.
   
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – klíč účtu](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Pokud se zobrazí chybová zpráva "Zadaná databáze nebyl nalezen." sledovat řešení kroky v tomto [Power BI problém](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Když se účet úspěšně připojí, **Navigátor** otevře se podokno.  **Navigátor** zobrazí seznam databází v rámci účtu.
10. Klikněte na tlačítko a rozbalte databázi, kde data pro sestavy pochází, pokud používáte ukázku účtu, vyberte **volcanodb**.   
11. Teď vyberte kolekci, která obsahuje data k načtení. Pokud používáte ukázku účtu, vyberte **volcano1**.
    
    V podokně náhledu se zobrazí seznam **záznam** položky.  Dokument je vyjádřena jako **záznam** typ v Power BI. Podobně, vnořený blok uvnitř dokumentu JSON je taky **záznam**.
    
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – okno Navigátor](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Klikněte na tlačítko **upravit** ke spuštění editoru dotazů v novém okně, který umožňuje transformovat data.

## <a name="flattening-and-transforming-json-documents"></a>Sloučení a transformaci dokumentů JSON
1. Přepněte do okna editoru dotazů Power BI, kde **dokumentu** sloupec v prostředním podokně.
   ![Editoru dotazů v Power BI Desktopu](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klikněte na rozšíření na pravé straně **dokumentu** záhlaví sloupce.  Zobrazí se místní nabídky pomocí seznamu polí.  Vyberte pole, které potřebujete pro svoji sestavu, například sopka název, země, oblast, umístění, ke zvýšení úrovně oprávnění, typ, stav a poslední erupcí vědět. Zrušte zaškrtnutí políčka **používat původní název sloupce jako předponu** pole a potom klikněte na tlačítko **OK**.
   
    ![Power BI kurz pro Azure Cosmos DB Power BI konektor – rozbalte dokumenty](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. V prostředním podokně zobrazí náhled výsledku s poli vybrali.
   
    ![Power BI kurz pro Azure Cosmos DB Power BI konektor – sloučit výsledky](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. V našem příkladu je vlastnost umístění bloku GeoJSON v dokumentu.  Jak vidíte, je reprezentována umístění **záznam** typ v Power BI Desktopu.  
5. Klikněte na rozšíření na pravé straně záhlaví sloupce Document.Location.  Zobrazit místní nabídku s poli typu a souřadnice.  Teď vyberte pole souřadnice, ujistěte se, **používat původní název sloupce jako předponu** není vybrána a klikněte na tlačítko **OK**.
   
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – záznam o umístění](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. V prostředním podokně se teď zobrazuje sloupec souřadnice **seznamu** typu.  Jak je znázorněno na začátku tohoto kurzu, GeoJSON data v tomto kurzu jsou bod typu hodnoty zeměpisné šířky a délky zaznamenány v poli souřadnice.
   
    Element souřadnice [0] představuje délky, zatímco představuje zeměpisné souřadnice [1].
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – seznam souřadnice](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. Pokud chcete sloučit pole souřadnice, vytvořte **vlastní sloupec** volá LatLong.  Vyberte **přidat sloupec** pásu karet a klikněte na **vlastní sloupec**.  **Vlastní sloupec** zobrazí se okno.
8. Zadejte název pro nový sloupec, třeba LatLong.
9. Dále zadejte vlastní vzorec pro nový sloupec.  V našem příkladu jsme se zřetězit zeměpisná šířka a délka hodnoty oddělené čárkou, jak je znázorněno níže pomocí následujícího vzorce: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Klikněte na **OK**.
   
    Další informace o výrazech DAX (Data Analysis) včetně funkcí jazyka DAX, navštivte prosím [základního jazyka DAX v Power BI Desktopu](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – přidat vlastní sloupec](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)

10. V prostředním podokně nyní zobrazuje nové sloupce LatLong obsahuje hodnotu.
    
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – LatLong vlastní sloupec](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Pokud v novém sloupci zobrazí chybová zpráva, ujistěte se, že odpovídají použitých kroků v části Nastavení dotazu na následujícím obrázku:
    
    ![Použitý postup by měl být zdroj, navigace, rozbaleného dokumentu, rozbalení Document.Location, přidané: vlastní](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Pokud vaše postup se liší, odstraňte dodatečné kroky a pokuste se znovu přidat vlastní sloupec. 

11. Klikněte na tlačítko **zavřít a použít** k uložení datového modelu.
    
    ![Kurz k Power BI pro konektor Azure Cosmos DB Power BI – zavřít a použít](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Vytváření sestav
Zobrazení Power BI Desktopu sestavy je, kde můžete začít s vytvářením sestav můžete znázorňovat data.  Můžete vytvořit přetažením polí do sestavy **sestavy** plátna.

![Power BI Desktopu zobrazení sestavy – konektor Power BI](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

V zobrazení sestav byste měli najít:

1. **Pole** podokno, to je, kde můžete zobrazit seznam datové modely s poli můžete použít pro sestavy.
2. **Vizualizace** podokně. Sestava může obsahovat jednu nebo více vizualizací.  Vyberte typy vizuálů, které jsou vhodné pro vaše potřeby od **vizualizace** podokně.
3. **Sestavy** plátno, to je, kde vytvářet vizuály sestavy.
4. **Sestavy** stránky. Můžete přidat více stránek sestavy v Power BI Desktopu.

Následující uvádí základní kroky k vytvoření jednoduché interaktivní mapu zobrazit sestavu.

1. V našem příkladu vytvoříme zobrazení mapa zobrazuje umístění každého sopka.  V **vizualizace** podokně, klikněte na typ vizuálu mapy jak je zdůrazněno na snímku obrazovky výše.  Měli byste vidět typ vizuálu mapy vykreslení na **sestavy** plátna.  **Vizualizace** podokno by měl zobrazit také sadu vlastností související s typ vizuálu mapy.
2. Nyní, přetáhnout myší pole LatLong z **pole** podokně **umístění** vlastnost v **vizualizace** podokně.
3. V dalším kroku přetáhněte a umístěte pole Název sopka **legendy** vlastnost.  
4. Potom přetáhnout myší pole ke zvýšení úrovně oprávnění **velikost** vlastnost.  
5. Teď byste měli vidět mapy vizuál zobrazující sadu určující umístění jednotlivých sopka s velikostí bublin korelace zvýšení sopka bubliny.
6. Nyní jste vytvořili základní sestavy.  Sestavu můžete dále přizpůsobit tak, že přidáte další vizualizace.  V našem případě jsme přidali sopka typ průřezu, aby interaktivní sestavy.  
   
    ![Snímek obrazovky se konečná sestava Power BI Desktopu po dokončení tohoto kurzu Power BI pro službu Azure Cosmos DB](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)
7. V nabídce Soubor klikněte na tlačítko **Uložit** a uložte soubor jako PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publikování a sdílení sestavy
Sdílet tuto sestavu, musíte mít účet na PowerBI.com.

1. V Power BI Desktopu, klikněte na **Domů** pásu karet.
2. Klikněte na **Publikovat**.  Zobrazí se výzva k zadání uživatelského jména a hesla pro váš účet na PowerBI.com.
3. Po ověření přihlašovacích údajů k cílové službě, kterou jste vybrali po publikování sestavy.
4. Klikněte na tlačítko **otevřít 'PowerBITutorial.pbix"v Power BI** vidět a sdílet tuto sestavu na PowerBI.com.
   
    ![Publikování do Power BI úspěch! Kurz otevřít v Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Vytvoření řídicího panelu na webu PowerBI.com
Teď, když máte sestavu, umožňuje sdílet na PowerBI.com

Když publikujete sestavu z Power BI Desktopu do: PowerBI.com, generuje **sestavy** a **datovou sadu** ve vašem tenantovi PowerBI.com. Například po publikování sestavy volá **PowerBITutorial** do: PowerBI.com, zobrazí se PowerBITutorial v obou **sestavy** a **datových sad** oddíly na PowerBI.com.

   ![Snímek obrazovky s novou sestavu a datovou sadu na webu PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Pokud chcete vytvořit řídicí panel sdílet, klikněte na tlačítko **připnout živou stránku** tlačítko v sestavě PowerBI.com.

   ![Snímek obrazovky s novou sestavu a datovou sadu na webu PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Postupujte podle pokynů v [Připnutí dlaždice ze sestavy](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) k vytvoření nového řídicího panelu. 

Ad hoc změny do sestavy můžete také provést před vytvořením řídicího panelu. Doporučujeme však, že používáte Power BI Desktopu k provedení změny a znovu publikujte sestavu do: PowerBI.com.

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
## <a name="next-steps"></a>Další postup
* Další informace o Power BI najdete v tématu [Začínáme s Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Další informace o službě Azure Cosmos DB najdete v tématu [cílové stránky dokumentace služby Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

