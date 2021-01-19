---
title: Integrace s Azure Maps
titleSuffix: Azure Digital Twins
description: Podívejte se, jak pomocí Azure Functions vytvořit funkci, která může použít oznámení o vytvářené grafice a digitální vlákna Azure k aktualizaci Azure Maps vnitřní mapy.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.reviewer: baanders
ms.openlocfilehash: 6a654f74ff6a32ad37646021d504359c84942c12
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573033"
---
# <a name="use-azure-digital-twins-to-update-an-azure-maps-indoor-map"></a>Použití digitálních vláken Azure k aktualizaci mapy vnitřních Azure Maps

Tento článek vás provede kroky potřebnými k aktualizaci dat, která se zobrazují na *mapě interiéru* pomocí nástroje [Azure Maps](../azure-maps/about-azure-maps.md), pomocí dat digitálních vláken Azure. Služba Azure Digital autoformaes ukládá graf vašich vztahů zařízení IoT a směruje telemetrii do různých koncových bodů a díky tomu představuje ideální službu pro aktualizaci informačních překryvů na mapách.

Tento postup se zabývá těmito postupy:

1. Konfigurace instance digitálního vlákna Azure pro posílání dvojitě aktualizovaných událostí do funkce v [Azure Functions](../azure-functions/functions-overview.md).
2. Vytvoření funkce pro aktualizaci Azure Maps funkce vnitřních map stateset
3. Jak ukládat ID map a ID stateset funkcí do grafu digitálních vláken Azure

### <a name="prerequisites"></a>Požadavky

* Postupujte podle kurzu digitálních vláken Azure [*: připojení kompletního řešení*](./tutorial-end-to-end.md).
    * Tuto dvojitou cestu rozšíříte pomocí dalšího koncového bodu a trasy. Z tohoto kurzu taky přidáte další funkci do aplikace Function App. 
* Postupujte podle Azure Maps [*kurzu: pomocí nástroje Azure Maps Creator Vytvořte vnitřní*](../azure-maps/tutorial-creator-indoor-maps.md) mapy a vytvořte Azure Mapsou vnitřní mapu s *funkcí stateset*.
    * [Funkce statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) jsou kolekce dynamických vlastností (stavů) přiřazených k funkcím datové sady, jako jsou místnosti nebo vybavení. V tomto kurzu Azure Maps ukládá funkce stateset stav místnosti, který budete zobrazovat na mapě.
    * Budete potřebovat *STATESET ID* vaší funkce a *klíč předplatného* Azure Maps.

### <a name="topology"></a>Topologie

Následující obrázek znázorňuje, kde se prvky integrace vnitřních map v tomto kurzu vejdou do většího, uceleného scénáře digitálních vláken Azure.

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="Zobrazení služeb Azure v rámci uceleného scénáře – zvýraznění části pro integraci vnitřních map" lightbox="media/how-to-integrate-maps/maps-integration-topology.png":::

## <a name="create-a-function-to-update-a-map-when-twins-update"></a>Vytvoření funkce pro aktualizaci mapy při aktualizaci dvojitých vláken

Nejdřív vytvoříte trasu v části digitální vlákna Azure, která bude předávat všechny události s dvojitou aktualizací do tématu Event Grid. Pak použijete funkci ke čtení těchto zpráv aktualizace a aktualizaci stateset funkce v Azure Maps. 

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Vytvořit trasu a filtr pro oznámení o aktualizacích dvojčete

Instance digitálních vláken Azure mohou generovat události s dvojitou aktualizací pokaždé, když se aktualizuje stav vlákna. Kurz k digitálním vazbám Azure [*: připojení kompletního řešení*](./tutorial-end-to-end.md) propojeného výše projde scénářem, kdy se k aktualizaci atributu teploty připojeného ke zdvojení místnosti používá teploměr. Toto řešení rozšíříte tak, že se přihlásíte k odběru oznámení o aktualizacích pro vlákna a tyto informace použijete k aktualizaci vašich map.

Tento model čte přímo z místnosti místo zařízení IoT, což vám dává flexibilitu při změně podkladového zdroje dat na teplotu, aniž by bylo potřeba aktualizovat logiku mapování. Můžete například přidat více teploměrů nebo nastavit tuto místnost pro sdílení teploměru s jinou místností, a to vše bez nutnosti aktualizovat logiku mapy.

1. Vytvořte téma Event gridu, které bude přijímat události z instance digitálního vlákna Azure.
    ```azurecli-interactive
    az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
    ```

2. Vytvořte koncový bod, který propojí téma Event Grid s digitálními podsítěmi Azure.
    ```azurecli-interactive
    az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
    ```

