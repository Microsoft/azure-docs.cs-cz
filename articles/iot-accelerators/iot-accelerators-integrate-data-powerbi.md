---
title: Vizualizovat data vzdálené monitorování pomocí Power BI – Azure | Dokumentace Microsoftu
description: Tento kurz používá Power BI Desktopu a Cosmos DB k integerate data z řešení vzdáleného monitorování do vlastní vizualizace. Tímto způsobem uživatelé můžete vytvářet své vlastní vlastní řídicí panely a sdílet je pro uživatele, ne na řešení.
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: ae039573cf202059114f23cca86207c117a35ead
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970395"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Vizualizovat data vzdálené monitorování pomocí Power BI

Tento kurz vás provede jak k řešení vzdáleného monitorování dat z cosmos DB do Power BI. Pomocí tohoto připojení můžete pak vytvoření vlastních řídicích panelů a přidat zpět na řídicí panel řešení vzdáleného monitorování. Tato workstream umožňuje více specializované grafy, abyste vytvořili, kromě těch, které jsou připravené. Potom můžete tento kurz integrace s jinými datovými proudy nebo vytvářet vlastní řídicí panely, který se má používat mimo řešení vzdáleného monitorování. Vytváření řídicích panelů v Power BI znamená, že můžete také provést jednotlivé panely komunikovat mezi sebou při výběru jednotlivých konkrétních. Například můžete mít filtr, který se zobrazí pouze informace o simulovaných vozů a každého jednotlivého řídicího panelu by interakci lze zobrazit, že pouze simulované truck informace. Pokud jste chtěli použít nástroj než Power BI, můžete taky rozšířit tohoto postupu použít váš nástroj na vizualizaci podle výběru a integrovat do databáze Cosmos, nebo vlastní databázi, pokud jste nastavili jeden. 

## <a name="prerequisites"></a>Požadavky

- Musíte mít nainstalováno řešení vzdáleného monitorování
- Musíte mít přístup k [webu Azure Portal](https://portal.azure.com) a vaše předplatné, ve kterém jsou spuštěny služby IoT Hub a řešení
- Musíte mít [Power BI desktopu](https://powerbi.microsoft.com) nainstalované, bude provádět všechny verze


## <a name="information-needed-from-azure-portal"></a>Informace o potřebné z webu Azure Portal

1. Přejděte do [webu Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste

2. Na panelu vlevo klikněte na skupiny prostředků

    ![Boční Panel Nav](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Přejděte do skupiny prostředků, z nichž je spuštěn řešení Iot na a klikněte na tlačítko přejdete na stránku přehled této skupiny prostředků. 

4. Na stránce Přehled klikněte na položku, která má typ "Účet služby Azure Cosmos DB", pak přejdete na stránku přehled streamu Cosmos DB pro příslušné řešení IoT.

    ![Skupina prostředků](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. Na panelu vlevo klikněte v části "Klíče" a poznamenejte následující hodnoty, které se použijí ve službě Power BI:

    - URI
    - Primární klíč

    ![keys](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Nastavení Stream v Power BI
  
1. Otevřete aplikaci Power BI desktop a klikněte na možnost "Získat Data" z levého horního rohu. 

    ![Získání dat](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. Když se zobrazí výzva k zadání dat, zvolte možnost Hledat "Azure Cosmos DB" a vyberte tento konektor. Tento konektor v podstatě čte data přímo z databáze cosmos vašeho řešení Azure IoT
  
    ![Databáze Cosmos](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. Zadejte informace, které jste pořídili jste výše:

    * URI
    * Primární klíč

4. Vyberte všechny tabulky k importu do Power BI. Tím se spustí řízený načítá data. Čím delší je řešení spuštěné, tím déle, může trvat pro načtení dat (až několik hodin). 

    ![Import tabulky](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. Po dokončení data načítání, klikněte na tlačítko "Upravit dotazy" Power BI do horního řádku a rozšířit všechny tabulky kliknutím na šipky v žlutý pruh pro každou tabulku. V podstatě se rozbalí a zobrazí všechny sloupce. Můžete si všimnout, jak data pro takové věci, jako je například vlhkosti, urychlili čas a podobně nejsou nesprávného typu.

    ![Nový sloupec](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    Například data přicházející do Power BI se změnil na UNIXOVÝ čas, kdy ho byli zaznamenáni v prostřednictvím konektoru. Pro tento převod, upravte od nynějška můžete můžete vytvořit nový sloupec a použít rovnice můžete načíst data formát času: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Aktualizovaná tabulka](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.Device.msg.Received je právě jeden z těchto sloupců se systémem UNIX formátování a s ostatními, které je třeba převodu se můžou nahradit. 
  
    Dalších datových bodů bylo převedeno na typ, řetězec je možné změnit do hodnot datového typu Double nebo Int, ve kterém příslušný pomocí stejných kroků jako výše.

## <a name="creating-a-dashboard"></a>Vytváření řídicího panelu

Jakmile datový proud byl připojen, budete chtít vytvořit přizpůsobené řídicí panely! Řídicí panel níže je příklad odběr, který otáčí telemetrická data se immmited podle našich Simulovaná zařízení a zobrazuje různé kolem, jako: 

* Umístění zařízení na mapě (vpravo)
* Zařízení s jejich stavu a závažnosti. (nahoře vlevo)
* Zařízení s pravidly na místě a pokud neexistují žádné alarmy bude pro ně (vlevo dole)

![Vizualizace Power BI](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikování řídicího panelu a aktualizace dat

Po úspěšném vytvoření řídicích panelů, doporučujeme vám [publikovat řídicí panely Power BI](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files) ke sdílení s ostatními.

Budete také chtít [aktualizovat data](https://docs.microsoft.com/power-bi/refresh-data) na publikovaný řídicí panel, abyste měli jistotu, že máte nejnovější datové sady.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o tom, jak vizualizovat vzdáleného monitorování dat pomocí Power BI

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v tématu:

* [Přizpůsobení řešení vzdáleného monitorování uživatelského rozhraní](iot-accelerators-remote-monitoring-customize.md)
* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce řešením potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

