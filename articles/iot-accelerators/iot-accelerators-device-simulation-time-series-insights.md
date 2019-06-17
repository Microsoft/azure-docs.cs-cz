---
title: Vizualizace telemetrie simulace zařízení pomocí služby Time Series Insights – Azure | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat prostředí Time Series Insights můžete zkoumat a analyzovat telemetrii generovanou akcelerátor řešení simulaci zařízení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5d20adc11e0d679e12fd060e719593a50180db8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65834950"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Vizualizace telemetrická data odesílaná z akcelerátoru řešení simulace zařízení pomocí služby Time Series Insights

Akcelerátor řešení simulace zařízení umožňuje generování telemetrie ze simulovaných zařízení pro testování řešení IoT. Tato příručka ukazuje, jak vizualizovat a analyzovat Simulovaná telemetrická data pomocí prostředí Time Series Insights.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků v této příručce s postupy, potřebujete aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Kroky v této příručce s postupy předpokládají, že akcelerátor řešení simulace zařízení jste nasadili do vašeho předplatného Azure. Pokud jste nenasadili akcelerátor řešení, postupujte podle kroků v [nasazení a spuštění řešení cloudové zařízení simulace](quickstart-device-simulation-deploy.md) rychlý start.

Tento článek předpokládá, že je název akcelerátor řešení **contoso simulace**. Nahraďte **contoso simulace** s názvem jako akcelerátor řešení proveďte následující kroky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Vytvořit skupinu uživatelů

Je potřeba vytvořit vyhrazenou skupinu spotřebitelů ve službě IoT hub do datového proudu telemetrických dat do služby Time Series Insights. Zdroje událostí v Time Series Insights by měl mít výhradní použití skupinu příjemců IoT Hub.

Následující kroky pomocí rozhraní příkazového řádku Azure ve službě Azure Cloud Shell vytvořte skupinu příjemců:

1. IoT hub je jedním z několika prostředků vygenerován při nasazení akcelerátoru řešení simulaci zařízení. Spusťte následující příkaz Najít název služby IoT hub – nezapomeňte použít název akcelerátor řešení:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT hub je prostředek typu **Microsoft.Devices/IotHubs**.

1. Přidat skupinu uživatelů s názvem **devicesimulationtsi** k rozbočovači. V následujícím příkazu použijte název centra a řešení akcelerátoru:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Teď můžete zavřít Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Vytvoření nového prostředí Time Series Insights

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) je plně spravovaná služba analýzy, ukládání a vizualizace pro správu dat časových řad IoT měřítku v cloudu. Chcete-li vytvořit nové prostředí Time Series Insights:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte **vytvořit prostředek** > **Internet of Things** > **Time Series Insights**:

    ![Nové Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Chcete-li vytvořit prostředí Time Series Insights ve stejné skupině prostředků jako akcelerátor řešení, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Na následujícím snímku obrazovky používá název **Contoso-TSI**. Po dokončení tohoto kroku, zvolte svůj vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **simulace contoso**. Použijte název akcelerátor řešení. |
    | Location | Tento příklad používá **USA – východ**. Vytvořte prostředí ve stejné oblasti jako váš akcelerátoru simulace zařízení. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |

    ![Vytvoření služby Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Přidání Time Series Insights prostředí do stejné skupiny prostředků jako akcelerátor řešení znamená, že se odstraní při odstranění akcelerátor řešení.

1. Klikněte na možnost **Vytvořit**. Může trvat několik minut, než prostředí, který se má vytvořit.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí k připojení ke službě IoT hub. Použijte skupinu příjemců, který jste vytvořili v předchozích krocích. Zdroje událostí Time Series Insights vyžaduje vyhrazenou skupinu spotřebitelů není používán jinou službou.

1. Na webu Azure Portal přejděte do nového prostředí Time Series Insights.

1. Na levé straně klikněte na tlačítko **zdroje událostí**:

    ![Zobrazit události zdroje](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na tlačítko **přidat**:

    ![Přidání zdroje událostí](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Pokud chcete nakonfigurovat službu IoT hub jako nový zdroj událostí, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Zdroj událostí název | Na následujícím snímku obrazovky používá název **contoso-iot-hub**. Po dokončení tohoto kroku, použijte vlastní jedinečný název. |
    | source | **IoT Hub** |
    | Možnost importu | **Pomocí služby IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IOT | **contoso-simulation7d894**. Použijte název vašeho centra IoT z akcelerátor řešení simulace zařízení. |
    | Název zásad centra IOT | **iothubowner** |
    | Klíč zásad centra IOT | Toto pole se vyplní automaticky. |
    | Skupina uživatelů centra IOT | **devicesimulationtsi** |
    | Formát serializace události | **JSON** |
    | Název vlastnosti časového razítka | Ponechte prázdné |

    ![Vytvoření zdroje událostí](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na možnost **Vytvořit**.

> [!NOTE]
> Je možné [udělení dodatečného přístupu uživatelům](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) do Průzkumníka služby Time Series Insights.

## <a name="start-a-simulation"></a>Spustit simulaci

Než použijete Průzkumníka služby Time Series Insights, nakonfigurujte akcelerátor řešení simulace zařízení k vygenerování nějaké telemetrie. Následující snímek obrazovky ukazuje spuštění simulace s 10 chladič zařízení:

![Spuštění simulace zařízení](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

V Průzkumníku Time Series Insights je webová aplikace, které můžete použít k vizualizaci telemetrických dat.

1. Na webu Azure Portal, vyberte Time Series Insights **přehled** kartu.

1. Webové aplikace Průzkumníka Time Series Insights, klikněte na tlačítko **přejít na prostředí**:

    ![Průzkumník Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Panel výběru času vyberte **posledních 30 minut** z rychlé časy nabídky a klikněte na tlačítko **hledání**:

    ![Čas hledání v Průzkumníkovi Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. V panelu podmínky na levé straně vyberte **teploty** jako **míru** a **iothub připojení zařízení id** jako **rozdělit podle** hodnotu:

    ![Čas Series Insights Průzkumník dotazů](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Klikněte pravým tlačítkem na graf a vyberte **zkoumat události**:

    ![Čas události explorer Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Data události se zobrazí v tabulce:

    ![Tabulka explorer doby Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klikněte na tlačítko perspektivy zobrazení:

    ![Čas Series Insights explorer perspektivy](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klikněte na tlačítko **+** přidat nový dotaz do perspektivy:

    ![Průzkumník Time Series Insights přidat dotaz](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Vyberte **posledních 30 minut** jako časové období **vlhkosti** jako **míru**, a **iothub připojení zařízení id** jako **Rozdělit podle** hodnotu:

    ![Čas Series Insights Průzkumník dotazů](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kliknutím na tlačítko zobrazení perspektivy zobrazení řídicího panelu telemetrie zařízení:

    ![Řídicí panel explorer čas Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud budete chtít dále zkoumat, ponechte akcelerátor řešení nasazení.

Pokud akcelerátor řešení už nepotřebujete, odstraňte ho z [zřídili řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) stránky, že ji vyberete a pak kliknutím na **odstranit řešení**.

Pokud jste přidali do skupiny prostředků akcelerátor řešení prostředí Time Series Insights, je automaticky odstraní při odstranění akcelerátor řešení. V opačném případě bude zapotřebí ručně odebrat prostředí Time Series Insights na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zkoumat a dotazování dat v Průzkumníku Time Series Insights najdete v tématu [Průzkumníka služby Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
