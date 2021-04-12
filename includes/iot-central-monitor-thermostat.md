---
title: zahrnout soubor
description: zahrnout soubor
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 77fdaf297fff0e145b1dd53908887bc14f9d3f14
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491056"
---
<!-- All needs updating -->
Jako operátor ve vaší aplikaci Azure IoT Central můžete:

* Zobrazení telemetrie odesílané dvěma komponentami termostatu na stránce **Přehled** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Zobrazení telemetrie zařízení":::

* Prohlédněte si vlastnosti zařízení na stránce **o produktu** . Tato stránka zobrazuje vlastnosti ze součásti informace o zařízení a dvou přítermostatcích komponent:

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Zobrazit vlastnosti zařízení":::

## <a name="customize-the-device-template"></a>Přizpůsobení šablony zařízení

Jako vývojář řešení můžete přizpůsobit šablonu zařízení, kterou IoT Central vytvořit automaticky, když je zařízení řadiče teploty připojené.

Chcete-li přidat vlastnost cloudu pro uložení názvu zákazníka přidruženého k zařízení:

1. V IoT Central aplikaci přejděte na šablonu zařízení **řadiče pro teploty** na stránce **šablony zařízení** .

1. V šabloně zařízení **řadiče teploty** vyberte **vlastnosti cloudu**.

1. Vyberte **Přidat vlastnost cloudu**. Jako **Zobrazovaný název** zadejte *název zákazníka* a jako **schéma** vyberte **řetězec** . Pak vyberte **Uložit**.

Chcete-li přizpůsobit způsob zobrazení příkazů **Get Max-Min sestavy** ve vaší aplikaci IoT Central:

1. V šabloně zařízení vyberte **přizpůsobit** .

1. Pro **getMaxMinReport (thermostat1)** nahraďte *Get Max-Min Report.* se sestavou *získat thermostat1 stav*.

1. Pro **getMaxMinReport (thermostat2)** nahraďte *Get Max-Min Report.* se sestavou *získat thermostat2 stav*.

1. Vyberte **Uložit**.

Chcete-li upravit způsob, jakým se v aplikaci IoT Central zobrazí cílová vlastnost s možností zápisu na **cílovou teplotu** :

1. V šabloně zařízení vyberte **přizpůsobit** .

1. V případě **targetTemperature (thermostat1)** nahraďte *cílovou teplotu* *cílovou teplotou (1)*.

1. V případě **targetTemperature (thermostat2)** nahraďte *cílovou teplotu* *cílovou teplotou (2)*.

1. Vyberte **Uložit**.

Termostat komponenty v modelu **teplotního řadiče** zahrnují vlastnost s **cílovou teplotou** zapisovatelnou, šablona zařízení obsahuje vlastnost Cloud **Customer Name** . Vytvoří zobrazení, které může operátor použít k úpravě těchto vlastností:

1. Vyberte **zobrazení** a pak vyberte dlaždici pro **úpravu zařízení a dat v cloudu** .

1. Jako název formuláře zadejte _vlastnosti_ .

1. Vyberte **cílovou teplotu (1)**,  **cílovou teplotu (2)** a vlastnosti **názvu zákazníka** . Pak vyberte **přidat oddíl**.

1. Uložte provedené změny.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Zobrazení pro aktualizaci hodnot vlastností":::

## <a name="publish-the-device-template"></a>Publikování šablony zařízení

Předtím, než může operátor zobrazit a použít vámi provedené úpravy, je nutné publikovat šablonu zařízení.

Ze šablony zařízení **termostata** vyberte **publikovat**. Na panelu **aplikace v části publikovat tuto šablonu zařízení** vyberte **publikovat**.

Operátor může nyní použít zobrazení **vlastností** k aktualizaci hodnot vlastností a volání příkazů s názvem **získat thermostat1 stav sestava** a **získat zprávu o stavu thermostat2** na stránce příkazy zařízení:

* Aktualizovat hodnoty zapisovatelné vlastnosti na stránce **vlastností** :

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Aktualizace vlastností zařízení":::

* Zavolejte příkazy ze stránky **příkazy** :

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Volání příkazu":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Zobrazit odpověď příkazu":::
