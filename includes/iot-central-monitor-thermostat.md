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
ms.openlocfilehash: 28f676892967abbd0da63d7a75ea3d164b87ce97
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017469"
---
Jako operátor ve vaší aplikaci Azure IoT Central můžete:

* Zobrazení telemetrie odesílané zařízením na stránce **Přehled** :

    :::image type="content" source="media/iot-central-monitor-thermostat/view-telemetry.png" alt-text="Zobrazení telemetrie zařízení":::

* Zobrazení vlastností zařízení na stránce **o produktu** :

    :::image type="content" source="media/iot-central-monitor-thermostat/about-properties.png" alt-text="Zobrazit vlastnosti zařízení":::

## <a name="customize-the-device-template"></a>Přizpůsobení šablony zařízení

Jako vývojář řešení můžete přizpůsobit šablonu zařízení, kterou IoT Central vytvořit automaticky při připojení termostatu zařízení.

Chcete-li přidat vlastnost cloudu pro uložení názvu zákazníka přidruženého k zařízení:

1. V aplikaci IoT Central na stránce **šablony zařízení** přejděte na šablonu zařízení **termostat** .

1. V šabloně zařízení **termostata** vyberte **vlastnosti cloudu**.

1. Vyberte **Přidat vlastnost cloudu**. Jako **Zobrazovaný název** zadejte *název zákazníka* a jako **schéma** vyberte **řetězec** . Pak vyberte **Uložit**.

Pokud chcete přizpůsobit způsob, jakým se v aplikaci IoT Central zobrazí příkaz **získat Max-Min sestavu** , vyberte v šabloně zařízení **přizpůsobit** . Nahradit **sestavu získat Max-Min** se sestavou *získat stav*. Pak vyberte **Uložit**.

Model **termostatu** obsahuje **cílovou teplotu** vlastnost s možností zápisu, šablona zařízení obsahuje vlastnost Cloud **Customer Name** . Vytvoří zobrazení, které může operátor použít k úpravě těchto vlastností:

1. Vyberte **zobrazení** a pak vyberte dlaždici pro **úpravu zařízení a dat v cloudu** .

1. Jako název formuláře zadejte _vlastnosti_ .

1. Vyberte **cílovou teplotu** a vlastnosti **názvu zákazníka** . Pak vyberte **přidat oddíl**.

1. Uložte provedené změny.

:::image type="content" source="media/iot-central-monitor-thermostat/properties-view.png" alt-text="Zobrazení pro aktualizaci hodnot vlastností":::

## <a name="publish-the-device-template"></a>Publikování šablony zařízení

Předtím, než může operátor zobrazit a použít vámi provedené úpravy, je nutné publikovat šablonu zařízení.

Ze šablony zařízení **termostata** vyberte **publikovat**. Na panelu **aplikace v části publikovat tuto šablonu zařízení** vyberte **publikovat**.

Operátor může nyní použít zobrazení **vlastností** k aktualizaci hodnot vlastností a volání příkazu " **získat zprávu o stavu** " na stránce příkazy zařízení:

* Aktualizovat hodnoty zapisovatelné vlastnosti na stránce **vlastností** :

    :::image type="content" source="media/iot-central-monitor-thermostat/update-properties.png" alt-text="Aktualizace vlastností zařízení":::

* Zavolejte příkazy ze stránky **příkazy** :

    :::image type="content" source="media/iot-central-monitor-thermostat/call-command.png" alt-text="Volání příkazu":::

    :::image type="content" source="media/iot-central-monitor-thermostat/command-response.png" alt-text="Zobrazit odpověď příkazu":::
