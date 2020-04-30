---
title: 'Rychlý Start: Azure Time Series Insights Explorer – Azure Time Series Insights | Microsoft Docs'
description: Naučte se, jak začít s Průzkumníkem Azure Time Series Insights. Vizualizujte velké objemy dat IoT a klíčových funkcí vašeho prostředí.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/13/2020
ms.openlocfilehash: a566c55d2ac51efc5ff2650ad682f10dae2bdc31
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81271994"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Rychlý start: Prozkoumání služby Azure Time Series Insights

Tento rychlý Start Azure Time Series Insights Exploreru vám pomůže začít pracovat s Time Series Insights v bezplatném ukázkovém prostředí. V tomto rychlém startu se dozvíte, jak pomocí webového prohlížeče vizualizovat velké objemy dat IoT a klíčových funkcí, které jsou všeobecně dostupné.

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o datech, abyste mohli rychle ověřit vaše řešení IoT a vyhnout se nákladným výpadkům důležitých zařízení. Azure Time Series Insights vám pomůže odhalit skryté trendy, odhalit anomálie a provádět analýzy hlavních příčin téměř v reálném čase.

Pro větší flexibilitu můžete přidat Azure Time Series Insights do existující aplikace prostřednictvím svých výkonných [rozhraní REST API](./time-series-insights-update-tsq.md) a [klientské sady SDK](https://github.com/microsoft/tsiclient). Rozhraní API můžete použít k ukládání, dotazování a zpracování dat časových řad v klientské aplikaci podle vašeho výběru. Můžete také použít sadu SDK klienta k přidání součástí uživatelského rozhraní do existující aplikace.

Tento rychlý Start Time Series Insights Exploreru nabízí Průvodce funkcemi, které jsou všeobecně dostupné.

> [!IMPORTANT]
> Vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) , pokud jste ho ještě nevytvořili.

## <a name="prepare-the-demo-environment"></a>Příprava ukázkového prostředí

1. V prohlížeči přejdete do [ukázky obecné dostupnosti](https://insights.timeseries.azure.com/demo).

1. Pokud se zobrazí výzva, přihlaste se k Průzkumníkovi Time Series Insights pomocí svých přihlašovacích údajů k účtu Azure.

1. Zobrazí se stránka Time Series Insights Rychlá prohlídka. Kliknutím na tlačítko **Další** zahájíte rychlou prohlídku.

   [![Úvodní Start – výběr další](media/quickstart/quickstart-welcome.png)](media/quickstart/quickstart-welcome.png#lightbox)

## <a name="explore-the-demo-environment"></a>Prozkoumat ukázkové prostředí

1. Zobrazí se **panel pro výběr času** . Pomocí tohoto panelu vyberte časový rámec, který chcete vizualizovat.

   [![Panel výběru času](media/quickstart/quickstart-time-selection-panel.png)](media/quickstart/quickstart-time-selection-panel.png#lightbox)

1. Vyberte časový rámec a přetáhněte ho do oblasti. Pak vyberte **Hledat**.

   [![Výběr časového rámce](media/quickstart/quickstart-select-time.png)](media/quickstart/quickstart-select-time.png#lightbox)

   Time Series Insights zobrazí vizualizaci grafu pro zadaný časový rámec. V rámci spojnicového grafu můžete provádět různé akce. Můžete například filtrovat, připnout, řadit a Stack.

   Chcete-li se vrátit na **panel pro výběr času**, vyberte šipku dolů, jak je znázorněno níže:

   [![Graf](media/quickstart/quickstart-select-down-arrow.png)](media/quickstart/quickstart-select-down-arrow.png#lightbox)

1. Vyberte **Přidat** na **panelu podmínky** a přidejte nový hledaný výraz.

   [![Přidat panel vyhledávacích podmínek](media/quickstart/quickstart-add-terms.png)](media/quickstart/quickstart-add-terms.png#lightbox)

1. V grafu můžete vybrat oblast, kliknout na ni pravým tlačítkem a vybrat **Prozkoumat události**.

   [![Zkoumání událostí](media/quickstart/quickstart-explore-events.png)](media/quickstart/quickstart-explore-events.png#lightbox)

   Z oblasti, kterou zkoumáte, se zobrazí mřížka nezpracovaných dat.

   [![Prozkoumat události – zobrazení dat mřížky](media/quickstart/quickstart-explore-events-grid-data.png)](media/quickstart/quickstart-explore-events-grid-data.png#lightbox)

## <a name="select-and-filter-data"></a>Výběr a filtrování dat

1. Upravte své výrazy a změňte hodnoty v grafu. Přidejte další termín pro křížovou korelaci různých typů hodnot.

   [![Přidání výrazu](media/quickstart/quickstart-add-a-term.png)](media/quickstart/quickstart-add-a-term.png#lightbox)

1. Pole pro **Výběr** sady vyberte prázdné, pokud chcete zobrazit všechny vybrané hledané termíny nebo zadat termín filtru v poli **filtrovat řady** pro filtrování řady Improvised.

   [![Filtrování řad](media/quickstart/quickstart-filter-series.png)](media/quickstart/quickstart-filter-series.png#lightbox)

   Pro účely tohoto rychlého startu zadejte **Station5**, aby se provedla vzájemná korelace teploty a tlaku pro příslušnou stanici.

Po dokončení tohoto rychlého startu můžete experimentovat s ukázkovou sadou dat a vytvářet různé vizualizace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**a vyhledejte svoji Azure Time Series Insights skupinu prostředků.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) tak, že vyberete možnost **Odstranit** nebo odebrat jednotlivé prostředky zvlášť.

## <a name="next-steps"></a>Další kroky

Jste připraveni vytvořit vlastní prostředí Time Series Insights:
> [!div class="nextstepaction"]
> [Plánování prostředí Time Series Insights](time-series-insights-environment-planning.md)
