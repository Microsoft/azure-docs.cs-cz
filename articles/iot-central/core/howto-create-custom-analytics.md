---
title: Rozšiřování Azure IoT Central s využitím vlastních analýz | Microsoft Docs
description: Jako vývojář řešení můžete nakonfigurovat aplikaci IoT Central, aby vlastní analýzy a vizualizace. Toto řešení používá Azure Databricks.
author: TheRealJasonAndrew
ms.author: v-anjaso
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 3132ec8fb3cb123653887d92a2f33788f40564c0
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105033819"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Rozšiřování Azure IoT Central s využitím vlastních analýz pomocí Azure Databricks

V této příručce se dozvíte, jak pomocí vývojářů řešení rozšíříte svou IoT Centralovou aplikaci s využitím vlastních analýz a vizualizací. V tomto příkladu se používá pracovní prostor [Azure Databricks](/azure/azure-databricks/) k analýze IoT Centralho datového proudu telemetrie a k vygenerování vizualizací, jako je například [krabicový graf](https://wikipedia.org/wiki/Box_plot).  

V této příručce se dozvíte, jak můžete IoT Central nad rámec toho, co už s [integrovanými analytickými nástroji](./howto-create-custom-analytics.md)udělat.

V této příručce se dozvíte, jak:

* Pomocí *průběžného exportu dat* Streamujte telemetrii z IoT Central aplikace.
* Vytvořte prostředí Azure Databricks pro analýzu a vykreslení telemetrie zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto průvodci, potřebujete aktivní předplatné Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="iot-central-application"></a>IoT Central aplikace

Na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) vytvořte aplikaci IoT Central s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Cenový tarif | Standard |
| Šablona aplikace | Analýzy v obchodě – monitorování podmínek |
| Název aplikace | Přijměte výchozí nebo vyberte svůj vlastní název. |
| URL | Přijměte výchozí nebo vyberte vlastní jedinečnou předponu adresy URL. |
| Adresář | Váš tenant Azure Active Directory |
| Předplatné Azure | Vaše předplatné Azure |
| Oblast | Vaše nejbližší oblast |

Příklady a snímky obrazovky v tomto článku používají oblast **USA** . Vyberte umístění, které chcete zavřít, a ujistěte se, že jste vytvořili všechny prostředky ve stejné oblasti.

Tato šablona aplikace zahrnuje dvě simulovaná termostata zařízení, která odesílají telemetrii.

### <a name="resource-group"></a>Skupina prostředků

