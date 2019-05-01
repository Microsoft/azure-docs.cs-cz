---
title: 'Rychlý start: Průzkumník služby Azure Time Series Insights | Dokumentace Microsoftu'
description: V tomto rychlém startu se dozvíte, jak začít s vizualizací velkých objemů dat IoT pomocí průzkumníka služby Azure Time Series Insights ve webovém prohlížeči. Prohlédněte si klíčové funkce v ukázkovém prostředí.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 04/22/2019
ms.openlocfilehash: be663520c9c1afd11ace57cd9dcb8ffb8219b86b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696960"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Rychlý start: Prozkoumat službu Azure Time Series Insights

Rychlý start explorer je k dispozici začít s Azure Time Series Insights v bezplatném ukázkovém prostředí. Prostřednictvím, se dozvíte, jak pomocí webového prohlížeče vizualizovat velké objemy IoT dat a prohlídku klíčových funkcí v současné době v obecné dostupnosti.

Azure Time Series Insights je plně spravovaná služba analýzy, ukládání a vizualizace, která zjednodušuje postupy zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o vašich datech a umožňuje rychle ověřit vaše řešení IoT a vyhnout se nákladným prostojům důležitých zařízení. Prostřednictvím služby Azure Time Series Insights můžete odhalovat skryté trendy, detekovat anomálie a provádět analýzy hlavních příčin téměř v reálném čase.

Pro větší flexibilitu, lze přidat Azure Time Series Insights do už existující aplikaci prostřednictvím jeho výkonné [rozhraní REST API](./time-series-insights-update-tsq.md) a [Klientská sada SDK](./tutorial-create-tsi-sample-spa.md). Rozhraní API vám umožňují ukládat data časových řad pro dotazování a využívat data časových řad v klientské aplikaci podle vašeho výběru. Můžete také použít klientskou sadou SDK pro přidání součásti uživatelského rozhraní do stávající aplikace.

Time Series Insights explorer je seznámení s funkcí v současné době obecné dostupnosti.

## <a name="prepare-the-demo-environment"></a>Příprava prostředí pro ukázku

1. Vytvoření [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Pokud ještě nebyl vytvořen.

1. V prohlížeči přejděte [všeobecné dostupnosti ukázka](https://insights.timeseries.azure.com/demo).

1. Pokud se zobrazí výzva, přihlaste se do Průzkumníka služby Time Series Insights pomocí přihlašovacích údajů svého účtu Azure.

1. Zobrazí se stránka s krátkou prohlídkou služby Time Series Insights. Zahajte rychlou prohlídku kliknutím na **Další**.

   [![Klikněte na tlačítko Další](media/quickstart/quickstart1.png)](media/quickstart/quickstart1.png#lightbox)

## <a name="explore-the-demo-environment"></a>Prozkoumejte ukázkovém prostředí

1. Zobrazí se **Panel výběru času**. Pomocí tohoto panelu vyberte časový rámec, který chcete vizualizovat.

   [![Panel výběru času](media/quickstart/quickstart2.png)](media/quickstart/quickstart2.png#lightbox)

1. Klikněte na oblast a tažením ji přesuňte do grafu, pak klikněte na tlačítko **Vyhledat**.

   [![Vyberte časový rámec](media/quickstart/quickstart3.png)](media/quickstart/quickstart3.png#lightbox)

   Time Series Insights zobrazí vizualizaci grafu pro zadaný časový rámec. Ve spojnicovém grafu můžete provádět různé akce, jako jsou filtrování, připínání, řazení a vrstvení.

   Pokud se chcete vrátit na **Panel výběru času**, klikněte na šipku dolů, jak je znázorněno níže:

   [![Graf](media/quickstart/quickstart4.png)](media/quickstart/quickstart4.png#lightbox)

1. Na **Panelu výrazů** klikněte na **Přidat** a přidejte nový hledaný výraz.

   [![Přidání položky](media/quickstart/quickstart5.png)](media/quickstart/quickstart5.png#lightbox)

1. V grafu můžete vybrat oblast, kliknout na ni pravým tlačítkem a vybrat **Prozkoumat události**.

   [![Zkoumat události](media/quickstart/quickstart6.png)](media/quickstart/quickstart6.png#lightbox)

   Zobrazí se mřížka nezpracovaných dat ze zkoumané oblasti:

   [![Zobrazení mřížky](media/quickstart/quickstart7.png)](media/quickstart/quickstart7.png#lightbox)

## <a name="select-and-filter-data"></a>Výběr a filtrování dat

1. Upravte výrazy, aby se změnily hodnoty v grafu, a přidejte další výraz, aby se provedla vzájemná korelace různých typů hodnot:

   [![Přidání výrazu](media/quickstart/quickstart8.png)](media/quickstart/quickstart8.png#lightbox)

1. Zadejte podmínky filtru a v **filtrovat řady...**  pole pro filtrování improvizovanými řad. Pro účely tohoto rychlého startu zadejte **Station5**, aby se provedla vzájemná korelace teploty a tlaku pro příslušnou stanici.

   [![Filtrovat řady](media/quickstart/quickstart9.png)](media/quickstart/quickstart9.png#lightbox)

Po dokončení tohoto rychlého startu můžete experimentovat s ukázkovou sadou dat a vytvářet různé vizualizace.

## <a name="next-steps"></a>Další postup

Jste připraveni vytvořit vlastní prostředí Time Series Insights:
> [!div class="nextstepaction"]
> [Plánování prostředí Time Series Insights](time-series-insights-environment-planning.md)
