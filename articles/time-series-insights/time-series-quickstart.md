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
ms.date: 09/23/2019
ms.openlocfilehash: 0253a46cd73679c7c2f50a1e37d26d67578e342e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006423"
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

   [![vybrat další](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Prozkoumat ukázkové prostředí

1. Zobrazí se **panel pro výběr času** . Pomocí tohoto panelu vyberte časový rámec, který chcete vizualizovat.

   [panel pro výběr ![ho času](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Vyberte časový rámec a přetáhněte ho do oblasti. Pak vyberte **Hledat**.

   [![výběr časového rámce](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Time Series Insights zobrazí vizualizaci grafu pro zadaný časový rámec. V rámci spojnicového grafu můžete provádět různé akce. Můžete například filtrovat, připnout, řadit a Stack.

   Chcete-li se vrátit na **panel pro výběr času**, vyberte šipku dolů, jak je znázorněno níže:

   [![graf](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Vyberte **Přidat** na **panelu podmínky** a přidejte nový hledaný výraz.

   [![přidat položku](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. V grafu můžete vybrat oblast, kliknout na ni pravým tlačítkem a vybrat **Prozkoumat události**.

   [![prozkoumat události](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Z oblasti, kterou zkoumáte, se zobrazí mřížka nezpracovaných dat.

   [zobrazení mřížky ![](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Výběr a filtrování dat

1. Upravte své výrazy a změňte hodnoty v grafu. Přidejte další termín pro křížovou korelaci různých typů hodnot.

   [![přidat termín](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Do pole **filtrovat řady** pro filtrování řady Improvised Zadejte termín filtru. Pro účely tohoto rychlého startu zadejte **Station5**, aby se provedla vzájemná korelace teploty a tlaku pro příslušnou stanici.

   [řady ![filtru](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Po dokončení tohoto rychlého startu můžete experimentovat s ukázkovou sadou dat a vytvářet různé vizualizace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Teď, když jste dokončili kurz, vyčistěte prostředky, které jste vytvořili:

1. V nabídce vlevo v [Azure Portal](https://portal.azure.com)vyberte **všechny prostředky**a vyhledejte svoji Azure Time Series Insights skupinu prostředků.
1. Odstraňte celou skupinu prostředků (a všechny prostředky v ní obsažené) tak, že vyberete možnost **Odstranit** nebo odebrat jednotlivé prostředky zvlášť.

## <a name="next-steps"></a>Další kroky

Jste připraveni vytvořit vlastní prostředí Time Series Insights:
> [!div class="nextstepaction"]
> [Plánování prostředí Time Series Insights](time-series-insights-environment-planning.md)
