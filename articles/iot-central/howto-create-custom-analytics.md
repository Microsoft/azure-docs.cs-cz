---
title: Rozšíření Azure IoT Central pomocí vlastní analytics | Dokumentace Microsoftu
description: Jako vývojář řešení konfigurace aplikace IoT Central provedete vlastní analýzy a vizualizace. Toto řešení využívá Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743437"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Rozšíření Azure IoT Central pomocí vlastní analytics

Tato příručka ukazuje, jako vývojář řešení rozšíření IoT Central aplikace s vlastní analýzy a vizualizace. V příkladu se používá [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) pracovní prostor pro analýzu telemetrie stream IoT Central a k vytvoření vizualizace například [pole vykreslení](https://wikipedia.org/wiki/Box_plot).

Tato příručka ukazuje, jak rozšířit nad rámec jeho již přínosech s IoT Central [integrovaných analytických nástrojů](howto-create-analytics.md).

V této příručce s postupy se dozvíte, jak:

* Stream telemetrická data z aplikace IoT Central s využitím *nepřetržitý export dat*.
* Vytvoření prostředí Azure Databricks k analýze a zobrazit telemetrii zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v této příručce s postupy, budete potřebovat aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

### <a name="iot-central-application"></a>Aplikace IoT Central

Vytvořit aplikace IoT Central [Azure IoT Central – Moje aplikace](https://aka.ms/iotcentral) stránka s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Plán plateb | Průběžné platby |
| Šablona aplikace | Sample Contoso (Ukázka Contoso) |
| Název aplikace | Přijměte výchozí adresář nebo zvolte svůj vlastní název |
| zprostředkovatele identity | Přijměte výchozí adresář nebo zvolte vlastní jedinečnou předponu adresy URL |
| Adresář | Váš tenant Azure Active Directory |
| Předplatné Azure | Vaše předplatné Azure |
| Oblast | USA – východ |

Příklady a snímky obrazovky v tomto článku používají **USA – východ** oblasti. Vyberte umístění blízko vás a ujistěte se, že vytváření všech vašich prostředků ve stejné oblasti.

### <a name="resource-group"></a>Skupina prostředků

Použití [webu Azure portal vytvořte skupinu prostředků](https://portal.azure.com/#create/Microsoft.ResourceGroup) volá **IoTCentralAnalysis** tak, aby obsahovala další prostředky, které vytvoříte. Vytvoření prostředků Azure ve stejném umístění jako aplikace IoT Central.

### <a name="event-hubs-namespace"></a>Obor názvů služby Event Hubs

Použití [webu Azure portal vytvořte obor názvů služby Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název    | Vyberte název vašeho oboru názvů |
| Cenová úroveň | Basic |
| Předplatné | Vaše předplatné |
| Skupina prostředků | IoTCentralAnalysis |
| Location | USA – východ |
| Jednotky propustnosti | 1 |

### <a name="azure-databricks-workspace"></a>Pracovní prostor Azure Databricks

Použití [webu Azure portal k vytvoření služby Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název pracovního prostoru    | Vyberte název vašeho pracovního prostoru |
| Předplatné | Vaše předplatné |
| Skupina prostředků | IoTCentralAnalysis |
| Location | USA – východ |
| Cenová úroveň | Standard |

Pokud jste vytvořili požadované prostředky, vaše **IoTCentralAnalysis** skupiny prostředků vypadá jako na následujícím snímku obrazovky:

![Skupina prostředků analýzy IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Můžete nakonfigurovat aplikace IoT Central můžete průběžně exportovat telemetrii do centra událostí. V této části vytvoříte Centrum událostí pro příjem telemetrických dat z vaší aplikace IoT Central. Centrum událostí přináší telemetrie vaší úlohy Stream Analytics ke zpracování.

1. Na webu Azure Portal, přejděte do svého oboru názvů služby Event Hubs a vyberte **+ Centrum událostí**.
1. Název vašeho centra událostí **centralexport**a vyberte **vytvořit**.
1. V seznamu event hubs v oboru názvů vyberte **centralexport**. Klikněte na tlačítko **zásady sdíleného přístupu**.
1. Vyberte **+ Přidat**. Vytvoření zásady s názvem **naslouchání** s **naslouchání** deklarací identity.
1. Jakmile zásadu připravený, vyberte v seznamu a zkopírujte **připojovací řetězec – primární klíč** hodnotu.
1. Poznamenejte si tento připojovací řetězec, můžete použít později při konfiguraci Poznámkový blok Databricks pro čtení z centra událostí.

Váš obor názvů služby Event Hubs bude vypadat jako na následujícím snímku obrazovky:

![Obor názvů služby Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Nakonfigurovat export v IoT Central

Přejděte [aplikace IoT Central](https://aka.ms/iotcentral) vytvoříte pomocí šablony Contoso. V této části nakonfigurujete aplikaci, aby datový proud telemetrie ze své simulovaných zařízení do vašeho centra událostí. Postup konfigurace exportu:

1. Přejděte **průběžný Export dat** stránce **+ nový**a potom **Azure Event Hubs**.
1. Použijte následující nastavení konfigurace exportu a pak vyberte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zobrazované jméno | Export do služby Event Hubs |
    | Enabled | Zapnuto |
    | Obor názvů služby Event Hubs | Název vašeho oboru názvů služby Event Hubs |
    | Centrum událostí | centralexport |
    | Měření | Zapnuto |
    | Zařízení | Vypnuto |
    | Šablony zařízení | Vypnuto |

![Souvislá datová exportovat konfiguraci](media/howto-create-custom-analytics/cde-configuration.png)

Počkejte, dokud je stav exportu **systémem** předtím, než budete pokračovat.

## <a name="configure-databricks-workspace"></a>Konfigurovat pracovní prostor Databricks

Na webu Azure Portal, přejděte k vaší službě Azure Databricks a vyberte **spustit pracovní prostor**. Na nové kartě se otevře v prohlížeči a přihlášení do pracovního prostoru.

### <a name="create-a-cluster"></a>Vytvoření clusteru

Na **Azure Databricks** stránky, v seznamu běžných úloh vyberte **nový Cluster**.

Vytvoření clusteru použijte informace v následující tabulce:

| Nastavení | Hodnota |
| ------- | ----- |
| Název clusteru | centralanalysis |
| Režim clusteru | Standard |
| Verze modulu Runtime Databricks | 5.3 (Scala 2.11, Spark 2.4.0) |
| Python Version | 3 |
| Povolení automatického škálování | Ne |
| Ukončit po minutách nečinnosti | 30 |
| Typ pracovního procesu | Standard_DS3_v2 |
| Pracovní procesy | 1 |
| Typ ovladače | Stejný jako pracovní proces |

Vytvoření clusteru může trvat několik minut, počkejte na dokončení předtím, než budete pokračovat vytváření clusteru.

### <a name="install-libraries"></a>Instalace knihoven

Na **clustery** stránce, počkejte, dokud je stav clusteru **systémem**.

Následující kroky ukazují, jak importovat knihovny musí svou ukázku do clusteru:

1. Na **clustery** stránce, počkejte, dokud se stav **centralanalysis** interaktivní clusteru je **systémem**.

1. Vyberte cluster a klikněte na tlačítko **knihovny** kartu.

1. Na **knihovny** kartě **nainstalovat nový**.

1. Na **nainstalujte knihovnu** zvolte **Maven** jako zdroj knihovny.

1. V **koordinuje** textového pole zadejte následující hodnotu: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Zvolte **nainstalovat** ji nainstalovat na clusteru.

1. Stav knihovny je nyní **nainstalováno**:

    ![Nainstalovanou knihovnu](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Import poznámkového bloku Databricks

Následující kroky použijte pro import poznámkového bloku Databricks, který obsahuje kód Pythonu pro analýzu a vizualizaci telemetrických dat IoT Central:

1. Přejděte **pracovní prostor** stránku ve svém prostředí Databricks. Vyberte rozevírací nabídku vedle názvu účtu a klikněte na tlačítko **Import**.

1. Zvolte možnost importovat z adresy URL a zadejte následující adresu: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Chcete-li importovat Poznámkový blok, zvolte **importovat**.

1. Vyberte **pracovní prostor** Chcete-li zobrazit importované Poznámkový blok:

    ![Importované poznámkového bloku](media/howto-create-custom-analytics/import-notebook.png)

1. Úpravy kódu přidat připojovací řetězec služby Event Hubs, kterou jste si předtím uložili do první buňky Pythonu:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Spuštění analýzy

Spustit analýzu, musíte připojit poznámkového bloku do clusteru:

1. Vyberte **Detached** a pak vyberte **centralanalysis** clusteru.
1. Pokud není spuštěná clusteru, spusťte ji.
1. Spusťte Poznámkový blok, vyberte tlačítko pro spuštění.

Může se zobrazit chyba do poslední buňky. Pokud ano, zkontrolujte, že běží předchozí buněk, Počkejte minutu pro některá data k zápisu do úložiště a poté znovu spusťte poslední buňku.

### <a name="view-smoothed-data"></a>Data zobrazení vyhlazené

V poznámkovém bloku posuňte se dolů buňky 14 zobrazíte vykreslení postupné průměrná vlhkost podle typu zařízení. Tento diagram se průběžně aktualizuje, když dorazí datových proudů telemetrie:

![Vyhlazené vykreslení telemetrie](media/howto-create-custom-analytics/telemetry-plot.png)

Změnit velikost grafu v poznámkovém bloku.

### <a name="view-box-plots"></a>Vykreslí zobrazení pole

V poznámkovém bloku, posuňte se dolů buňky 20 zobrazíte [pole vykreslení](https://en.wikipedia.org/wiki/Box_plot). Vykreslení pole jsou založeny na statická data, tak k aktualizaci je třeba znovu spustit buňku:

![Vytvoří pole](media/howto-create-custom-analytics/box-plots.png)

Změnit velikost vykreslení v poznámkovém bloku.

## <a name="tidy-up"></a>Pořádek

Po tomto postupy pořádek a vyhnout se zbytečné náklady, odstranit **IoTCentralAnalysis** skupinu prostředků na webu Azure Portal.

Můžete odstranit z aplikace IoT Central **správu** stránky v aplikaci.

## <a name="next-steps"></a>Další postup

V této příručce s postupy jste zjistili, jak:

* Stream telemetrická data z aplikace IoT Central s využitím *nepřetržitý export dat*.
* Vytvoření prostředí Azure Databricks k analýze a zobrazit telemetrická data.

Teď, když víte, jak vytvořit vlastní analytics, navrhované dalším krokem je další postupy [vizualizovat a analyzovat data v řídicím panelu Power BI Azure IoT Central](howto-connect-powerbi.md).
