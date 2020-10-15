---
title: Vizualizace simulované telemetrie pomocí Time Series Insights – Azure | Microsoft Docs
description: Naučte se konfigurovat prostředí Time Series Insights pro zkoumání a analýzu telemetrie vygenerované akcelerátorem řešení pro simulaci zařízení.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: db9ba3efe60af830c0e15310b6127c18130f00b9
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076245"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Použít Time Series Insights k vizualizaci telemetrie odeslané z akcelerátoru řešení pro simulaci zařízení

Akcelerátor řešení pro simulaci zařízení umožňuje generovat telemetrii z simulovaných zařízení a testovat vaše řešení IoT. V této příručce se dozvíte, jak vizualizovat a analyzovat simulaci telemetrie pomocí Time Series Insightsho prostředí.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto průvodci, potřebujete aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

Postup v tomto průvodci předpokládá, že jste do svého předplatného Azure nasadili akcelerátor řešení pro simulaci zařízení. Pokud jste nenainstalovali akcelerátor řešení, postupujte podle kroků v části [nasazení a spuštění cloudového řešení simulace zařízení](quickstart-device-simulation-deploy.md) .

V tomto článku se předpokládá, že název akcelerátoru řešení je **simulace společnosti Contoso**. Při provádění následujících kroků nahraďte **simulaci contoso** názvem svého akcelerátoru řešení.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Vytvoření skupiny uživatelů

Ve službě IoT Hub potřebujete vytvořit vyhrazenou skupinu uživatelů pro streamování telemetrie do Time Series Insights. Zdroj události v Time Series Insights by měl mít výhradní použití IoT Hub skupiny příjemců.

Následující kroky používají rozhraní příkazového řádku Azure CLI v Azure Cloud Shell k vytvoření skupiny uživatelů:

1. IoT Hub je jedním z několika prostředků vytvořených při nasazení akcelerátoru řešení pro simulaci zařízení. Spusťte následující příkaz, kde najdete název vašeho centra IoT – nezapomeňte použít název vašeho akcelerátoru řešení:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Centrum IoT je prostředkem typu **Microsoft. Devices/IotHubs**.

1. Přidejte do centra skupinu příjemců s názvem **devicesimulationtsi** . V následujícím příkazu použijte název vašeho centra a akcelerátoru řešení:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Nyní můžete Azure Cloud Shell zavřít.

