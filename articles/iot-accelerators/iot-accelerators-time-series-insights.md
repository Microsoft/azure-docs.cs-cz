---
title: Vizualizujte vzdálené monitorování dat pomocí služby Azure Time Series Insights | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat prostředí Time Series Insights pro zkoumání a analýzy dat časových řad řešení vzdáleného monitorování.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: f16fdfca704b8f8cb175de637ad7f3ef143d3ed7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968951"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>Vizualizace vzdáleného monitorování pomocí služby Time Series Insights

Operátor chtít rozšířit mimo pole dat vizualizaci pomocí vzdáleného monitorování konfigurace řešení. Naše solution accelerator poskytuje mimo okamžité integrace s TSI. V tomto návodu se dozvíte, jak nakonfigurovat služby Time Series Insights k analýze telemetrie zařízení a detekci anomálií.

## <a name="prerequisites"></a>Požadavky

Pokud chcete dokončit tento návod, budete potřebovat následující:

* [Nasazení řešení vzdáleného monitorování předkonfigurované](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Vytvořit skupinu uživatelů

Je potřeba vytvořit vyhrazenou skupinu spotřebitelů ve službě IoT hub má být použit pro streamování dat do služby Time Series Insights.

> [!NOTE]
> Skupiny uživatelů se aplikace používají k načítání dat ze služby Azure IoT Hub. Každé skupině příjemců umožní až pěti příjemců výstup. Pro každých pět výstupní jímky a můžete vytvořit až 32 skupin konzumentů, by měl vytvořit novou skupinu uživatelů.

1. Na webu Azure Portal klikněte na tlačítko Cloud Shell.

1. Spusťte následující příkaz k vytvoření nové skupiny příjemců:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>Vytvoření nového prostředí Time Series Insights

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která umožňuje správu dat časových řad v IoT měřítku v cloudu. Poskytuje vysoce škálovatelné úložiště dat časových řad a umožňuje prozkoumávat a analyzovat miliardy událostí přicházejících z celého světa během několika sekund. Pomocí služby Time Series Insights ukládat a spravovat terabajty dat časových řad, prozkoumat a vizualizovat miliardy událostí současně, provádět analýzy původních příčin a porovnávat několik webů a prostředků.

1. Přihlaste se k [portálu Azure](http://portal.azure.com/).

1. Vyberte **vytvořit prostředek** > **Internet of Things** > **Time Series Insights**.

    ![Nové Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. Pokud chcete vytvořit prostředí Time Series Insights, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Na následujícím snímku obrazovky používá název **contorosrmtsi**. Po dokončení tohoto kroku, zvolte svůj vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **Vytvořit nový**. Používáme název **ContosoRM**. |
    | Umístění | Používáme **USA – východ**. Vytvořte prostředí ve stejné oblasti jako vaše řešení vzdálené monitorování. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |
    | Připnutí na řídicí panel | **Ano** |

    ![Vytvoření služby Time Series Insights](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. Klikněte na možnost **Vytvořit**. Může trvat chvíli, než se prostředí, který se má vytvořit.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí k připojení ke službě IoT hub. Ujistěte se, že použijete se skupina uživatelů vytvořené v předchozích krocích. Time Series Insights vyžaduje každá služba má vyhrazenou skupinu spotřebitelů není používán jinou službou.

1. Přejděte do nového prostředí Time Series Insights.

1. Na levé straně vyberte **zdroje událostí**.

    ![Zobrazit události zdroje](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na tlačítko **Add** (Přidat).

    ![Přidání zdroje událostí](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. Pokud chcete nakonfigurovat službu IoT hub jako nový zdroj událostí, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zdroj událostí název | Na následujícím snímku obrazovky používá název **contosorm-iot-hub**. Po dokončení tohoto kroku, použijte vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Pomocí služby IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IOT | **contosorma57a6**. Použijte název vašeho centra IoT z vašeho řešení vzdáleného monitorování. |
    | Název zásad centra IOT | **iothubowner** zkontrolujte zásady používané zásady vlastníka. |
    | Klíč zásad centra IOT | Toto pole se vyplní automaticky. |
    | Skupina uživatelů centra IOT | **timeseriesinsights** |
    | Formát serializace události | **JSON**     | Název vlastnosti časového razítka | Ponechte prázdné |

    ![Vytvoření zdroje událostí](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na možnost **Vytvořit**.

> [!NOTE]
> Pokud potřebujete další uživatelům udělit přístup k Průzkumníka služby Time Series Insights, můžete použít následující postup [udělení přístupu k datům](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access).

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

V Průzkumníku Time Series Insights je webová aplikace, která vám pomůže vytvořit vizualizacemi svých dat.

1. Vyberte **přehled** kartu.

1. Klikněte na tlačítko **přejít do prostředí**, otevře se webová aplikace Průzkumníka Time Series Insights.

    ![Průzkumník Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. Panel výběru času vyberte **posledních 12 hodin** z rychlé časy nabídky a klikněte na tlačítko **hledání**.

    ![Hledání v Průzkumníkovi Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. V panelu podmínky na levé straně vyberte hodnotu míry **teploty** a rozdělit podle hodnotu **iothub připojení zařízení id**.

    ![Dotaz Průzkumníka Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. Klikněte pravým tlačítkem na graf a vyberte **zkoumat události**.

    ![Čas Series Insights Explorer události](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. Události se zobrazují v mřížce ve formátu tabulky.

    ![Čas řad Insights Průzkumníka](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. Klikněte na tlačítko pro zobrazení perspektivy.

    ![Čas Series Insights Explorer perspektivy](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klikněte na tlačítko **přidat** vytvořit nový dotaz v perspektivě.

    ![Průzkumník Time Series Insights přidat dotaz](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. Vyberte čas rychlé **posledních 12 hodin**, míra **vlhkosti** a rozdělení z **iothub připojení zařízení id**.

    ![Dotaz Průzkumníka Time Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. Kliknutím na tlačítko zobrazení perspektivy zobrazení řídicího panelu zařízení metrik.

    ![Řídicí panel Průzkumníka čas Series Insights](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zkoumat a dotazování dat v Průzkumníku Time Series Insights najdete v tématu [Průzkumníka služby Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
