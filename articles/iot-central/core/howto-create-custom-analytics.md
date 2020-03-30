---
title: Rozšíření Azure IoT Central pomocí vlastníanalýzy | Dokumenty společnosti Microsoft
description: Jako vývojář řešení nakonfigurujte aplikaci IoT Central tak, aby vytvářela vlastní analýzy a vizualizace. Toto řešení používá Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 7c2c14a937b4ef55d0e5f71e7b20214428ecd68c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158193"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Rozšiřte Azure IoT Central o vlastní analýzy pomocí Azure Databricks

Tento návod vám jako vývojáři řešení ukáže, jak rozšířit aplikaci IoT Central o vlastní analýzy a vizualizace. Příklad používá pracovní prostor [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) k analýze datového proudu telemetrie IoT Central a ke generování vizualizací, jako jsou [obrázky rámečku](https://wikipedia.org/wiki/Box_plot).

Tento návod vám ukáže, jak rozšířit IoT Central nad rámec toho, co už může dělat s [integrovanými analytickými nástroji](./howto-create-custom-analytics.md).

V tomto návodu se dozvíte, jak:

* Streamtele telemetrii z aplikace IoT Central pomocí *průběžného exportu dat*.
* Vytvořte prostředí Azure Databricks pro analýzu a vykreslení telemetrie zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto návodu, budete potřebovat aktivní předplatné Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

### <a name="iot-central-application"></a>Aplikace IoT Central

Vytvořte aplikaci IoT Central na webu [správce aplikací Azure IoT Central](https://aka.ms/iotcentral) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Cenový plán | Standard |
| Šablona aplikace | Analýza v obchodě – monitorování stavu |
| Název aplikace | Přijmout výchozí nastavení nebo zvolit vlastní jméno |
| zprostředkovatele identity | Přijmout výchozí hodnotu nebo zvolit vlastní jedinečnou předponu URL |
| Adresář | Váš klient Služby Azure Active Directory |
| Předplatné Azure | Vaše předplatné Azure |
| Region (Oblast) | Vaše nejbližší oblast |

Příklady a snímky obrazovky v tomto článku používají oblast **Spojených států.** Vyberte umístění blízko vás a ujistěte se, že vytvoříte všechny prostředky ve stejné oblasti.

Tato šablona aplikace obsahuje dvě simulovaná termostatová zařízení, která odesílají telemetrii.

### <a name="resource-group"></a>Skupina prostředků

Pomocí [portálu Azure vytvořte skupinu prostředků](https://portal.azure.com/#create/Microsoft.ResourceGroup) s názvem **IoTCentralAnalysis,** která bude obsahovat další prostředky, které vytvoříte. Vytvořte prostředky Azure ve stejném umístění jako vaše aplikace IoT Central.

### <a name="event-hubs-namespace"></a>Obor názvů služby Event Hubs

Na [webu Azure Portal můžete vytvořit obor názvů Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Name (Název)    | Zvolte název oboru názvů |
| Cenová úroveň | Basic |
| Předplatné | Vaše předplatné |
| Skupina prostředků | IoTCentralAnalysis |
| Umístění | USA – východ |
| Jednotky propustnosti | 1 |

### <a name="azure-databricks-workspace"></a>Pracovní prostor Azure Databricks

Pomocí [portálu Azure vytvořte službu Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název pracovního prostoru    | Výběr názvu pracovního prostoru |
| Předplatné | Vaše předplatné |
| Skupina prostředků | IoTCentralAnalysis |
| Umístění | USA – východ |
| Cenová úroveň | Standard |

Po vytvoření požadovaných prostředků vypadá vaše skupina prostředků **IoTCentralAnalysis** jako následující snímek obrazovky:

![Skupina prostředků analýzy IoT Central](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

Můžete nakonfigurovat aplikaci IoT Central tak, aby průběžně exportovala telemetrii do centra událostí. V této části vytvoříte centrum událostí pro příjem telemetrie z aplikace IoT Central. Centrum událostí doručuje telemetrická data do úlohy Stream Analytics ke zpracování.

1. Na webu Azure Portal přejděte do oboru názvů Centra událostí a vyberte **+ Event Hub**.
1. Pojmenujte centrum událostí **centralexportace**a vyberte **Vytvořit**.
1. V seznamu rozbočovačů událostí v oboru názvů vyberte **centralexport**. Pak zvolte **Zásady sdíleného přístupu**.
1. Vyberte **+ Přidat**. Vytvořte zásadu s názvem **Naslouchání** s deklarací **Listen.**
1. Až bude zásada připravená, vyberte ji v seznamu a zkopírujte hodnotu **primárního klíče připojovacího řetězce.**
1. Poznamenejte si tento připojovací řetězec, můžete jej použít později při konfiguraci poznámkového bloku Databricks pro čtení z centra událostí.

Obor názvů Event Hubs vypadá jako následující snímek obrazovky:

![Obor názvů služby Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Konfigurace exportu v centru IoT

Na webu [správce aplikací Azure IoT Central](https://aka.ms/iotcentral) přejděte na aplikaci IoT Central, kterou jste vytvořili ze šablony Contoso. V této části nakonfigurujete aplikaci pro streamování telemetrie ze simulovaných zařízení do centra událostí. Postup konfigurace exportu:

1. Přejděte na stránku **Export dat,** vyberte **+ Nový**a potom na Azure **Event Hubs**.
1. Ke konfiguraci exportu použijte následující nastavení a pak vyberte **Uložit**:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zobrazovaný název | Export do centra událostí |
    | Povoleno | Zapnuto |
    | Obor názvů služby Event Hubs | Název oboru názvů Event Hubs |
    | Centrum událostí | centralexport |
    | Měření | Zapnuto |
    | Zařízení | Vypnuto |
    | Šablony zařízení | Vypnuto |

![Konfigurace exportu dat](media/howto-create-custom-analytics/cde-configuration.png)

Než budete pokračovat, počkejte, dokud nebude **spuštěn** stav exportu.

## <a name="configure-databricks-workspace"></a>Konfigurace pracovního prostoru Databricks

Na webu Azure Portal přejděte do služby Azure Databricks a vyberte **Spustit pracovní prostor**. V prohlížeči se otevře nová karta a přihlásí vás k pracovnímu prostoru.

### <a name="create-a-cluster"></a>Vytvoření clusteru

Na stránce **Azure Databricks** vyberte v seznamu běžných úloh **nový cluster**.

K vytvoření clusteru použijte informace v následující tabulce:

| Nastavení | Hodnota |
| ------- | ----- |
| Název clusteru | centrální analýza |
| Režim clusteru | Standard |
| Datová bricks runtime verze | 5.5 LTS (Scala 2.11, Jiskra 2.4.3) |
| Verze Pythonu | 3 |
| Povolit automatické škálování | Ne |
| Ukončit po minutách nečinnosti | 30 |
| Typ pracovníka | Standard_DS3_v2 |
| Pracovníků | 1 |
| Typ ovladače | Stejné jako pracovník |

Vytvoření clusteru může trvat několik minut, počkejte na dokončení vytvoření clusteru, než budete pokračovat.

### <a name="install-libraries"></a>Instalace knihoven

Na stránce **Clustery** počkejte, dokud nebude spuštěn stav **clusteru**.

Následující kroky ukazují, jak importovat knihovnu, kterou ukázka potřebuje, do clusteru:

1. Na stránce **Clustery** počkejte, dokud nebude spuštěn stav interaktivního **clusteru** **centralanalysis** .

1. Vyberte cluster a pak zvolte kartu **Knihovny.**

1. Na kartě **Knihovny** zvolte **Instalovat nové**.

1. Na stránce **Instalovat knihovnu** zvolte **Maven** jako zdroj knihovny.

1. Do textového pole **Souřadnice** zadejte následující hodnotu:`com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Zvolte **Instalovat,** chcete-li nainstalovat knihovnu do clusteru.

1. Nyní je nainstalován stav **knihovny**:

    ![Nainstalovaná knihovna](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Import poznámkového bloku Databricks

Pomocí následujících kroků importujte poznámkový blok Databricks, který obsahuje kód Pythonu k analýze a vizualizaci telemetrie IoT Central:

1. Přejděte na stránku **Pracovní prostor** v prostředí Databricks. Vyberte rozevírací okno vedle názvu účtu a pak zvolte **Importovat**.

1. Zvolte import z adresy URL a zadejte následující adresu:[https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Chcete-li poznámkový blok importovat, zvolte **Importovat**.

1. Vyberte **pracovní prostor,** chcete-li importovaný poznámkový blok zobrazit:

    ![Importovaný poznámkový blok](media/howto-create-custom-analytics/import-notebook.png)

1. Upravte kód v první buňce Pythonu a přidejte připojovací řetězec Event Hubs, který jste uložili dříve:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Spustit analýzu

Chcete-li spustit analýzu, musíte připojit poznámkový blok ke clusteru:

1. Vyberte **Odpojeno** a pak vyberte **cluster centrální analýzy.**
1. Pokud cluster není spuštěn, spusťte jej.
1. Chcete-li poznámkový blok spustit, vyberte tlačítko spustit.

V poslední buňce se může zobrazit chyba. Pokud ano, zkontrolujte, zda jsou spuštěny předchozí buňky, počkejte minutu na zápis některých dat do úložiště a potom znovu spusťte poslední buňku.

### <a name="view-smoothed-data"></a>Zobrazení vyhlazených dat

V poznámkovém bloku přejděte dolů do buňky 14 a podívejte se na obrázek klouzavé průměrné vlhkosti podle typu zařízení. Tento graf se průběžně aktualizuje při příchodu telemetrie streamování:

![Vyhlazený graf telemetrie](media/howto-create-custom-analytics/telemetry-plot.png)

Velikost grafu v poznámkovém bloku můžete změnit.

### <a name="view-box-plots"></a>Obrázky pole zobrazení

V poznámkovém bloku přejděte dolů do buňky 20 a zodvaáše [obrázky rámečku](https://en.wikipedia.org/wiki/Box_plot). Obrázky pole jsou založeny na statických datech, takže pro jejich aktualizaci je nutné znovu spustit buňku:

![Obrázky rámečků](media/howto-create-custom-analytics/box-plots.png)

Velikost parcel v poznámkovém bloku můžete změnit.

## <a name="tidy-up"></a>Uklidit

Chcete-li po tomto návodu uklidit a vyhnout se zbytečným nákladům, odstraňte skupinu prostředků **IoTCentralAnalysis** na webu Azure Portal.

Aplikaci IoT Central můžete odstranit ze stránky **Správa** v rámci aplikace.

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se naučili:

* Streamtele telemetrii z aplikace IoT Central pomocí *průběžného exportu dat*.
* Vytvořte prostředí Azure Databricks pro analýzu a vykreslení telemetrických dat.

Teď, když víte, jak vytvořit vlastní analýzy, je navrhovaným dalším krokem naučit se [spravovat aplikaci](howto-administer.md).