## <a name="create-a-new-time-series-insights-environment"></a>Vytvoření nového Time Series Insightsho prostředí

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) je plně spravovaná služba pro analýzy, ukládání a vizualizace, která slouží ke správě dat časových řad ve službě IoT-Scale v cloudu. Vytvoření nového prostředí Time Series Insights:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Vyberte **vytvořit prostředek**  >  **Internet věcí**  >  **Time Series Insights**:

    ![Nový Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Pokud chcete vytvořit prostředí Time Series Insights ve stejné skupině prostředků jako akcelerátor řešení, použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název prostředí | Následující snímek obrazovky používá název **Contoso-TSI**. Po dokončení tohoto kroku vyberte svůj vlastní jedinečný název. |
    | Předplatné | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Skupina prostředků | **simulace společnosti Contoso**. Použijte název akcelerátoru řešení. |
    | Umístění | Tento příklad používá **východní USA**. Vytvořte prostředí ve stejné oblasti jako akcelerátor simulace zařízení. |
    | Skladová jednotka (SKU) |**S1** |
    | Kapacita | **1** |

    ![Vytvoření instance Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Přidání prostředí Time Series Insights do stejné skupiny prostředků jako akcelerátor řešení znamená, že při odstranění akcelerátoru řešení dojde k jeho odstranění.

1. Klikněte na **Vytvořit**. Vytvoření prostředí může trvat několik minut.

## <a name="create-event-source"></a>Vytvoření zdroje událostí

Vytvořte nový zdroj událostí pro připojení ke službě IoT Hub. Použijte skupinu uživatelů, kterou jste vytvořili v předchozích krocích. Zdroj události Time Series Insights vyžaduje vyhrazenou skupinu uživatelů, která se nepoužívá v jiné službě.

1. V Azure Portal přejděte do prostředí nové časové řady.

1. Na levé straně klikněte na **zdroje událostí**:

    ![Zobrazení zdrojů událostí](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Klikněte na **Přidat**:

    ![Přidat zdroj události](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Ke konfiguraci služby IoT Hub jako nového zdroje událostí použijte hodnoty v následující tabulce:

    | Nastavení | Hodnota |
    | ------- | ----- |
    | Název zdroje události | Následující snímek obrazovky používá název **Contoso-IoT-Hub**. Po dokončení tohoto kroku použijte vlastní jedinečný název. |
    | Zdroj | **IoT Hub** |
    | Možnost importu | **Použít IoT Hub z dostupných předplatných** |
    | ID předplatného | Z rozevíracího seznamu vyberte své předplatné Azure. |
    | Název centra IoT Hub | **Contoso-simulation7d894**. Použijte název centra IoT z akcelerátoru řešení pro simulaci zařízení. |
    | Název zásady centra IoT Hub | **iothubowner** |
    | Klíč zásad centra IoT Hub | Toto pole je vyplněno automaticky. |
    | Skupina uživatelů centra IoT Hub | **devicesimulationtsi** |
    | Formát serializace události | **JSON** |
    | Název vlastnosti časového razítka | Ponechte prázdné. |

    ![Vytvořit zdroj události](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Klikněte na **Vytvořit**.

> [!NOTE]
> [Dalším uživatelům můžete udělit přístup](../time-series-insights/concepts-access-policies.md#grant-data-access) k Průzkumníku Time Series Insights.

## <a name="start-a-simulation"></a>Spustit simulaci

Před použitím Time Series Insights Exploreru nakonfigurujte akcelerátor řešení pro simulaci zařízení, aby se vygenerovala nějaká telemetrie. Následující snímek obrazovky ukazuje běžící simulaci s 10 zařízeními chladicího zařízení:

![Simulace spuštění zařízení](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

Time Series Insights Explorer je webová aplikace, kterou můžete použít k vizualizaci telemetrie.

1. V Azure Portal vyberte kartu **přehled** Time Series Insights.

1. Pro otevření webové aplikace v Průzkumníkovi Time Series Insights klikněte na **Přejít do prostředí**:

    ![Průzkumník Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. Na panelu pro výběr času vyberte v nabídce Rychlé časy **posledních 30 minut** a klikněte na **Hledat**:

    ![Hledání v Průzkumníkovi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Na panelu podmínka na levé straně vyberte **teplotu** jako **míru** a **iothub-Connection-Device-ID** jako **rozdělení podle** hodnoty:

    ![Snímek obrazovky, který zobrazuje panel Time Series Insights podmínek se zvýrazněnými hodnotami "měření" a "rozdělit podle".](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Klikněte pravým tlačítkem na graf a vyberte **prozkoumat události**:

    ![Události Time Series Insights Exploreru](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Data události se zobrazí v mřížce:

    ![Tabulka Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Klikněte na tlačítko Zobrazení perspektivy:

    ![Perspektiva Time Series Insights Exploreru](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknutím **+** můžete přidat nový dotaz do perspektivy:

    ![Přidat dotaz v Průzkumníkovi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Jako časový rozsah vyberte jako časové rozmezí, **vlhkost** jako **míru**a **iothub-Connection-ID zařízení** jako **rozdělení podle** hodnoty zadejte **posledních 30 minut** :

    ![Dotaz aplikace Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kliknutím na tlačítko perspektivní zobrazení zobrazte řídicí panel telemetrie zařízení:

    ![Řídicí panel Průzkumníka Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete prozkoumat další, nechte akcelerátor řešení nasazený.

Pokud už akcelerátor řešení nepotřebujete, odstraňte ho ze stránky [zřízené řešení](https://www.azureiotsolutions.com/Accelerators#dashboard) , a to tak, že ho vyberete a pak kliknete na **Odstranit řešení**.

Pokud jste přidali prostředí Time Series Insights do skupiny prostředků akcelerátoru řešení, automaticky se odstraní při odstranění akcelerátoru řešení. V opačném případě je nutné ručně odebrat Time Series Insights prostředí z Azure Portal.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak prozkoumat a dotazovat data v Průzkumníkovi Time Series Insights, najdete v článku [Azure Time Series Insights Explorer](../time-series-insights/time-series-insights-explorer.md).