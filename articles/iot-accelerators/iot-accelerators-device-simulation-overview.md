---
title: Přehled simulace zařízení – Azure | Dokumentace Microsoftu
description: Popis akcelerátor řešení simulace zařízení a její možnosti.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 12/03/2018
ms.author: dobett
ms.openlocfilehash: 69e3708ef4c31e2dd91b5f50baecc46ea129cf33
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346066"
---
# <a name="device-simulation-solution-accelerator-overview"></a>Přehled akcelerátorů řešení simulace zařízení

V cloudové řešení IoT vaše zařízení připojit k koncového bodu cloudu k odesílání telemetrických dat, jako je například teploty, umístění a stav. Řešení využívá tato telemetrie, umožňuje provádět akce a vyvoďte z něj.

Při vývoji řešení IoT, experimentování a testování se základními postupy procesu. Simulace je důležitý nástroj během tohoto procesu. Simulace zařízení vám umožňuje:

* Rychlé zprovoznění prototypu a potom iterovat úpravou simulované zařízení chování v reálném čase. Tento postup umožňuje prokázat si myšlenkou před investic do hardwaru nákladné. Můžete vytvořit vlastní zařízení prostřednictvím webového uživatelského rozhraní k vygenerování prototypu zařízení v řádu sekund.
* Proveďte simulaci chování skutečných zařízení a ověřte, že celé řešení funguje podle očekávání. Můžete používat skripty pro chování komplexní zařízení pomocí JavaScriptu ke generování realistické Simulovaná telemetrická data.
* Škálování otestování řešení simulace ve špičce normální a za podmínek zatížení ve špičce. Testy škálování také vám pomůže s správné velikosti prostředků Azure potřebných ke spuštění vašeho řešení.

![Ukázka dronu simulace](media/iot-accelerators-device-simulation-overview/dronesimulation.png)

Pomocí simulace zařízení můžete definovat modely zařízení pro simulaci fyzických zařízení. Tento model zahrnuje formáty zpráv, dvojčete vlastnosti a metody. Můžete také simulovat chování komplexní zařízení pomocí JavaScriptu.

Můžete spouštět simulace pro tisíce zařízení připojující se k libovolné službě IoT hub. Abyste s testováním, Volitelně můžete nasadit službu IoT hub společně s simulace zařízení pro samostatné prostředí.

Simulace zařízení je zdarma. Simulace zařízení se ale nasadí do vašeho předplatného Azure v cloudu a využívat prostředky Azure. Pokud simulace zařízení nevyhovuje vašim požadavkům [zdrojový kód je také k dispozici na Githubu](https://github.com/Azure/device-simulation-dotnet) můžete zkopírovat a upravit.

## <a name="sample-simulations"></a>Ukázka simulace

Při nasazení simulace zařízení získáte některé ukázkové simulace a ukázková zařízení. Tyto ukázky můžete použít pro další informace o použití simulaci zařízení. Pokud chcete začít, spusťte [ukázkový simulace, která simuluje 10 trucks](quickstart-device-simulation-deploy.md). Můžete také [vytvořit vlastní simulaci pomocí jednoho z mnoha ukázková zařízení, které jsou k dispozici](iot-accelerators-device-simulation-create-simulation.md).

![Konfigurace simulace](media/iot-accelerators-device-simulation-overview/SampleSimulation.png)

## <a name="custom-simulated-devices"></a>Vlastní simulovaných zařízení

Můžete použít k simulaci zařízení [vytvářet vlastní modely](iot-accelerators-device-simulation-create-custom-device.md) pro použití v simulace. Můžete například definovat nový model zařízení lednice, která odesílá telemetrické údaje teploty a vlhkosti. Vlastní Simulovaná zařízení jsou ideální pro jednoduché zařízení chování se náhodný zvyšování hodnoty nebo dekrementace telemetrická data.

![Vytvoření modelu zařízení](media/iot-accelerators-device-simulation-overview/adddevicemodel.png)

## <a name="advanced-simulated-devices"></a>Pokročilé simulovaných zařízení

Pokud potřebujete větší kontrolu nad telemetrické hodnoty, které zařízení odesílá, můžete použít model pokročilé zařízení. Modely pokročilé zařízení povolit podporu jazyka JavaScript pro manipulaci s hodnotami odeslané telemetrie. Například může simulovat vnitřní teploty zaparkovaný automobilu, na horké Optimistický – jako nárůst teploty vnější, vnitřní teploty exponenciálně zvyšuje.

Modely pokročilé zařízení umožňují [vytváříte a nahráváte vlastní modely zařízení](iot-accelerators-device-simulation-advanced-device.md) , které se skládají z definice JSON zařízení souboru a odpovídající soubory jazyka JavaScript.

Modely pokročilé zařízení vám umožní:

* Zadejte formát zprávy odeslané ze zařízení společně s typy telemetrie.
* Vlastní skripty slouží k vytvoření hodnoty telemetrie, které se údržba stavu zařízení v průběhu času.
* Pomocí vlastních skriptů můžete určit, jak v simulovaném zařízení reaguje na metody.

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o akcelerátor řešení simulace zařízení a její možnosti. Abyste mohli začít používat akcelerátor řešení, pokračujte k rychlému startu:

> [!div class="nextstepaction"]
> [Nasadit a spustit simulaci zařízení IoT v Azure](quickstart-device-simulation-deploy.md)
