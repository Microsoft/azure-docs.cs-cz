---
title: Vizualizace vzdálené monitorování dat pomocí Azure časové řady Insights | Microsoft Docs
description: Zjistěte, jak ke konfiguraci prostředí časové řady Statistika umožní zkoumat a analyzovat data řady čas vašeho řešení vzdáleného monitorování.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 7a0a5d4f1fbba5d7bd2813e8b9c300a37853e06c
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111472"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Vizualizaci vzdálené monitorování dat se statistickými časové řady

Operátor chtít dále rozšířit mimo pole dat, vizualizace poskytované vzdálené monitorování předem nakonfigurujte řešení. Naše solution accelerator poskytuje mimo pole integraci s TSI. V tento postup se dozvíte, jak nakonfigurovat časové řady Statistika k analýze zařízení telemetrie a zjišťovat anomálie.

## <a name="prerequisites"></a>Požadavky

Abyste mohli dokončit tento postup, budete potřebovat následující:

* [Nasazení řešení vzdáleného monitorování předkonfigurované](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Vytvořte skupinu uživatelů

Musíte vytvořit vyhrazenou skupinu spotřebitelů ve službě IoT hub, který se má použít pro streamování dat ke statistice časové řady.

> [!NOTE]
> Skupiny uživatelů slouží aplikace k načítání dat z Azure IoT Hub. Každé skupině příjemců umožňuje až pět příjemci výstup. Pro každých pět Výstup jímky a můžete vytvořit až 32 skupiny příjemců, měli vytvořit nové skupiny příjemců.

1. Na portálu Azure klikněte na tlačítko cloudové prostředí.

1. Spusťte následující příkaz k vytvoření nové skupiny příjemců:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Vytvoření nového prostředí Statistika časové řady

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která umožňuje správu dat časových řad v IoT měřítku v cloudu. Poskytuje vysoce škálovatelné úložiště dat časových řad a umožňuje prozkoumávat a analyzovat miliardy událostí přicházejících z celého světa během několika sekund. Použijte časové řady Insights k uložení a správě terabajtů data časové řady, prozkoumat a vizualizovat až miliardy události současně provést analýzu hlavní příčiny a k porovnání více sítí a prostředky.

1. Přihlaste se k [portálu Azure](http://portal.azure.com/).

1. Vyberte **vytvořit prostředek** > **Internet věcí** > **časové řady Insights**.

    ![Nový pohled na řady čas](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. K vytvoření prostředí časové řady Insights, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Následující snímek obrazovky používá název **contorosrmtsi**. Po dokončení tohoto kroku, zvolte svůj vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **Vytvořit nový**. Se používá název **ContosoRM**. |
    | Umístění | Používáme **východní USA**. Vytvoření prostředí ve stejné oblasti jako vaše řešení vzdáleného monitorování. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |
    | Připnutí na řídicí panel | **Ano** |

    ![Vytvářet přehledy, časové řady](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Klikněte na možnost **Vytvořit**. Může trvat chvíli prostředí, který se má vytvořit.

## <a name="create-event-source"></a>Vytvořit zdroj události

Vytvořte nový zdroj událostí k připojení do služby IoT hub. Ujistěte se, že používáte skupinu příjemců vytvořili v předchozích krocích. Statistika časové řady vyžaduje každá služba má vyhrazenou skupinu spotřebitelů není používán jinou službou.

1. Přejděte do nové prostředí časové řady.

1. Na levé straně vyberte **zdroje událostí**.

    ![Zobrazení zdroje událostí](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na tlačítko **Add** (Přidat).

    ![Přidání zdroje událostí](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Konfigurace služby IoT hub jako nový zdroj událostí, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zdroj události název | Následující snímek obrazovky používá název **contosorm-iot-hub**. Po dokončení tohoto kroku, použijte svůj vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Pomocí služby IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IOT | **contosorma57a6**. Použijte název služby IoT hub z řešení vzdáleného monitorování. |
    | Název zásady centra IOT | **iothubowner** zkontrolujte zásady používané zásadu vlastníka. |
    | Klíč zásad centra IOT | Toto pole se vyplní automaticky. |
    | Skupina uživatelů centra IOT | **timeseriesinsights** |
    | Formát serializace události | **JSON**     | Název vlastnosti časové razítko | Ponechte prázdné |

    ![Vytvořit zdroj události](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na možnost **Vytvořit**.

> [!NOTE]
> Pokud potřebujete další uživatelům uděluje přístup do Průzkumníka časové řady statistiky, můžete použít následující postup [udělit přístup k datům](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

Statistika časové řady explorer je webovou aplikaci, která vám pomůže vytvořit vizualizacemi vašich dat.

1. Vyberte **přehled** kartě.

1. Klikněte na tlačítko **přejděte do prostředí**, které se otevře časové řady Statistika explorer webové aplikace.

    ![Průzkumník Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. V panelu Výběr čas vyberte **posledních 12 hodin** z rychlé doby nabídky a klikněte na tlačítko **vyhledávání**.

    ![Hledání Explorer Statistika časové řady](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. V panelu podmínky na levé straně vyberte hodnotu měr **teploty** a rozdělení podle hodnota **iothub připojení zařízení id**.

    ![Časové řady Statistika Průzkumníka dotazů](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Klikněte pravým tlačítkem na graf a vyberte **prozkoumat události**.

    ![Časové řady Statistika Explorer události](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Události jsou vykreslovány v mřížce v tabulkovém formátu.

    ![Čas řady Statistika Explorer tabulky](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Klikněte na tlačítko zobrazení perspektivy.

    ![Časové řady Statistika Explorer perspektivy](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klikněte na tlačítko **přidat** vytvořit nový dotaz v perspektivě.

    ![Časové řady Statistika Explorer přidat dotazu](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Vyberte čas rychlé **posledních 12 hodin**, míru **vlhkosti** a rozdělení z **iothub připojení zařízení id**.

    ![Časové řady Statistika Průzkumníka dotazů](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Klikněte na tlačítko zobrazení perspektivy zobrazení řídicího panelu metrik zařízení.

    ![Řady čas, přehledný řídicí panel aplikace Explorer](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Další kroky

Informace o tom, jak prozkoumat a dotazování dat v Průzkumníku časové řady statistiky najdete v tématu [Statistika řady čas Azure explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
