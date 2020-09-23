---
title: Vizualizace dat vzdáleného monitorování pomocí Power BI – Azure | Microsoft Docs
description: V tomto kurzu se pomocí Power BI Desktop a Cosmos DB integrují data z řešení vzdáleného monitorování do přizpůsobené vizualizace. Tímto způsobem můžou uživatelé vytvářet vlastní řídicí panely a sdílet je s uživateli, kteří nejsou v řešení.
author: dominicbetts
manager: hegate
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 1a7d29583636879698abc598e1935358796b6062
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90976003"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Vizualizace dat vzdáleného monitorování pomocí Power BI

Tento kurz vás provede postupem připojení dat řešení vzdáleného monitorování z CosmosDB do Power BI. Po navázání připojení můžete vytvořit vlastní řídicí panely a přidat je zpátky do řídicího panelu řešení vzdáleného monitorování. Tento workstream umožňuje vytvořit více specializovaných grafů kromě těch, které se nacházejí v poli. Tento kurz pak můžete použít k integraci s jinými datovými proudy nebo k vytvoření vlastních řídicích panelů, které se budou využívat mimo vaše řešení vzdáleného monitorování. Sestavování řídicích panelů v Power BI znamená, že můžete také každý panel vzájemně pracovat, když vyberete konkrétní části. Například můžete mít filtr, který vám ukáže jenom informace o simulovaných nákladech a jednotlivé části řídicího panelu by vám ukázaly, že vám ukáže jenom simulované údaje o automobilu. Pokud chcete použít jiný nástroj než Power BI, můžete tento postup také využít k tomu, abyste nástroj pro vizualizaci zvolili a připojili do databáze Cosmos nebo vlastní databázi, pokud jste ji nastavili. 

## <a name="prerequisites"></a>Požadavky

- Musíte mít aktuálně spuštěné řešení vzdáleného monitorování.
- Musíte mít přístup k [Azure Portal](https://portal.azure.com) a vašemu předplatnému, na kterém běží IoT Hub a řešení.
- Musíte mít nainstalovaný [Power BI Desktop](https://powerbi.microsoft.com) , ale všechny verze budou


## <a name="information-needed-from-azure-portal"></a>Informace potřebné od Azure Portal

1. V případě potřeby přejděte na [Azure Portal](https://portal.azure.com) a přihlaste se.

2. Na panelu na levé straně klikněte na skupiny prostředků.

    ![Navigace na bočním panelu](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Přejděte do skupiny prostředků, ve které je vaše řešení IoT spuštěné, a klikněte na stránku Přehled této skupiny prostředků. 

4. Na této stránce přehledu klikněte na položku, která má typ "Azure Cosmos DB účet", a poté se na stránku s přehledem Cosmos DB datového proudu pro toto řešení IoT povede.

    ![Skupina prostředků](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Na panelu vlevo klikněte na oddíl klíče a poznamenejte si následující hodnoty, které se mají použít v Power BI:

   - Identifikátor URI
   - Primární klíč

     ![Snímek obrazovky zobrazuje účet Azure Cosmos D B s vybranou položkou klíče.](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Nastavení datového proudu v Power BI
  
1. Otevřete Power BI desktopovou aplikaci a v levém horním rohu klikněte na načíst data. 

    ![Získat data](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Až se zobrazí výzva k zadání dat, vyberte Vyhledat "Azure Cosmos DB" a vyberte tento konektor. Tento konektor v podstatě vyžádá data přímo z databáze Cosmos vašeho řešení Azure IoT.
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Zadejte informace, které jste si poznamenali výše:

    * Identifikátor URI
    * Primární klíč

4. Vyberte všechny tabulky, které se mají importovat do Power BI. Tato akce aktivuje načítání dat. Čím déle je vaše řešení spuštěné, tím déle může trvat, než se data načtou (až do několika hodin). 

    ![Import tabulek](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Po načtení dat klikněte v horním řádku Power BI na Upravit dotazy a rozbalte všechny tabulky kliknutím na šipky na žlutém panelu pro každou tabulku. Tato možnost se v podstatě rozbalí a zobrazí všechny sloupce. Všimněte si, že data o akcích, jako je vlhkost, doba zrychlení atd., nejsou správného typu.

    ![Nový sloupec](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Data přicházející do Power BI se například změnila v době, kdy byla přijata prostřednictvím konektoru, do doby v systému UNIX. Pokud chcete tento převod upravit, budete moct vytvořit nový sloupec a pomocí této rovnice ho načíst do formátu data a času: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Aktualizovaná tabulka](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Dokument. Device. msg. Received je pouze jeden ze sloupců se formátováním systému UNIX a lze jej nahradit dalšími uživateli, kteří vyžadují převod. 
  
    Jiné datové body byly převedeny na typ String lze změnit na Double nebo int, kde to bude vhodné, pomocí výše uvedených kroků.

## <a name="creating-a-dashboard"></a>Vytvoření řídicího panelu

Po připojení ke streamu budete připraveni vytvořit přizpůsobené řídicí panely! Řídicí panel níže je příkladem, jak přebírat telemetrii vysílaná na základě našich simulovaných zařízení a zobrazit v nich různé pivoty, jako například: 

* Umístění zařízení na mapě (vpravo)
* Zařízení se stavem a závažností. (vlevo nahoře)
* Zařízení se zavedenými pravidly a v případě, že jsou pro ně vypnuty výstrahy (dole vlevo)

![Vizualizace PowerBi](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikování řídicího panelu a aktualizace dat

Po úspěšném vytvoření řídicích panelů doporučujeme [publikovat Power BI řídicí panely](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) pro sdílení s ostatními.

Také budete chtít [aktualizovat data](https://docs.microsoft.com/power-bi/refresh-data) na publikovaném řídicím panelu, abyste měli jistotu, že máte nejnovější datovou sadu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o tom, jak vizualizovat data vzdáleného monitorování pomocí Power BI

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v těchto tématech:

* [Přizpůsobení uživatelského rozhraní řešení vzdáleného monitorování](iot-accelerators-remote-monitoring-customize.md)
* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Příručka pro řešení potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