3. Vytvořte trasu v Azure Digital Twins k posílání událostí aktualizací dvojčete do vašeho koncového bodu.

    >[!NOTE]
    >V současné době existuje **známý problém** v Cloud Shellu, který se týká těchto skupin příkazů: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Pokud chcete tento problém vyřešit, buď spusťte `az login` v Cloud Shellu před spuštěním příkazu, nebo místo Cloud Shellu použijte [místní rozhraní příkazového řádku (CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Další podrobnosti najdete v tématu [*řešení potíží: známé problémy v Azure Digital revláken*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

## <a name="create-a-function-to-update-maps"></a>Vytvoření funkce pro aktualizaci map

Chystáte se vytvořit funkci aktivovanou **Event Grid** v rámci aplikace Function App z kompletního kurzu ([*kurz: připojení kompletního řešení*](./tutorial-end-to-end.md)). Tato funkce rozbalí tato oznámení a pošle aktualizace Azure Maps funkcím stateset k aktualizaci teploty jedné místnosti.

V následujícím dokumentu najdete referenční informace: [*Azure Event Grid Trigger pro Azure Functions*](../azure-functions/functions-bindings-event-grid-trigger.md).

Kód funkce nahraďte následujícím kódem. Odfiltruje pouze aktualizace vláken na prostor, přečte aktualizovanou teplotu a odešle tyto informace do Azure Maps.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateMaps.cs":::

Ve své aplikaci Function App budete muset nastavit dvě proměnné prostředí. Jedním z nich je váš [Azure Maps primární klíč předplatného](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account)a jedna je vaše [Azure Maps ID stateset](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --name <your-App-Service-(function-app)-name> --resource-group <your-resource-group> --settings "subscription-key=<your-Azure-Maps-primary-subscription-key>"
az functionapp config appsettings set --name <your-App-Service-(function-app)-name>  --resource-group <your-resource-group> --settings "statesetID=<your-Azure-Maps-stateset-ID>"
```

### <a name="view-live-updates-on-your-map"></a>Zobrazit živé aktualizace na mapě

Pokud chcete vidět živou aktualizaci, postupujte podle následujících kroků:

1. Začněte posílat Simulovaná data IoT spuštěním projektu **DeviceSimulator** z kurzu digitálních vláken Azure [*: Připojte ucelené řešení*](tutorial-end-to-end.md). Pokyny k tomuto postupu najdete v části [*Configure and run the simulace*](././tutorial-end-to-end.md#configure-and-run-the-simulation) .
2. Pomocí [modulu **Azure Maps interiéru**](../azure-maps/how-to-use-indoor-module.md) můžete vykreslit vaše vnitřní mapy vytvořené v programu Azure Maps Creator.
    1. Zkopírujte kód HTML z [*příkladu: použijte modul vnitřních map*](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) v kurzu pro mapy vnitřních souborů [*: použijte modul Azure Mapsch vnitřních map*](../azure-maps/how-to-use-indoor-module.md) k místnímu souboru.
    1. Nahraďte *klíč předplatného*, *tilesetId* a *statesetID*  v místním souboru HTML pomocí vašich hodnot.
    1. Otevřete tento soubor v prohlížeči.

Oba vzorky posílají teplotu v kompatibilním rozsahu, takže by se měla na mapě zobrazit barva místnosti 121 aktualizace každých 30 sekund.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="Mapa Office znázorňující 121 barevné oranžová":::

## <a name="store-your-maps-information-in-azure-digital-twins"></a>Ukládání informací o mapách do digitálních vláken Azure

Teď, když máte pevně zakódované řešení pro aktualizaci informací o mapách, můžete použít graf digitálních vláken Azure k uložení všech informací potřebných pro aktualizaci vaší vnitřní mapy. To by zahrnovalo ID stateset, mapování ID předplatného a ID funkcí jednotlivých map a umístění. 

Řešení pro tento konkrétní příklad by zahrnovalo aktualizaci každého prostoru na nejvyšší úrovni tak, aby měl ID stateset a mapování ID předplatného, a aktualizace každé místnosti tak, aby měla ID funkce. Tyto hodnoty byste museli nastavit jednou při inicializaci vyzdvojeného grafu a pak tyto hodnoty dotazovat pro každou událost s dvojitou aktualizací.

V závislosti na konfiguraci topologie budete moct uložit tyto tři atributy na různých úrovních, které korelují s členitosti mapy.

## <a name="next-steps"></a>Další kroky

Další informace o správě, upgradu a načítání informací z grafu vláken naleznete v následujících odkazech:

* [*Postupy: Správa digitálních vláken*](./how-to-manage-twin.md)
* [*Postupy: dotazování na nevlákenný graf*](./how-to-query-graph.md)
