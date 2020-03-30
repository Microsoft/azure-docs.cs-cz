---
title: Vizualizujte simulovanou telemetrii pomocí přehledů time series – Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat prostředí Time Series Insights tak, aby prozkoumalo a analyzovalo telemetrii generovanou akcelerátorem řešení Simulace zařízení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 2bbd7911a40d6a256d478e2533ad2469b8fd6973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889349"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Použití přehledů časových řad k vizualizaci telemetrie odeslané z akcelerátoru řešení Simulace zařízení

Akcelerátor řešení Simulace zařízení umožňuje generovat telemetrii ze simulovaných zařízení pro testování řešení IoT. Tento návod vám ukáže, jak vizualizovat a analyzovat simulované telemetrie pomocí prostředí Time Series Insights.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle pokynů v tomto návodu, potřebujete aktivní předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

Kroky v tomto návodu předpokládají, že jste do předplatného Azure nasadili akcelerátor řešení Simulace zařízení. Pokud jste nenasadili akcelerátor řešení, postupujte podle pokynů v [nasadit a spustit cloudové zařízení simulační řešení](quickstart-device-simulation-deploy.md) rychlý start.

Tento článek předpokládá, že název akcelerátoru řešení je **contoso-simulation**. Při dokončení následujících kroků nahraďte **contoso-simulaci** názvem akcelerátoru řešení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Vytvoření skupiny spotřebitelů

Musíte vytvořit vyhrazenou skupinu spotřebitelů ve vašem centru IoT pro streamování telemetrie do Time Series Insights. Zdroj událostí v Time Series Insights by měl mít výhradní použití skupiny spotřebitelů služby IoT Hub.

Následující kroky používají azure cli v prostředí Azure Cloud Shell k vytvoření skupiny spotřebitelů:

1. Centrum IoT je jedním z několika prostředků vytvořených při nasazení akcelerátoru řešení Simulace zařízení. Proveďte následující příkaz najít název vašeho centra IoT – nezapomeňte použít název akcelerátoru řešení:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Centrum IoT je zdrojem typu **Microsoft.Devices/IotHubs**.

1. Přidejte skupinu spotřebitelů nazvanou **devicesimulationtsi** do centra. V následujícím příkazu použijte název rozbočovače a akcelerátoru řešení:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Azure Cloud Shell teď můžete zavřít.

## <a name="create-a-new-time-series-insights-environment"></a>Vytvoření nového prostředí Time Series Insights

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) je plně spravovaná analytická, úložná a vizualizační služba pro správu dat časových řad v měřítku IoT v cloudu. Vytvoření nového prostředí Time Series Insights:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte **Vytvořit zdroj** > **Internet věcí** > **Time Series Insights**:

    ![Nové přehledy časové řady](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Chcete-li vytvořit prostředí Time Series Insights ve stejné skupině prostředků jako akcelerátor řešení, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Následující snímek obrazovky používá název **Contoso-TSI**. Po dokončení tohoto kroku si vyberte vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **contoso-simulace**. Použijte název akcelerátoru řešení. |
    | Umístění | Tento příklad používá **východní USA**. Vytvořte prostředí ve stejné oblasti jako akcelerátor simulace zařízení. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |

    ![Vytváření přehledů časových řad](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Přidání prostředí Time Series Insights do stejné skupiny prostředků jako akcelerátor řešení znamená, že se odstraní při odstranění akcelerátoru řešení.

1. Klikněte na **Vytvořit**. Vytvoření prostředí může trvat několik minut.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí pro připojení k centru IoT hub. Použijte skupinu spotřebitelů, kterou jste vytvořili v předchozích krocích. Zdroj událostí Time Series Insights vyžaduje vyhrazenou skupinu spotřebitelů, která není používána jinou službou.

1. Na webu Azure Portal přejděte do nového prostředí Time Series.

1. Vlevo klikněte na **Zdroje událostí**:

    ![Zobrazit zdroje událostí](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na **Přidat**:

    ![Přidat zdroj událostí](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Chcete-li službu IoT hub nakonfigurovat jako nový zdroj událostí, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název zdroje události | Následující snímek obrazovky používá název **contoso-iot-hub**. Po dokončení tohoto kroku použijte svůj vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Použití služby IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IoT Hub | **contoso-simulation7d894**. Použijte název centra IoT hub z akcelerátoru řešení Simulace zařízení. |
    | Název zásady centra IoT Hub | **iothubowner** |
    | Klíč zásad centra Iot | Toto pole je vyplněno automaticky. |
    | Skupina uživatelů centra IoT Hub | **devicesimulationtsi** |
    | Formát serializace události | **JSON** |
    | Název vlastnosti časového razítka | Ponechte prázdné |

    ![Vytvořit zdroj událostí](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na **Vytvořit**.

> [!NOTE]
> Můžete [udělit přístup dalším uživatelům k](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access) průzkumníku Time Series Insights.

## <a name="start-a-simulation"></a>Spuštění simulace

Před použitím průzkumníka Time Series Insights nakonfigurujte akcelerátor řešení Simulace zařízení tak, aby generoval nějakou telemetrii. Následující snímek obrazovky ukazuje běžící simulaci s 10 chladicími zařízeními:

![Simulace běžícího zařízení](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

Průzkumník Time Series Insights je webová aplikace, kterou můžete použít k vizualizaci telemetrie.

1. Na webu Azure Portal vyberte kartu **Přehled** přehledů časové řady.

1. Pokud chcete otevřít webovou aplikaci Průzkumník Časové řady Přehledy, klikněte na **Přejít do prostředí**:

    ![Průzkumník Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. V panelu pro výběr času vyberte z nabídky Rychlé časy **posledních 30 minut** a klepněte na **hledat**:

    ![Hledání průzkumníka Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. V panelu pojmů na levé straně vyberte **teplotu** jako **Measure** a **iothub-connection-device-id** jako hodnota **Rozdělit podle:**

    ![Dotaz průzkumníka Přehledy časových řad](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Klikněte pravým tlačítkem myši na graf a vyberte **Prozkoumat události**:

    ![Události průzkumníka Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Data události se zobrazí v mřížce:

    ![Tabulka průzkumníka Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klikněte na tlačítko perspektivního zobrazení:

    ![Perspektiva průzkumníka Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klepnutím **+** přidáte nový dotaz do perspektivy:

    ![Průzkumník Přehledů časových řad Přidat dotaz](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Jako časový rozsah vyberte **Posledních 30 minut,** **Vlhkost** jako **rozměr**a **iothub-connection-device-id** jako hodnota **Rozdělit podle:**

    ![Dotaz průzkumníka Přehledy časových řad](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kliknutím na tlačítko perspektivního zobrazení zobrazíte řídicí panel telemetrie zařízení:

    ![Řídicí panel průzkumníka Přehledy časových řad](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu prozkoumat další, nechte akcelerátor řešení nasazený.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho ze stránky [Zřízená řešení,](https://www.azureiotsolutions.com/Accelerators#dashboard) vyberete ho a kliknete na **Odstranit řešení**.

Pokud jste přidali prostředí Time Series Insights do skupiny prostředků akcelerátoru řešení, je automaticky odstraněnpři odstranění akcelerátoru řešení. V opačném případě je nutné ručně odebrat prostředí Time Series Insights z portálu Azure.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak prozkoumat a dotazovat data v průzkumníku Time Series Insights, najdete v [tématu Průzkumník Přehledy Time Series](../time-series-insights/time-series-insights-explorer.md).
