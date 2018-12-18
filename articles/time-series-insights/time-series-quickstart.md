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
ms.date: 11/15/2017
ms.openlocfilehash: 32ed0ec178f7a0064fae88357011197e71604885
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555536"
---
# <a name="quickstart-explore-azure-time-series-insights"></a>Rychlý start: Prozkoumat službu Azure Time Series Insights
V tomto rychlém startu se dozvíte, jak začít s průzkumníkem služby Azure Time Series Insights v bezplatném ukázkovém prostředí. Zjistíte, jak pomocí webového prohlížeče vizualizovat velké objemy dat IoT a prohlédnete si klíčové funkce průzkumníka služby Time Series Insights. 

Azure Time Series Insights je plně spravovaná služba pro analýzy, ukládání a vizualizace, která usnadňuje zkoumání a analýzy miliard událostí IoT současně. Poskytuje globální přehled o datech a umožňuje rychle ověřit vaše řešení IoT a vyhnout se nákladným prostojům důležitých zařízení, protože pomáhá odhalovat skryté trendy, detekovat anomálie a provádět analýzy původních příčin téměř v reálném čase.  Pokud vytváříte aplikaci, která potřebuje ukládat nebo dotazovat data časových řad, můžete při vývoji využít rozhraní REST API služby Time Series Insights.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Průzkumník služby Time Series Insights v ukázkovém prostředí

1. V prohlížeči přejděte na adresu [https://insights.timeseries.azure.com/demo](https://insights.timeseries.azure.com/demo). 

2. Pokud k tomu budete vyzváni, přihlaste se do průzkumníka služby Time Series Insights pomocí přihlašovacích údajů svého účtu Azure. 
 
3. Zobrazí se stránka s krátkou prohlídkou služby Time Series Insights. Zahajte rychlou prohlídku kliknutím na **Další**.

   ![Kliknutí na Další](media/quickstart/quickstart1.png)

4. Zobrazí se **Panel výběru času**. Pomocí tohoto panelu vyberte časový rámec, který chcete vizualizovat.

   ![Panel výběru času](media/quickstart/quickstart2.png)

5. Klikněte na oblast a tažením ji přesuňte do grafu, pak klikněte na tlačítko **Vyhledat**.
 
   ![Výběr časového rámce](media/quickstart/quickstart3.png) 

   Time Series Insights zobrazí vizualizaci grafu pro zadaný časový rámec. Ve spojnicovém grafu můžete provádět různé akce, jako jsou filtrování, připínání, řazení a vrstvení. 

   Pokud se chcete vrátit na **Panel výběru času**, klikněte na šipku dolů, jak je znázorněno níže:

   ![Graf](media/quickstart/quickstart4.png)

6. Na **Panelu výrazů** klikněte na **Přidat** a přidejte nový hledaný výraz.

   ![Přidání položky](media/quickstart/quickstart5.png)

7. V grafu můžete vybrat oblast, kliknout na ni pravým tlačítkem a vybrat **Prozkoumat události**.
 
   ![Zkoumání událostí](media/quickstart/quickstart6.png)

   Zobrazí se mřížka nezpracovaných dat ze zkoumané oblasti:

   ![Zobrazení mřížky](media/quickstart/quickstart7.png)

8. Upravte výrazy, aby se změnily hodnoty v grafu, a přidejte další výraz, aby se provedla vzájemná korelace různých typů hodnot:

   ![Přidání výrazu](media/quickstart/quickstart8.png)

9. Do pole **Filtrovat řady...** zadejte podmínky filtru a proveďte ad hoc filtrování řad. Pro účely tohoto rychlého startu zadejte **Station5**, aby se provedla vzájemná korelace teploty a tlaku pro příslušnou stanici.
 
   ![Filtrování řad](media/quickstart/quickstart9.png)

Po dokončení tohoto rychlého startu můžete experimentovat s ukázkovou sadou dat a vytvářet různé vizualizace. 

### <a name="next-steps"></a>Další postup
Jste připraveni vytvořit vlastní prostředí Time Series Insights:
> [!div class="nextstepaction"]
> [Plánování prostředí Time Series Insights](time-series-insights-environment-planning.md)
