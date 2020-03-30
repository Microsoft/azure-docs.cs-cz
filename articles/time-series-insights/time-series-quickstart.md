---
title: 'Úvodní příručka: Průzkumník Přehledů Azure Time Series – Přehledy Azure Time Series | Dokumenty společnosti Microsoft'
description: Zjistěte, jak začít s průzkumníkem Azure Time Series Insights. Vizualizujte velké objemy dat IoT a klíčové funkce prohlídek vašeho prostředí.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 01/06/2020
ms.openlocfilehash: a905054b1b2a04fa2b7865d2c1065ccee37cffc0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75860426"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Rychlý start: Prozkoumání služby Azure Time Series Insights

Tento rychlý start průzkumníka Azure Time Series Insights vám pomůže začít pracovat s Time Series Insights ve bezplatném demonstračním prostředí. V tomto rychlém startu se dozvíte, jak pomocí webového prohlížeče vizualizovat velké objemy dat IoT a klíčové funkce prohlídek, které jsou teď obecně dostupné.

Azure Time Series Insights je plně spravovaná analytická, úložná a vizualizační služba, která zjednodušuje, jak zkoumat a analyzovat miliardy událostí IoT současně. Poskytuje globální přehled o vašich datech, takže můžete rychle ověřit své řešení IoT a vyhnout se nákladným prostojům kritických zařízení. Azure Time Series Insights vám pomůže objevit skryté trendy, rozpoznat anomálie a provádět analýzy hlavních příčin téměř v reálném čase.

Pro větší flexibilitu můžete přidat Azure Time Series Insights do již existující aplikace prostřednictvím svých [výkonných rest API](./time-series-insights-update-tsq.md) a [klientské sady SDK](https://github.com/microsoft/tsiclient). Pomocí těchto api můžete ukládat, dotazovat a využívat data časových řad v klientské aplikaci podle vašeho výběru. Pomocí sady SDK klienta můžete také přidat součásti ui do existující aplikace.

Tento time series insights explorer rychlý start nabízí prohlídku funkcí, které jsou v obecné dostupnosti.

> [!IMPORTANT]
> Vytvořte si [bezplatný účet Azure,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) pokud jste ho ještě nevytvořili.

## <a name="prepare-the-demo-environment"></a>Příprava demo prostředí

1. V prohlížeči přejděte na [ukázku obecné dostupnosti](https://insights.timeseries.azure.com/demo).

1. Pokud se zobrazí výzva, přihlaste se k průzkumníku Time Series Insights pomocí přihlašovacích údajů účtu Azure.

1. Zobrazí se stránka rychlé prohlídky Time Series Insights. Výběrem **možnosti Další** zahájíte rychlou prohlídku.

   [![Rychlý start uvítání - vyberte další](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Prozkoumejte ukázkové prostředí

1. Zobrazí se **panel pro výběr času.** Pomocí tohoto panelu vyberte časový rámec, který chcete vizualizovat.

   [![Panel výběru času](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Vyberte časový rámec a přetáhněte jej v oblasti. Pak vyberte **Hledat**.

   [![Výběr časového rámce](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Time Series Insights zobrazí vizualizaci grafu pro zadaný časový rámec. V spojnicovém grafu můžete dělat různé akce. Můžete například filtrovat, připínat, řadit a skládat.

   Chcete-li se vrátit k **panelu pro výběr času**, vyberte šipku dolů, jak je znázorněno:

   [![Graf](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Vyberte **Přidat** v **panelu Termíny,** chcete-li přidat nový hledaný výraz.

   [![Panel Přidat hledané výrazy](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. V grafu můžete vybrat oblast, kliknout na ni pravým tlačítkem a vybrat **Prozkoumat události**.

   [![Zkoumání událostí](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Mřížka nezpracovaných dat se zobrazí z oblasti, kterou zkoumáte.

   [![Prozkoumat události – zobrazení dat mřížky](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Výběr a filtrování dat

1. Upravte termíny a změňte hodnoty v grafu. Přidejte další termín pro křížovou koreluji různých typů hodnot.

   [![Přidání výrazu](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Ponechte pole **Řady filtrů** prázdné, aby se zobrazily všechny vybrané hledané termíny, nebo zadejte termín filtru do pole **Řada filtrů** pro improvizované filtrování řad.

   [![Filtrování řad](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Pro účely tohoto rychlého startu zadejte **Station5**, aby se provedla vzájemná korelace teploty a tlaku pro příslušnou stanici.

Po dokončení tohoto rychlého startu můžete experimentovat s ukázkovou sadou dat a vytvářet různé vizualizace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V levé nabídce na [webu Azure Portal](https://portal.azure.com)vyberte Všechny **prostředky**, vyhledejte skupinu prostředků Azure Time Series Insights.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) výběrem **možnosti Odstranit** nebo odebrat jednotlivé prostředky jednotlivě.

## <a name="next-steps"></a>Další kroky

Jste připraveni vytvořit si vlastní prostředí Time Series Insights:
> [!div class="nextstepaction"]
> [Plánování prostředí Time Series Insights](time-series-insights-environment-planning.md)
