---
title: Vizualizujte vzdálené monitorování telemetrických dat s Azure Time Series Insights | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat prostředí Time Series Insights můžete zkoumat a analyzovat telemetrii generovanou akcelerátoru řešení vzdáleného monitorování.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 02b3afbc62be7a7a9c396de888378d762405248a
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383122"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-remote-monitoring-solution-accelerator"></a>Vizualizace telemetrická data odesílaná z akcelerátor řešení vzdálené monitorování pomocí služby Time Series Insights

Jakožto Obsluha můžete rozšířit funkce pro vizualizaci dat out-of-the-box akcelerátoru řešení vzdáleného monitorování. Tato příručka ukazuje, jak používat prostředí Time Series Insights umožňuje vizualizovat a analyzovat telemetrická data odesílaná do akcelerátoru řešení vzdáleného monitorování.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle kroků v této příručce s postupy, potřebujete aktivní předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Kroky v této příručce s postupy předpokládají, že akcelerátor řešení vzdálené monitorování jste nasadili do vašeho předplatného Azure. Pokud jste nenasadili akcelerátor řešení, postupujte podle kroků v [nasadit a spustit cloudové řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md) rychlý start.

Tento článek předpokládá, že je název akcelerátor řešení **contoso simulace**. Nahraďte **contoso simulace** s názvem jako akcelerátor řešení proveďte následující kroky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="time-series-insights-explorer"></a>Průzkumník Time Series Insights

V Průzkumníku Time Series Insights je webová aplikace, které můžete použít k vizualizaci telemetrických dat.

1. Na webu Azure Portal, vyberte Time Series Insights **přehled** kartu.

1. Webové aplikace Průzkumníka Time Series Insights, klikněte na tlačítko **přejít na prostředí**:

    ![Průzkumník Time Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-environment.png)

1. Panel výběru času vyberte **posledních 30 minut** z rychlé časy nabídky a klikněte na tlačítko **hledání**:

    ![Čas hledání v Průzkumníkovi Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-search-time.png)

1. V panelu podmínky na levé straně vyberte **teploty** jako **míru** a **iothub připojení zařízení id** jako **rozdělit podle** hodnotu:

    ![Čas Series Insights Průzkumník dotazů](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query1.png)

1. Klikněte pravým tlačítkem na graf a vyberte **zkoumat události**:

    ![Čas události explorer Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explore-events.png)

1. Data události se zobrazí v tabulce:

    ![Tabulka explorer doby Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-table.png)

1. Klikněte na tlačítko perspektivy zobrazení:

    ![Čas Series Insights explorer perspektivy](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explorer-perspective.png)

1. Klikněte na tlačítko **+** přidat nový dotaz do perspektivy:

    ![Průzkumník Time Series Insights přidat dotaz](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-new-query.png)

1. Vyberte **posledních 30 minut** jako časové období **vlhkosti** jako **míru**, a **iothub připojení zařízení id** jako **Rozdělit podle** hodnotu:

    ![Čas Series Insights Průzkumník dotazů](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query2.png)

1. Kliknutím na tlačítko zobrazení perspektivy zobrazení řídicího panelu telemetrie zařízení:

    ![Řídicí panel explorer čas Series Insights](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak zkoumat a dotazování dat v Průzkumníku Time Series Insights najdete v tématu [Průzkumníka služby Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