Pomocí [Azure Portal vytvořte skupinu prostředků](https://portal.azure.com/#create/Microsoft.ResourceGroup) s názvem **IoTCentralAnalysis** , která bude obsahovat další prostředky, které vytvoříte. Vytvořte prostředky Azure ve stejném umístění jako vaše aplikace IoT Central.

### <a name="event-hubs-namespace"></a>Obor názvů služby Event Hubs

Pomocí [Azure Portal vytvořte obor názvů Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název    | Zvolit název oboru názvů |
| Cenová úroveň | Basic |
| Předplatné | Vaše předplatné |
| Skupina prostředků | IoTCentralAnalysis |
| Umístění | USA – východ |
| Jednotky propustnosti | 1 |

### <a name="azure-databricks-workspace"></a>Pracovní prostor Azure Databricks

Pomocí [Azure Portal vytvořte službu Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) s následujícím nastavením:

| Nastavení | Hodnota |
| ------- | ----- |
| Název pracovního prostoru    | Volba názvu pracovního prostoru |
| Předplatné | Vaše předplatné |
| Skupina prostředků | IoTCentralAnalysis |
| Umístění | USA – východ |
| Cenová úroveň | Standard |

Po vytvoření požadovaných prostředků vypadá vaše skupina prostředků **IoTCentralAnalysis** jako na následujícím snímku obrazovky:

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="Obrázek skupiny prostředků analýzy IoT Central":::

## <a name="create-an-event-hub"></a>Vytvoření centra událostí

IoT Central aplikaci můžete nakonfigurovat tak, aby průběžně exportovali telemetrii do centra událostí. V této části vytvoříte centrum událostí pro příjem telemetrie z vaší aplikace IoT Central. Centrum událostí doručí telemetrii do vaší Stream Analytics úlohy ke zpracování.

1. V Azure Portal přejděte na obor názvů Event Hubs a vyberte **+ centrum událostí**.
1. Pojmenujte centrum událostí **centralexport**.
1. V seznamu Center událostí v oboru názvů vyberte **centralexport**. Pak zvolte **zásady sdíleného přístupu**.
1. Vyberte **+ Přidat**. Vytvořte zásadu s názvem **SendListen** s deklaracemi pro **odesílání** a **naslouchání** .
1. Když je zásada připravená, vyberte ji v seznamu a potom zkopírujte hodnotu **připojovací řetězec – primární klíč** .
1. Poznamenejte si tento připojovací řetězec, budete ho později používat při konfiguraci poznámkového bloku datacihly pro čtení z centra událostí.

Váš Event Hubs obor názvů vypadá jako na následujícím snímku obrazovky:

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="Obrázek Event Hubs oboru názvů":::

## <a name="configure-export-in-iot-central"></a>Konfigurace exportu v IoT Central

V této části nakonfigurujete aplikaci pro streamování telemetrie z simulovaných zařízení do centra událostí.

Na webu [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) přejděte do aplikace IoT Central, kterou jste předtím vytvořili. Pokud chcete nakonfigurovat export, nejdřív vytvořte cíl:

1. Přejděte na stránku pro **Export dat** a pak vyberte **cílová umístění**.
1. Vyberte **+ Nový cíl**.
1. K vytvoření cíle použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ----- | ----- |
    | Název cíle | Centrum událostí telemetrie |
    | Cílový typ | Azure Event Hubs |
    | Připojovací řetězec | Připojovací řetězec centra událostí, který jste si poznamenali dříve |

    **Centrum událostí** se zobrazí jako **centralexport**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="Snímek obrazovky znázorňující cíl exportu dat":::

1. Vyberte **Uložit**.

Vytvoření definice exportu:

1. Přejděte na stránku pro **Export dat** a vyberte **+ Nový export**.

1. Pro konfiguraci exportu použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název exportu | Export centra událostí |
    | Povoleno | Zapnout |
    | Typ dat k exportu | Telemetrie |
    | Cíle | Vyberte **+ cíl** a pak vyberte **centrum událostí telemetrie** . |

1. Vyberte **Uložit**.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="Snímek obrazovky znázorňující definici exportu dat":::

Než budete pokračovat, počkejte, než bude stav exportu v **pořádku** na stránce pro **Export dat** .

## <a name="configure-databricks-workspace"></a>Konfigurace pracovního prostoru datacihly

V Azure Portal přejděte do služby Azure Databricks a vyberte **Spustit pracovní prostor**. V prohlížeči se otevře nová karta a přihlásí vás k pracovnímu prostoru.

### <a name="create-a-cluster"></a>Vytvoření clusteru

Na stránce **Azure Databricks** v seznamu běžných úloh vyberte **nový cluster**.

K vytvoření clusteru použijte informace v následující tabulce:

| Nastavení | Hodnota |
| ------- | ----- |
| Název clusteru | centralanalysis |
| Režim clusteru | Standard |
| Verze Databricks Runtime | 5,5 LTS (Scala 2,11, Spark 2.4.5) |
| Verze Pythonu | 3 |
| Povolit automatické škálování | No |
| Ukončit po minutách nečinnosti | 30 |
| Typ pracovního procesu | Standard_DS3_v2 |
| Pracovníků | 1 |
| Typ ovladače | Stejné jako pracovní proces |

Vytvoření clusteru může trvat několik minut, než budete pokračovat, počkejte na dokončení vytváření clusteru.

### <a name="install-libraries"></a>Instalovat knihovny

Na stránce **clustery** počkejte, dokud nebude **spuštěn** stav clusteru.

Následující kroky ukazují, jak importovat knihovnu, kterou vaše ukázka potřebuje, do clusteru:

1. Na stránce **clustery** počkejte, než se **spustí** stav interaktivního clusteru **centralanalysis** .

1. Vyberte cluster a pak zvolte kartu **knihovny** .

1. Na kartě **knihovny** klikněte na možnost **instalovat nové**.

1. Na stránce **instalovat knihovnu** vyberte jako zdroj knihovny možnost **Maven** .

1. Do textového pole **souřadnice** zadejte následující hodnotu: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Kliknutím na **nainstalovat** nainstalujte knihovnu na cluster.

1. Stav knihovny je nyní **nainstalován**:

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="Snímek obrazovky instalované knihovny":::

### <a name="import-a-databricks-notebook"></a>Import poznámkového bloku datacihly

Pomocí následujících kroků importujte Poznámkový blok datacihly, který obsahuje kód Pythonu pro analýzu a vizualizaci IoT Central telemetrie:

1. V prostředí datacihly přejděte na stránku **pracovní prostor** . Vyberte rozevírací seznam vedle názvu účtu a pak zvolte **importovat**.

1. Vyberte Import z adresy URL a zadejte následující adresu: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Chcete-li importovat Poznámkový blok, klikněte na tlačítko **importovat**.

1. Vyberte **pracovní prostor** , ve kterém chcete zobrazit importovaný Poznámkový blok:

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="Snímek importovaného poznámkového bloku":::

5. Upravte kód v první buňce Pythonu přidáním připojovacího řetězce Event Hubs, který jste předtím uložili:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Analýza spuštění

Ke spuštění analýzy musíte ke clusteru připojit Poznámkový blok:

1. Vyberte možnost **Odpojeno** a pak vyberte cluster **centralanalysis** .
1. Pokud cluster neběží, spusťte ho.
1. Chcete-li spustit Poznámkový blok, vyberte tlačítko Spustit.

V poslední buňce se může zobrazit chyba. Pokud ano, zkontrolujte, jestli jsou předchozí buňky spuštěné, počkejte minutu, než se data zapisují do úložiště, a pak znovu spusťte poslední buňku.

### <a name="view-smoothed-data"></a>Zobrazit hladká data

V poznámkovém bloku přejděte dolů na buňku 14, aby se zobrazilo vykreslení průměrného vlhkosti podle typu zařízení. Tento graf se průběžně aktualizuje, protože telemetrie streamování dorazí:

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="Snímek obrazovky s vyhlazeným vykreslením telemetrie":::

Můžete změnit velikost grafu v poznámkovém bloku.

### <a name="view-box-plots"></a>Zobrazit pole

V poznámkovém bloku přejděte dolů na buňku 20 a podívejte se, že je [pole](https://en.wikipedia.org/wiki/Box_plot)zobrazeno. Pole se zobrazuje na základě statických dat, aby je bylo možné aktualizovat, je nutné znovu spustit buňku:

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="Snímek obrazovky box":::

Můžete změnit velikost pozemků v poznámkovém bloku.

## <a name="tidy-up"></a>Uklizený nahoru

Pokud se chcete uklizený po tomto postupu a vyhnout se zbytečným nákladům, odstraňte skupinu prostředků **IoTCentralAnalysis** v Azure Portal.

Aplikaci IoT Central můžete odstranit ze stránky **pro správu** v rámci aplikace.

## <a name="next-steps"></a>Další kroky

V této příručce se naučíte:

* Pomocí *průběžného exportu dat* Streamujte telemetrii z IoT Central aplikace.
* Vytvořte prostředí Azure Databricks pro analýzu a vykreslení dat telemetrie.

Teď, když víte, jak vytvářet vlastní analýzy, je navržený další krok, kde se dozvíte, jak [vizualizovat a analyzovat data Azure IoT Central v řídicím panelu Power BI](howto-connect-powerbi.md).
