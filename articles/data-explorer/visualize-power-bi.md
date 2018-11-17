---
title: 'Kurz: Vizualizace dat z Průzkumníku dat Azure v Power BI'
description: V tomto kurzu se dozvíte, jak propojit Průzkumníka dat Azure s Power BI a vizualizovat svá data.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: c04c9d9618cfc507ebdba170e0697aeaeecb1e99
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854097"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Kurz: Vizualizace dat z Průzkumníku dat Azure v Power BI

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Power BI je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky v rámci organizace. V tomto kurzu se nejprve dozvíte, jak vykreslovat vizuály v Průzkumníku dat Azure. Pak propojíte Průzkumníka dat s Power BI, vytvoříte sestavu založenou na ukázkových datech a tu pak publikujete ve službě Power BI.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete. Pokud ještě nejste zaregistrovaní k Power BI Pro, [zaregistrujte si bezplatnou zkušební verzi](https://app.powerbi.com/signupredirect?pbi_source=web) před tím, než začnete.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vykreslování vizuálů v Průzkumníku dat Azure
> * Připojení k Průzkumníku dat Azure v Power BI Desktopu
> * Práce s daty v Power BI Desktopu
> * Vytvoření sestavy s vizuály
> * Publikování a sdílení sestavy

## <a name="prerequisites"></a>Požadavky

Kromě předplatných Azure a Power BI budete k dokončení tohoto kurzu potřebovat následující:

* [Testovací cluster a databázi](create-cluster-database-portal.md)

* [Ukázková data StormEvents](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (vyberte **DOWNLOAD FREE**)

## <a name="render-visuals-in-azure-data-explorer"></a>Vykreslování vizuálů v Průzkumníku dat Azure

Než se pustíme do Power BI, podívejme se, jak vykreslovat vizuály v Průzkumníku dat Azure. To se skvěle hodí pro rychlé analýzy.

1. Přihlaste se k webu [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. V levém podokně vyberte testovací databázi, která obsahuje ukázková data StormEvents.

1. Do pravého okna vložte následující dotaz a vyberte **Spustit**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Tento dotaz spočítá meteorologické události podle státu. Pak vykreslí sloupcový graf se všemi státy s více než 1 800 meteorologickými událostmi.

    ![Sloupcový graf událostí](media/visualize-power-bi/events-column-chart.png)

1. Do pravého okna vložte následující dotaz a vyberte **Spustit**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Tento dotaz spočítá meteorologické události podle typu pro měsíc červenec ve státě Washington. Pak vykreslí výsečový graf znázorňující procentuální podíl jednotlivých typů událostí.

    ![Výsečový graf událostí](media/visualize-power-bi/events-pie-chart.png)

Teď je čas podívat se na Power BI, ale s vizuály v Průzkumníku dat Azure toho můžete dělat mnohem víc.

## <a name="connect-to-azure-data-explorer"></a>Připojení k Průzkumníku dat Azure

Teď se připojíte k Průzkumníku dat Azure v Power BI Desktopu.

1. V Power BI Desktopu na **Domů** kartu, vyberte možnost **získat Data** pak **Další**.

    ![Získání dat](media/visualize-power-bi/get-data-more.png)

1. Vyhledejte *Průzkumník dat Azure*vyberte **Průzkumník dat Azure (beta verze)**, pak **připojit**.

    ![Vyhledání a získání dat](media/visualize-power-bi/search-get-data.png)

1. Na obrazovce **Předběžná verze konektoru** vyberte **Pokračovat**.

1. Na další obrazovce zadejte název testovacího clusteru a databáze. Cluster by měl mít formát `https://<ClusterName>.<Region>.kusto.windows.net`. Jako název tabulky zadejte *StormEvents*. U všech ostatních možností ponechte výchozí hodnoty a vyberte **OK**.

    ![Možnosti clusteru, databáze a tabulky](media/visualize-power-bi/cluster-database-table.png)

1. Na obrazovce náhledu dat vyberte **Upravit**.

    Tabulka se otevře v editoru Power Query, kde můžete před importem dat upravit řádky a sloupce.

## <a name="work-with-data-in-power-bi-desktop"></a>Práce s daty v Power BI Desktopu

Teď, když máte připojení k Průzkumníku dat Azure, můžete data upravovat v editoru Power Query. Ve sloupci **BeginLat** odstraníte řádky s hodnotami null a sloupec JSON **StormSummary** odstraníte úplně. Toto jsou jednoduché operace, ale při importu dat můžete provádět také složité transformace.

1. Vyberte šipku u sloupce **BeginLat**, zrušte zaškrtnutí políčka **null** a pak vyberte **OK**.

    ![Filtrování sloupce](media/visualize-power-bi/filter-column.png)

1. Klikněte pravým tlačítkem na záhlaví sloupce **StormSummary** a vyberte **Odebrat**.

    ![Odebrání sloupce](media/visualize-power-bi/remove-column.png)

1. V podokně **NASTAVENÍ DOTAZU** změňte název z *Query1* na *StormEvents*.

    ![Změna názvu dotazu](media/visualize-power-bi/query-name.png)

1. Na kartě **Domů** na pásu karet vyberte **Zavřít a použít**.

    ![Zavřít a použít](media/visualize-power-bi/close-apply.png)

    Power Query použije provedené změny a pak naimportuje ukázková data do *datového modelu*. Dalších několik kroků ukazuje, jak tento model rozšířit. Opět se jedná pouze o jednoduchý příklad, abyste získali představu, co všechno je možné.

1. Na levé straně hlavního okna vyberte zobrazení dat.

    ![Zobrazení dat](media/visualize-power-bi/data-view.png)

1. Na kartě **Modelování** na pásu karet vyberte **Nový sloupec**.

    ![Nový sloupec](media/visualize-power-bi/new-column.png)

1. Na řádku vzorců zadejte následující vzorec DAX (Data Analysis Expressions) a pak stiskněte Enter.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Řádek vzorců](media/visualize-power-bi/formula-bar.png)

    Tento vzorec vytvoří sloupec *DurationHours*, který bude počítat, kolik hodin trvaly jednotlivé meteorologické události. Tento sloupec použijete ve vizuálu v další části.

1. Posuňte se do pravé části tabulky, aby se sloupec zobrazil.

## <a name="create-a-report-with-visuals"></a>Vytvoření sestavy s vizuály

Teď, když jsou data naimportovaná a vylepšili jste datový model, je čas vytvořit sestavu s vizuály. Přidáte sloupcový graf založený na délce trvání událostí a mapu znázorňující poškození plodin.

1. Na levé straně okna vyberte zobrazení sestavy.

    ![Zobrazení sestavy](media/visualize-power-bi/report-view.png)

1. V podokně **VIZUALIZACE** vyberte skupinový sloupcový graf.

    ![Přidání sloupcového grafu](media/visualize-power-bi/add-column-chart.png)

    Na plátno se přidá prázdný graf.

    ![Prázdný graf](media/visualize-power-bi/blank-chart.png)

1. V seznamu **POLE** vyberte **DurationHours** a **State**.

    ![Výběr polí](media/visualize-power-bi/select-fields.png)

    Teď máte graf, který ukazuje celkový počet hodin meteorologických událostí podle státu v průběhu roku.

    ![Sloupcový graf doby trvání](media/visualize-power-bi/duration-column-chart.png)

1. Klikněte kamkoli na plátno mimo sloupcový graf.

1. V podokně **VIZUALIZACE** vyberte mapu.

    ![Přidání mapy](media/visualize-power-bi/add-map.png)

1. V seznamu **POLE** vyberte **CropDamage** a **State**. Změňte velikost mapy tak, abyste jasně viděli státy USA.

    ![Mapa poškození plodin](media/visualize-power-bi/crop-damage-map.png)

    Velikost bublin představuje dolarovou hodnotu poškození plodin. Najetím myší na bubliny zobrazíte podrobnosti.

1. Změňte rozmístění a velikost vizuálů, abyste získali podobnou sestavu jako na následujícím obrázku.

    ![Dokončená sestava](media/visualize-power-bi/finished-report.png)

1. Uložte sestavu s názvem *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Publikování a sdílení sestavy

Do této chvíle jste veškerou práci v Power BI prováděli místně s využitím Power BI Desktopu. Teď sestavu publikujete do služby Power BI, kde ji můžete sdílet s ostatními.

1. V Power BI Desktopu na kartě **Domů** na pásu karet vyberte **Publikovat**.

    ![Tlačítko Publikovat](media/visualize-power-bi/publish-button.png)

1. Pokud ještě nejste přihlášeni do Power BI, absolvujte proces přihlášení.

1. Vyberte **Můj pracovní prostor** a pak **Vybrat**.

    ![Výběr pracovního prostoru](media/visualize-power-bi/select-workspace.png)

1. Po dokončení publikování vyberte **Otevřít storm-events.pbix v Power BI**.

    ![Publikování proběhlo úspěšně](media/visualize-power-bi/publishing-succeeded.png)

    Ve službě se otevře sestava se stejnými vizuály a rozložením, které jste definovali v Power BI Desktopu.

1. V pravém horním rohu sestavy vyberte **Sdílet**.

    ![Tlačítko Sdílet](media/visualize-power-bi/share-button.png)

1. Na obrazovce **Sdílet sestavu** přidejte kolegu z vaší organizace a poznámku a pak vyberte **Sdílet**.

    ![Sdílení sestavy](media/visualize-power-bi/share-report.png)

    Pokud má váš kolega odpovídající oprávnění, získá přístup k sestavě, kterou jste sdíleli.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud si vytvořenou sestavu nechcete ponechat, stačí odstranit soubor *storm-events.pbix*. Pokud chcete odstranit publikovanou sestavu, postupujte následovně.

1. V části **Můj pracovní prostor** se posuňte dolů do části **SESTAVY** a vyhledejte **storm-events**.

1. Vyberte tři tečky (**...**) vedle sestavy **storm-events** a pak vyberte **ODEBRAT**.

    ![Odebrání sestavy](media/visualize-power-bi/remove-report.png)

1. Potvrďte odebrání.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zápis dotazů](write-queries.md)
