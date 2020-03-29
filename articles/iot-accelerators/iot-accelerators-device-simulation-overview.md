---
title: Přehled simulace zařízení – Azure | Dokumenty společnosti Microsoft
description: Popis akcelerátoru řešení Simulace zařízení a jeho schopností.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: f58eb05ed582cf18157a76f4d637d72a228f4e96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65464874"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Přehled akcelerátorů řešení simulace zařízení

V cloudovém řešení IoT se vaše zařízení připojují ke koncovému bodu cloudu a odesílají telemetrii, jako je teplota, umístění a stav. Vaše řešení spotřebovává tuto telemetrická data, což vám umožní provést akce nebo odvodit poznatky z něj.

Při vývoji řešení IoT jsou experimentování a testování základní součástí tohoto procesu. Simulace je důležitým nástrojem v průběhu tohoto procesu. Pomocí simulace zařízení můžete:

* Rychle zprovoznit prototyp a poté iterovat úpravou simulované chování zařízení za běhu. Tento proces vám umožní prokázat myšlenku před investováním do nákladného hardwaru. Můžete vytvořit vlastní zařízení prostřednictvím webového uživatelského rozhraní pro generování prototypu zařízení během několika sekund.
* Ověření řešení funguje podle očekávání ze zařízení na řešení simulací chování zařízení v reálném světě. Můžete skriptovat složité chování zařízení pomocí JavaScriptu pro generování realistické simulované telemetrie.
* Škálovací test vašeho řešení simulací normálního, špičkového a přesahujícího podmínky špičkového zatížení. Škálovací testy vám taky pomůžou správně zvětšit prostředky Azure potřebné ke spuštění vašeho řešení.

![Ukázková simulace dronů](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Pomocí simulace zařízení můžete definovat modely zařízení pro simulaci skutečných zařízení. Tento model obsahuje formáty zpráv, vlastnosti dvojčete a metody. Můžete také simulovat složité chování zařízení pomocí JavaScriptu.

Můžete spustit simulace pro jedno až tisíce zařízení, která se připojují k libovolnému centru IoT hub. Chcete-li pomoci s testováním, můžete volitelně nasadit centrum IoT spolu se simulací zařízení pro samostatné prostředí.

Simulace zařízení je zdarma. Simulace zařízení se však nasadí do vašeho předplatného Azure v cloudu a spotřebovává prostředky Azure. Pokud simulace zařízení nesplňuje vaše požadavky, [zdrojový kód je také k dispozici na GitHubu,](https://github.com/Azure/device-simulation-dotnet) který můžete kopírovat a upravovat.

## <a name="sample-simulations"></a>Ukázkové simulace

Při nasazení simulace zařízení získáte několik ukázkových simulací a ukázkových zařízení. Tyto ukázky můžete použít k tomu, abyste se dozvěděli, jak používat simulaci zařízení. Chcete-li začít, spusťte [ukázkovou simulaci, která simuluje 10 nákladních vozidel](quickstart-device-simulation-deploy.md). Můžete také [vytvořit vlastní simulaci pomocí jednoho z mnoha ukázkových zařízení .](iot-accelerators-device-simulation-create-simulation.md)

![Konfigurace simulace](media/iot-accelerators-device-simulation-overview/samplesimulation1.png)

## <a name="custom-simulated-devices"></a>Vlastní simulovaná zařízení

Pomocí simulace zařízení můžete [vytvořit vlastní modely zařízení,](iot-accelerators-device-simulation-create-custom-device.md) které se budou používat v simulacích. Můžete například definovat nový model zařízení chladničky, který odesílá telemetrii teploty a vlhkosti. Vlastní simulovaná zařízení jsou ideální pro jednoduché chování zařízení s náhodnými, přírůstkovými nebo dekrementačními hodnotami telemetrie.

![Vytvoření modelu zařízení](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Pokročilá simulovaná zařízení

Když potřebujete větší kontrolu nad hodnotami telemetrie, které zařízení odesílá, můžete použít pokročilý model zařízení. Pokročilé modely zařízení umožňují podporu JavaScriptu pro manipulaci s odeslanými hodnotami telemetrie. Například byste mohli simulovat vnitřní teplotu zaparkovaného auta za horkého slunečného dne - jak se zvyšuje vnější teplota, vnitřní teplota exponenciálně stoupá.

Pokročilé modely zařízení umožňují [vytvářet a nahrávat vlastní modely zařízení,](iot-accelerators-device-simulation-advanced-device.md) které se skládají z definičního souboru zařízení JSON a odpovídajících souborů JavaScriptu.

Pokročilé modely zařízení umožňují:

* Zadejte formát zprávy odeslaný ze zařízení spolu s typy telemetrie.
* Vlastní skriptování slouží ke generování telemetrických hodnot, které udržují stav zařízení v průběhu času.
* Pomocí vlastního skriptování určete, jak simulované zařízení reaguje na metody.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o akcelerátoru řešení Simulace zařízení a jeho možnostech. Chcete-li začít používat akcelerátor řešení, pokračujte na rychlý start:

> [!div class="nextstepaction"]
> [Nasazení a spuštění simulace zařízení IoT v Azure](quickstart-device-simulation-deploy.md)
