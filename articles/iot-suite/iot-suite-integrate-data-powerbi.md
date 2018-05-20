---
title: Vizualizovat data vzdálené monitorování pomocí Power BI - Azure | Microsoft Docs
description: Tento kurz používá Power BI Desktop a Cosmos DB integerate data z řešení vzdáleného monitorování do přizpůsobené vizualizace. Tímto způsobem uživatelé můžete vytvořit své vlastní vlastní řídicí panely a sdílet je uživatelům, nikoli na řešení.
services: iot-suite
suite: iot-suite
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-suite
ms.date: 05/01/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 45b91241223b1286cc412ab609e60aeadef51b85
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Vizualizovat data vzdálené monitorování pomocí Power BI

Tento kurz vás provede postup zařaďte svá data řešení vzdáleného monitorování z CosmosDB do Power BI. K tomuto připojení navázat můžete pak vytvořit vlastní řídicí panely a přidat je do řídicího panelu řešení vzdáleného monitorování. Tato workstream umožňuje více specializované grafy má být vytvořen, kromě těch mimo pole. Potom můžete v tomto kurzu k integraci s jinými datovými proudy nebo sestavit vlastní řídicí panely a využijí mimo řešení vzdáleného monitorování. Vytváření řídicích panelů v Power BI znamená, můžete provést také jednotlivé panely vzájemné interakce při výběru konkrétní části. Například můžete mít filtr, který vám zobrazí pouze informace o vaší simulované dodávky a každá část řídicího panelu by interakci zobrazit že pouze simulated vůz informace. Pokud jste chtěli použít nástroj než Power BI, můžete také rozšířit následující postup použijte vaše vizualizace nástroje výběru a připojit do databáze Cosmos nebo vlastní databázi, pokud jste nastavili jednu. 

## <a name="prerequisites"></a>Požadavky

- Musíte mít aktuálně spuštěných řešení vzdáleného monitorování
- Musíte mít přístup k [portálu Azure](https://portal.azure.com) ale vaše předplatné, na kterém běží služby IoT Hub a řešení
- Musíte mít [Power BI desktop](https://powerbi.microsoft.com) nainstalovaná, budou se všechny verze


## <a name="information-needed-from-azure-portal"></a>Informace, které potřebujete z portálu Azure

1. Přejděte na [portálu Azure](https://portal.azure.com) a přihlaste se v případě potřeby

2. Na levém panelu klikněte na skupiny prostředků

    ![Nav straně panelu](media/iot-suite-remote-monitoring-powerbi/side_panel.png)

3. Přejděte do skupiny prostředků, které běží vaše řešení Iot na a klikněte na tlačítko přejdete na stránku přehled příslušné skupině prostředků. 

4. Na této stránce Přehled klikněte na položku, která má typ "Azure Cosmos DB účet", pak přejdete na stránku přehled Cosmos DB datového proudu pro toto řešení IoT.

    ![Skupina prostředků](media/iot-suite-remote-monitoring-powerbi/resource_groups.png)

5. Na panelu vlevo klikněte sekci "Klíče" a poznamenejte následující hodnoty pro použití v Power BI:

    - URI
    - Primární klíč

    ![keys](media/iot-suite-remote-monitoring-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Nastavení datového proudu ve službě Power BI
  
1. Otevřete aplikaci Power BI desktop a klikněte na možnost "Získat Data" z levého horního rohu. 

    ![Získání dat](media/iot-suite-remote-monitoring-powerbi/get_data.png)

2. Když budete vyzváni k zadávání dat, rozhodnete vyhledejte "Azure Cosmos DB" a vyberte tento konektor. Tento konektor v podstatě čte data přímo z databáze cosmos řešení Azure IoT
  
    ![Databáze Cosmos](media/iot-suite-remote-monitoring-powerbi/cosmos_db.png)
  
3. Zadejte informace, které jste nahráli výše:

    * URI
    * Primární klíč

4. Vyberte všechny tabulky, které mají být importována do Power BI. Tato akce bude ji načítání data. Čím delší spuštění řešení, tím déle, může trvat k načtení dat (až několik hodin). 

    ![Import tabulek](media/iot-suite-remote-monitoring-powerbi/import_tables.png)

5. Po dokončení data načítání, klikněte na "Upravit dotazy" v horním řádku Power BI a rozbalte všechny tabulky kliknutím šipky v žlutý pruh pro každou tabulku. To v podstatě se rozbalí a zobrazí všechny sloupce. Si všimnete, jak jsou data pro akcí, například vlhkosti, čas rychlost atd není správného typu.

    ![Nový sloupec](media/iot-suite-remote-monitoring-powerbi/new_column.png)
  
    Například dat pocházejících do Power BI bylo změněno do UNIX čas, kdy pochází prostřednictvím konektoru. Upravit pro tento převod, chvíle jste můžete vytvoří nový sloupec a použít tento rovnice nahrát do formátu času datum: 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![Aktualizované tabulky](media/iot-suite-remote-monitoring-powerbi/updated_table.png)
  
    Document.Device.msg.Received je právě jeden ze sloupců s UNIX formátování a s ostatními uživateli, které je třeba převod se můžou nahradit. 
  
    Ostatní datové body byly převést na typ, který lze změnit řetězec do hodnoty Double nebo Int, kde odpovídající pomocí stejné kroky jako výše.

## <a name="creating-a-dashboard"></a>Vytváření řídicího panelu

Jakmile datový proud byl připojen, jste připraveni vytvořit přizpůsobené řídicí panely! Řídicí panel dole je příklad pořízení, které telemetrii je immmited naše simulované zařízení a zobrazuje různé otáčí kolem něj, jako: 

* Umístění zařízení na mapě (napravo)
* Zařízení s jejich stavu a závažnosti. (vlevo nahoře)
* Zařízení s pravidly na místě a pokud jsou všechny výstrahy přejdete pro ně (vlevo dole)

![Vizualizace PowerBi](media/iot-suite-remote-monitoring-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>Publikování řídicího panelu a aktualizace dat

Po úspěšně jste vytvořili svoje řídicí panely, doporučujeme vám [publikovat svoje řídicí panely Power BI](https://docs.microsoft.com/en-us/power-bi/desktop-upload-desktop-files) sdílet s ostatními uživateli.

Budete také chtít [aktualizuje data](https://docs.microsoft.com/en-us/power-bi/refresh-data) na řídicím panelu publikovaných a ujistěte se, že máte nejnovější datové sady.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o tom, která bude vizualizovat data vzdálené monitorování pomocí Power BI

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v tématu:

* [Přizpůsobení řešení vzdáleného monitorování uživatelského rozhraní](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md)
* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce řešením potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

