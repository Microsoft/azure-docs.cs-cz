---
title: Power BI kurz pro Azure Cosmos DB konektor | Microsoft Docs
description: Tento kurz Power BI použijte k importu JSON, vytvářet sestavy pronikavého a vizualizovat data pomocí konektoru Azure Cosmos DB a Power BI.
keywords: Power bi kurzu, vizualizaci dat, power bi connector
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: cd1b7f70-ef99-40b7-ab1c-f5f3e97641f7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2018
ms.author: sngun
ms.openlocfilehash: 7f884589cc198bed95a4a5fe51325a72cb799b69
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="power-bi-tutorial-for-azure-cosmos-db-visualize-data-using-the-power-bi-connector"></a>Power BI kurz pro Azure Cosmos DB: vizualizovat data pomocí Power BI connector
[PowerBI.com](https://powerbi.microsoft.com/) je online služba, kde můžete vytvářet a sdílet řídicí panely a sestavy s daty, která je pro vás i vaši organizaci důležité.  Power BI Desktop je vyhrazené sestavy vývojového nástroje, která umožňuje načíst data z různých zdrojů dat, sloučení a transformaci dat, vytvářet výkonné sestavy a vizualizací a publikovat sestavy do Power BI.  Pomocí nejnovější verze Power BI Desktop můžete teď připojit k účtu Azure Cosmos DB prostřednictvím konektoru Azure Cosmos DB pro Power BI.   

V tomto kurzu Power BI jsme provede kroky pro připojení k účtu Azure Cosmos DB v Power BI Desktop, přejděte do kolekce, kde chceme extrahovat data pomocí navigátoru, transformovat JSON data v tabulkovém formátu pomocí Power BI Desktop dotazu editoru a vytvářet a publikovat sestavu na PowerBI.com.

Po dokončení tohoto kurzu Power BI, budete moct odpovězte si na následující otázky:  

* Jak lze vytvářet sestavy s daty z databáze Cosmos Azure pomocí Power BI Desktop?
* Jak můžete připojit k účtu Azure Cosmos DB v Power BI Desktop?
* Jak můžete načítat data z kolekce v Power BI Desktop?
* Jak můžete převést vnořené data JSON v Power BI Desktop?
* Jak může sdílet Moje sestavy na PowerBI.com a publikovat?

> [!NOTE]
> Power BI connector pro Azure Cosmos DB připojuje k Power BI Desktop pro extrakci a transformaci dat. Sestavy vytvořené v Power BI Desktop pak dají publikovat na PowerBI.com. Přímé extrakce a transformaci dat Azure Cosmos databáze nelze provést na PowerBI.com. 

> [!NOTE]
> Pokud chcete připojit k Power BI pomocí rozhraní API MongoDB Azure Cosmos DB, musíte použít [ovladač ODBC MongoDB Simba](http://www.simba.com/drivers/mongodb-odbc-jdbc/).

## <a name="prerequisites"></a>Požadavky
Než budete postupovat podle pokynů v tomto kurzu Power BI, ujistěte se, abyste měli přístup na následujících odkazech:

* [Nejnovější verze Power BI Desktop](https://powerbi.microsoft.com/desktop).
* Přístup k naší ukázkový účet nebo data ve vašem účtu Azure Cosmos DB.
  * Ukázkový účet bude zahrnovat sopka data zobrazená v tomto kurzu. Tento ukázkový účet není vázán žádné SLA a je určena pouze pro demonstrační účely.  Jsme vyhrazuje právo k úpravám této ukázkové účtu včetně, ale bez omezení, účet se ukončuje, změníte klíč, omezení přístupu, změna a odstranit data, kdykoli bez předstihu upozornění nebo důvod.
    * URL: https://analytics.documents.azure.com
    * Read-only key: MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==
  * Nebo, pokud chcete vytvořit svůj vlastní účet, najdete v části [vytvoření účtu Azure Cosmos DB databáze pomocí portálu Azure](https://azure.microsoft.com/documentation/articles/create-account/). Potom získat sopka ukázková data, která je podobná co se používá v tomto kurzu (ale neobsahuje bloky GeoJSON) najdete v tématu [NOAA lokality](https://www.ngdc.noaa.gov/nndc/struts/form?t=102557&s=5&d=5) a pak můžete importovat data pomocí [nástroj pro migraci dat Azure Cosmos DB](import-data.md).

Pokud chcete sdílet si sestavy na PowerBI.com, musí mít účet na PowerBI.com.  Další informace o službě Power BI pro volné a Power BI Pro, navštivte [ https://powerbi.microsoft.com/pricing ](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Pusťme se do toho
V tomto kurzu budeme Představte si, že geologist, studujete vulkány po celém světě.  Sopka data jsou uložena v účtu Azure Cosmos DB a dokumenty JSON vypadat jako následující ukázka dokumentu.

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

Chcete načíst data sopka z účtu Azure Cosmos DB a vizualizovat data v interaktivní sestavy Power BI jako následující sestavy.

![Provedením tohoto kurzu Power BI pomocí Power BI connector, bude možné k vizualizaci dat se sestavou nástroje Power BI Desktop sopka](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

Jste připravení a vyzkoušejte ho? Můžeme začít.

1. Na pracovní stanici spusťte Power BI Desktop.
2. Po spuštění Power BI Desktop *úvodní* obrazovka se zobrazí.
   
    ![Power BI Desktop úvodní obrazovce - Power BI connector](./media/powerbi-visualize/power_bi_connector_welcome.png)
3. Můžete **načíst Data**, najdete v části **poslední zdroje**, nebo **otevřít další sestavy** přímo z *úvodní* obrazovky.  Klikněte na tlačítko X v pravém horním rohu zavřete na obrazovce. **Sestavy** Power BI Desktop zobrazí.
   
    ![Power BI Desktop zobrazení sestavy - Power BI connector](./media/powerbi-visualize/power_bi_connector_pbireportview.png)
4. Vyberte **Domů** pásu karet, a potom klikněte na **načíst Data**.  **Načíst Data** okno by se měla objevit.
5. Klikněte na **Azure**, vyberte **Azure DB Cosmos (Beta)**a potom klikněte na **Connect**. 

    ![Power BI Desktop získat Data - Power BI connector](./media/powerbi-visualize/power_bi_connector_pbigetdata.png)   
6. Na **Preview konektor** klikněte na tlačítko **pokračovat**. **Azure Cosmos DB** se zobrazí v okně.
7. Zadejte databázi Cosmos Azure koncový bod adresa URL účtu by chtěli načíst data ze, jak je uvedeno níže a potom klikněte na **OK**. Pokud chcete používat svůj vlastní účet, můžete načíst adresu URL z identifikátoru URI pole v **[klíče](manage-account.md#keys)** okno portálu Azure. Chcete-li použít účet ukázku, zadejte `https://analytics.documents.azure.com` pro adresu URL. 
   
    Nezadávejte název databáze, název kolekce a příkaz jazyka SQL jako tato pole jsou volitelná.  Místo toho vybrat databázi a kolekci k identifikaci, kdy se data pocházejí z použijeme navigátoru.
   
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – okno připojení plochy](./media/powerbi-visualize/power_bi_connector_pbiconnectwindow.png)
8. Pokud se připojujete k tomuto koncovému bodu poprvé, budete vyzváni k klíč účtu. Pro svůj vlastní účet načíst klíč z **primární klíč** pole **[klíče jen pro čtení](manage-account.md#keys)** okno portálu Azure. Pro účet ukázku klíč je `MSr6kt7Gn0YRQbjd6RbTnTt7VHc5ohaAFu7osF0HdyQmfR+YhwCH2D2jcczVIR1LNK3nMPNBD31losN7lQ/fkw==`. Zadejte příslušný klíč a pak klikněte na tlačítko **Connect**.
   
    Doporučujeme používat klíč jen pro čtení, při vytváření sestav.  To zabrání ohrožením hlavního klíče na potenciální rizika zabezpečení. Klíč jen pro čtení má k dispozici [klíče](manage-account.md#keys) okno portálu Azure, nebo můžete použít informace o účtu ukázka výše uvedeného.
   
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – klíč účtu](./media/powerbi-visualize/power_bi_connector_pbidocumentdbkey.png)
    
    > [!NOTE] 
    > Pokud dojde k chybě, která říká "Zadaná databáze nebyl nalezen." najdete v části alternativní řešení kroky v tomto [Power BI problém](https://community.powerbi.com/t5/Issues/Document-DB-Power-BI/idi-p/208200).
    
9. Když se účet úspěšně připojí, **Navigátor** se zobrazí.  **Navigátor** se zobrazí seznam databází v rámci účtu.
10. Klikněte na tlačítko a rozbalte v databázi, kde data pro sestavy bude pocházet z, pokud používáte ukázku účet, vyberte **volcanodb**.   
11. Nyní vyberte kolekci, která se budou načítat data z. Pokud používáte ukázku účtu, vyberte **volcano1**.
    
    Zobrazí se v podokně náhledu seznam **záznam** položky.  Dokument je reprezentován jako **záznam** typu v Power BI. Podobně vnořený blok v dokumentu JSON je také **záznam**.
    
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – okno Navigátor](./media/powerbi-visualize/power_bi_connector_pbinavigator.png)
12. Klikněte na tlačítko **upravit** spustíte Editor dotazů v novém okně pro transformaci dat.

## <a name="flattening-and-transforming-json-documents"></a>Vyrovnání a transformace dokumentů JSON
1. Přepnout na okno editoru dotazů Power BI, kde **dokumentu** sloupec v prostředním podokně.
   ![Editor dotazů aplikace Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png)
2. Klikněte na rozbalovací na pravé straně **dokumentu** záhlaví sloupce.  Zobrazí se v místní nabídce seznam polí.  Vyberte pole, je nutné pro sestavu, pro instanci, sopka název, zemi, oblast, umístění, zvýšení oprávnění, typ, stav a poslední erupcí vědět a pak klikněte na tlačítko **OK**.
   
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – rozbalte dokumenty](./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png)
3. V prostředním podokně se zobrazí náhledu výsledku s poli vybrané.
   
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – vyrovnání výsledky](./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png)
4. V našem příkladu je vlastnost umístění GeoJSON blok v dokumentu.  Jak vidíte, jako je reprezentována umístění **záznam** typu v Power BI Desktop.  
5. Klikněte na rozbalovací na pravé straně záhlaví sloupce umístění.  Zobrazí se v místní nabídce s souřadnice a typ pole.  Umožňuje vybrat pole souřadnice a klikněte na tlačítko **OK**.
   
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – záznam o umístění](./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png)
6. V prostředním podokně zobrazí souřadnice sloupec **seznamu** typu.  Jak je znázorněno na začátku tohoto kurzu, GeoJSON data v tomto kurzu je bod typu s hodnoty zeměpisné šířky a délky zaznamenané v poli souřadnice.
   
    Element souřadnice [0] představuje zeměpisnou délku během šířky představuje souřadnice [1].
    ![Power BI kurz pro Azure Cosmos DB Power BI connector - souřadnice seznamu](./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png)
7. K vyrovnání pole souřadnice, vytvoříme **vlastní sloupec** názvem LatLong.  Vyberte **přidat sloupec** pásu karet a klikněte na **přidat sloupec vlastní**.  **Přidat sloupec vlastní** okno by se měla objevit.
8. Zadejte název pro nový sloupec, například LatLong.
9. Potom zadejte vlastní vzorec pro nový sloupec.  Pro náš příklad jsme se zřetězení hodnoty zeměpisné šířky a délky, oddělených čárkou, jak je uvedeno dále pomocí následujícího vzorce: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Klikněte na **OK**.
   
    Další informace na Data Analysis výrazy (DAX) včetně funkce jazyka DAX, navštivte [DAX základní v Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/554619-dax-basics-in-power-bi-desktop).
   
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – přidat sloupec vlastní](./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png)
10. V prostředním podokně se zobrazí nyní, nový sloupec LatLong, naplní hodnoty zeměpisné šířky a délky, oddělený čárkou.
    
    ![Power BI kurz pro Azure Cosmos DB Power BI connector – vlastní LatLong sloupce](./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png)
    
    Pokud obdržíte chybu do nového sloupce, ujistěte se, že použité kroky v části Nastavení dotazu odpovídat na následujícím obrázku:
    
    ![Použité kroky by měla být zdroje, navigace, rozšířit dokumentu, rozšířit Document.Location, přidat vlastní](./media/powerbi-visualize/power-bi-applied-steps.png)
    
    Pokud vaše postup se liší, kroků navíc odstranit a znovu zkuste přidat vlastní sloupce. 
11. Nyní jsme dokončili vyrovnání data v tabulkovém formátu.  Můžete využít všech funkcí dostupných v editoru dotazů na obrazec a transformovat data do databáze. Cosmos.  Pokud používáte ukázku, změnit datový typ pro zvýšení oprávnění k **celé číslo** změnou **datový typ** na **Domů** pásu karet.
    
    ![Power BI kurz pro Azure Cosmos DB Power BI connector - Změna typu sloupce](./media/powerbi-visualize/power_bi_connector_pbichangetype.png)
12. Klikněte na tlačítko **zavřete a použít** uložit datový model.
    
    ![Power BI kurz pro Azure Cosmos DB Power BI connector - zavřete & použít](./media/powerbi-visualize/power_bi_connector_pbicloseapply.png)

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Vytvoření sestavy
Zobrazení Power BI Desktop sestavy je, kde můžete začít s vytvářením sestav můžete znázorňovat data.  Můžete vytvořit sestavy přetažením polí do **sestavy** plátno.

![Power BI Desktop zobrazení sestavy - Power BI connector](./media/powerbi-visualize/power_bi_connector_pbireportview2.png)

V zobrazení sestavy byste měli najít:

1. **Pole** podokně, to je, kde uvidíte seznam modelů dat s poli, můžete použít pro sestavy.
2. **Vizualizace** podokně. Sestava může obsahovat jeden nebo více vizualizací.  Vyberte typy visual hodí potřeb z **vizualizace** podokně.
3. **Sestavy** plátno, to je, kde vytvoříte vizuálech sestavy.
4. **Sestavy** stránky. Můžete přidat více stránek sestavy v Power BI Desktop.

Na obrázku je základní kroky k vytvoření jednoduché sestavy interaktivní zobrazení mapy.

1. Pro náš příklad vytvoříme zobrazení mapa zobrazuje umístění každého sopka.  V **vizualizace** podokně, klikněte na typ visual mapování jako zvýrazněných v výše uvedený snímek obrazovky.  Měli byste vidět typ visual mapování vykresluje na **sestavy** plátno.  **Vizualizace** podokně by měl zobrazit také sadu vlastností související s typem visual mapování.
2. Nyní, přetáhnout myší pole LatLong z **pole** podokně **umístění** vlastnost v **vizualizace** podokně.
3. V dalším kroku přetažení sopka název pole, které chcete **legendy** vlastnost.  
4. Potom přetáhnout myší pole zvýšení oprávnění, které chcete **velikost** vlastnost.  
5. Teď byste měli vidět mapy visual zobrazující sadu bubliny, která určuje umístění každého sopka s velikostí bublin k zvýšení úrovně sopka korelace.
6. Nyní jste vytvořili základní sestavy.  Sestavu můžete přizpůsobit přidáním více vizualizací.  V našem případě jsme přidali průřez sopka typ aby interaktivní sestavy.  
   
    ![Snímek obrazovky po dokončení tohoto kurzu Power BI pro Azure Cosmos DB konečné sestavy Power BI Desktop](./media/powerbi-visualize/power_bi_connector_pbireportfinal.png)

## <a name="publish-and-share-your-report"></a>Publikování a sdílení sestavy
Pokud chcete sdílet sestavy, musí mít účet na PowerBI.com.

1. V Power BI Desktop, klikněte na **Domů** pásu karet.
2. Klikněte na **Publikovat**.  Zobrazí se výzva k zadání uživatelského jména a hesla pro váš účet na PowerBI.com.
3. Po ověření přihlašovacích údajů, bude sestava publikována do umístění, které jste vybrali.
4. Klikněte na tlačítko **otevřete 'PowerBITutorial.pbix' v Power BI** vidět a sdílet sestavu na PowerBI.com.
   
    ![Publikování do Power BI úspěchu! Otevřete kurz v Power BI](./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png)

## <a name="create-a-dashboard-in-powerbicom"></a>Vytvořit řídicí panel na PowerBI.com
Teď, když máte sestavy, umožňuje sdílet na webu PowerBI.com

Při publikování z Power BI Desktop sestavu na PowerBI.com generuje **sestavy** a **datovou sadu** ve vašem klientovi PowerBI.com. Například po publikované sestavy názvem **PowerBITutorial** na PowerBI.com, uvidíte PowerBITutorial v obou **sestavy** a **datové sady** částech na PowerBI.com.

   ![Snímek obrazovky nové sestavy a datové sady na PowerBI.com](./media/powerbi-visualize/powerbi-reports-datasets.png)

Chcete-li vytvořit lze sdílet řídicí panel, klikněte na tlačítko **Pin Live stránky** tlačítko na PowerBI.com sestavy.

   ![Snímek obrazovky nové sestavy a datové sady na PowerBI.com](./media/powerbi-visualize/power-bi-pin-live-tile.png)

Potom postupujte podle pokynů v [připnout dlaždice ze sestavy](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) vytvoření nového řídicího panelu. 

Před vytvořením řídicího panelu můžete provést také ad hoc změny do sestavy. Doporučuje se ale používáte Power BI Desktop k provedení změn a znovu publikovat sestavu na PowerBI.com.

## <a name="refresh-data-in-powerbicom"></a>Aktualizovat data na PowerBI.com
Existují dva způsoby, jak aktualizovat data, ad hoc a naplánované.

Pro ad hoc aktualizace, klikněte jednoduše na eclipses (...) pomocí **datovou sadu**, například PowerBITutorial. Zobrazí seznam akcí, včetně **aktualizovat**. Klikněte na tlačítko **aktualizovat** aktualizovat data.

![Snímek obrazovky aktualizace teď na PowerBI.com](./media/powerbi-visualize/power-bi-refresh-now.png)

Plánovaná aktualizace postupujte takto.

1. Klikněte na tlačítko **naplánovat aktualizaci** v seznamu akcí. 

    ![Snímek obrazovky naplánovat aktualizaci na PowerBI.com](./media/powerbi-visualize/power-bi-schedule-refresh.png)
2. V **nastavení** rozbalte **přihlašovací údaje ke zdroji dat**. 
3. Klikněte na **upravit přihlašovací údaje**. 
   
    Se zobrazí místní okno konfigurace. 
4. Zadejte klíč k připojení k účtu Azure Cosmos DB pro tuto datovou sadu a pak klikněte na **přihlášení**. 
5. Rozbalte položku **naplánovat aktualizaci** a nastavení plánu, kterou chcete datovou sadu obnovte. 
6. Klikněte na tlačítko **použít** a dokončení nastavení plánované aktualizace.

## <a name="next-steps"></a>Další postup
* Další informace o službě Power BI naleznete v tématu [Začínáme s Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Další informace o databázi Cosmos Azure, najdete v článku [cílovou stránku dokumentace Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).

