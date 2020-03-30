---
title: Vizualizace dat vzdáleného monitorování pomocí Power BI – Azure | Dokumenty společnosti Microsoft
description: Tento kurz používá Power BI Desktop a Cosmos DB k integraci dat z řešení vzdáleného monitorování do přizpůsobené vizualizace. Uživatelé tak mohou vytvářet vlastní řídicí panely a sdílet je s uživateli, kteří nejsou v řešení.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 8b0f395936c8628ff7b8dab601168538f97ccdb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184241"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Vizualizace dat vzdáleného monitorování pomocí Power BI

Tento kurz vás provede připojením dat řešení vzdáleného monitorování z CosmosDB do Power BI. Po navázání tohoto připojení pak můžete vytvořit vlastní řídicí panely a přidat je zpět do řídicího panelu řešení vzdáleného monitorování. Tento pracovní proud umožňuje vytvářet více specializovaných grafů, kromě ty, které jsou po vybalení z krabice. Tento kurz pak můžete použít k integraci s jinými datovými proudy nebo vytvořit vlastní řídicí panely, které mají být spotřebovány mimo vzdálené monitorování řešení. Vytváření řídicích panelů v Power BI znamená, že při výběru konkrétních částí můžete také vzájemně komunikovat s každým panelem. Můžete mít například filtr, který zobrazuje pouze informace o simulovaných nákladních vozidlech a každý kus řídicího panelu by interagoval a zobrazoval pouze simulované informace o nákladních vozidlech. Pokud chcete použít jiný nástroj než Power BI, můžete také rozšířit tyto kroky použít vizualizační nástroj volby a připojit se do databáze Cosmos nebo vlastní databáze, pokud jste nastavili jeden. 

## <a name="prerequisites"></a>Požadavky

- Je nutné mít aktuálně spuštěné řešení vzdáleného monitorování.
- Musíte mít přístup k [portálu Azure portal](https://portal.azure.com) a předplatnému, na kterém běží IoT Hub a řešení
- Musíte mít [nainstalovanou desktopovou plochu Power BI,](https://powerbi.microsoft.com) bude dělat libovolnou verzi


## <a name="information-needed-from-azure-portal"></a>Potřebné informace z webu Azure Portal

1. Přejděte na [portál Azure portal](https://portal.azure.com) a v případě potřeby se přihlaste.

2. Na levém panelu klikněte na Skupiny zdrojů.

    ![Nácvik bočního panelu](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Přejděte na skupinu prostředků, na které běží vaše řešení Iot, a klikněte na stránku Přehled této skupiny prostředků. 

4. Na této stránce přehledklikněte na položku, která má typ "Účet Azure Cosmos DB", pak se přejdete na stránku přehledu datového proudu Cosmos DB pro toto řešení IoT.

    ![Skupina prostředků](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Na panelu vlevo klikněte na část "Klávesy" a poznamenejte si následující hodnoty, které se mají použít v Power BI:

   - Identifikátor URI
   - Primární klíč

     ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Nastavení streamu v Power BI
  
1. Otevřete desktopovou aplikaci Power BI a klikněte na "Získat data" v levém horním rohu. 

    ![Získat data](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Až budete vyzváni k zadání dat, zvolte vyhledat "Azure Cosmos DB" a vyberte tento konektor. Tento konektor v podstatě získává data přímo z databáze cosmos vašeho řešení Azure IoT Solution
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Zadejte informace, které jste zaznamenali výše:

    * Identifikátor URI
    * Primární klíč

4. Vyberte všechny tabulky, které chcete importovat do Power BI. Tato akce zahájí načítání dat. Čím déle je vaše řešení spuštěno, tím déle může trvat, než se data načtou (až několik hodin). 

    ![Importovat tabulky](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Po načtení dat klikněte na "Upravit dotazy" v horním řádku Power BI a rozbalte všechny tabulky kliknutím na šipky ve žlutém pruhu pro každou tabulku. To se v podstatě rozbalí, aby se zobrazily všechny sloupce. Všimněte si, jak data pro věci, jako je vlhkost, rychlost času, atd., nejsou správného typu.

    ![Nový sloupec](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Například data přicházející do Power BI byla změněna na čas unixu, když přišel přes konektor. Chcete-li upravit pro tento převod, do budoucna můžete vytvořit nový sloupec a použít tuto rovnici, aby si to do formátu data času: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Aktualizovaná tabulka](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received je pouze jeden ze sloupců s formátováním systému UNIX a může být nahrazen jinými, které potřebují převod. 
  
    Ostatní datové body byly převedeny na typ String lze změnit na Čtyřhra nebo Int, pokud je to vhodné pomocí stejných kroků jako výše.

## <a name="creating-a-dashboard"></a>Vytvoření řídicího panelu

Jakmile je stream připojen, jste připraveni vytvořit si personalizované řídicí panely! Řídicí panel níže je příkladem přijetí telemetrie vyzařované našimi simulovaných zařízení a zobrazení různých čepů kolem něj, jako jsou: 

* Umístění zařízení na mapě (vpravo)
* Zařízení s jejich stavem a závažností. (vlevo nahoře)
* Zařízení s pravidly na místě, a pokud existují nějaké výstrahy jít pryč pro ně (vlevo dole)

![Vizualizace PowerBi](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikování řídicího panelu a aktualizace dat

Po úspěšném vytvoření řídicích panelů doporučujeme [publikovat řídicí panely Power BI,](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) které chcete sdílet s ostatními.

Budete také chtít [aktualizovat data](https://docs.microsoft.com/power-bi/refresh-data) na publikovaném řídicím panelu, abyste měli jistotu, že máte nejnovější sadu dat.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o vizualizaci dat vzdáleného monitorování pomocí Power BI

Další informace o přizpůsobení řešení vzdáleného monitorování naleznete v následujících tématech:

* [Přizpůsobení uživatelského nastavení řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize.md)
* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce odstraňováním potíží s vývojáři](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

