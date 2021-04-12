---
title: Přehled simulace zařízení – Azure | Microsoft Docs
description: Popis akcelerátoru řešení pro simulaci zařízení a jeho schopnosti.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 979643c76b8bded51e30232228b7fe7485a1f53b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057673"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Přehled akcelerátorů řešení simulace zařízení

V cloudovém řešení IoT se vaše zařízení připojí ke koncovému bodu cloudu, aby mohla odesílat telemetrii, jako je například teplota, umístění a stav. Vaše řešení využívá tuto telemetrii a umožňuje provádět akce nebo odvodit z nich přehledy.

Při vývoji řešení IoT jsou experimenty a testy zásadními součástmi tohoto procesu. Simulace je důležitým nástrojem v celém rámci tohoto procesu. Díky simulaci zařízení můžete:

* Rychle získáte prototypy v provozu a potom iterovat úpravou simulovaného chování zařízení. Tento proces vám umožní Ukázat si nápad před investováním do nákladného hardwaru. Pomocí webového uživatelského rozhraní můžete vytvořit vlastní zařízení, která vygenerují prototyp zařízení během několika sekund.
* Ověří, jestli řešení funguje podle očekávání ze zařízení až po řešení simulací chování zařízení v reálném světě. Můžete skriptovat složité chování zařízení pomocí JavaScriptu pro generování reálné simulované telemetrie.
* Škálujte své řešení pomocí simulace normálního zatížení, špičky a dalších podmínek zátěže ve špičce. Testy škálování vám také pomůžou zajistit správnou velikost prostředků Azure potřebných ke spuštění vašeho řešení.

![Ukázka simulace pomocí dronů](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Díky simulaci zařízení můžete definovat modely zařízení pro simulaci skutečných zařízení. Tento model obsahuje formáty zpráv, dvojitých vlastností a metod. Můžete také simulovat složité chování zařízení pomocí JavaScriptu.

Můžete spustit simulace pro jednu až tisíce zařízení připojujících se ke službě IoT Hub. Pro lepší testování můžete volitelně nasadit IoT Hub spolu s simulací zařízení pro samostatné prostředí.

Simulace zařízení je zdarma. Simulace zařízení se ale nasadí do vašeho předplatného Azure v cloudu a spotřebovávají prostředky Azure. Pokud simulace zařízení nevyhovuje vašim požadavkům, [je zdrojový kód dostupný taky na GitHubu](https://github.com/Azure/device-simulation-dotnet) , abyste ho mohli kopírovat a upravovat.

## <a name="sample-simulations"></a>Ukázkové simulace

Když nasadíte simulaci zařízení, zobrazí se několik ukázkových simulací a ukázkových zařízení. Pomocí těchto ukázek můžete zjistit, jak používat simulaci zařízení. Chcete-li začít, spusťte [ukázkovou simulaci](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md). Můžete také [vytvořit vlastní simulaci pomocí jedné z mnoha dostupných ukázkových zařízení](iot-accelerators-device-simulation-create-simulation.md).

![Konfigurace simulace](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Vlastní simulovaná zařízení

Simulaci zařízení můžete použít k [Vytvoření vlastních modelů zařízení](iot-accelerators-device-simulation-create-custom-device.md) pro použití v simulacích. Můžete například definovat nový model zařízení chladnička, který bude odesílat telemetrii teploty a vlhkosti. Vlastní simulovaná zařízení jsou ideální pro jednoduché chování zařízení s náhodnými, přírůstky nebo snížením hodnot telemetrie.

![Vytvoření modelu zařízení](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Pokročilá simulovaná zařízení

Pokud potřebujete větší kontrolu nad hodnotami telemetrie, které zařízení odesílá, můžete použít pokročilý model zařízení. Pokročilé modely zařízení umožňují podpoře JavaScriptu manipulovat s odeslanými hodnotami telemetrie. Můžete například simulovat vnitřní teplotu zaparkovaných automobilů za horkou Slunečné den – jako vnější nárůst teploty se vnitřní teplota zvyšuje exponenciálně.

Pokročilé modely zařízení vám umožní [vytvářet a nahrávat vlastní modely zařízení](iot-accelerators-device-simulation-advanced-device.md) , které se skládají ze souboru definice zařízení JSON a odpovídajících souborů JavaScriptu.

Pokročilé modely zařízení vám umožní:

* Zadejte formát zprávy odesílaný ze zařízení spolu s typy telemetrie.
* Pomocí vlastního skriptování můžete generovat hodnoty telemetrie, které udržují stav zařízení v čase.
* Pomocí vlastního skriptování určíte, jak simulované zařízení reaguje na metody.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o akcelerátoru řešení pro simulaci zařízení a jeho schopnostech. Pokud chcete nasadit akcelerátor řešení, navštivte úložiště GitHub:

> [!div class="nextstepaction"]
> [Nasazení a spuštění simulace zařízení IoT v Azure](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)
