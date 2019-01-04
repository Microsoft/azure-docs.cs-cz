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
ms.openlocfilehash: aea02cbde32d9485bd49ec39a6f300021c6ef927
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597694"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Vizualizace telemetrická data odesílaná z akcelerátoru řešení simulace zařízení pomocí služby Time Series Insights

Akcelerátor řešení simulace zařízení umožňuje generování telemetrie ze simulovaných zařízení pro testování řešení IoT. Tato příručka ukazuje, jak vizualizovat a analyzovat Simulovaná telemetrická data pomocí prostředí Time Series Insights.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků v této příručce s postupy, potřebujete aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Kroky v této příručce s postupy předpokládají, že akcelerátor řešení simulace zařízení jste nasadili do vašeho předplatného Azure. Pokud jste nenasadili akcelerátor řešení, postupujte podle kroků v [nasazení a spuštění řešení cloudové zařízení simulace](quickstart-device-simulation-deploy.md) rychlý start.

Tento článek předpokládá, že je název akcelerátor řešení **contoso simulace**. Nahraďte **contoso simulace** s názvem jako akcelerátor řešení proveďte následující kroky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

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

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zkoumat a dotazování dat v Průzkumníku Time Series Insights najdete v tématu [Průzkumníka služby Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
