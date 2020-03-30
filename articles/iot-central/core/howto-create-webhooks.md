---
title: Vytváření webhooků pravidel v Azure IoT Central | Dokumenty společnosti Microsoft
description: Vytvořte webhooky v Azure IoT Central, abyste automaticky upozorňovali ostatní aplikace při spálení pravidel.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158091"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Vytváření akcí webhooku pro pravidla v Azure IoT Central

*Toto téma se týká tvůrců a správců.*

Webhooky umožňují připojit aplikaci IoT Central k jiným aplikacím a službám pro vzdálené monitorování a oznámení. Webhooky automaticky upozorňují ostatní aplikace a služby, které se připojí, kdykoli se ve vaší aplikaci IoT Central aktivuje pravidlo. Vaše aplikace IoT Central odešle požadavek POST do koncového bodu HTTP druhé aplikace při každém aktivaci pravidla. Datová část obsahuje podrobnosti o zařízení a podrobnosti o aktivační události pravidla.

## <a name="set-up-the-webhook"></a>Nastavení webového háčku

V tomto příkladu se připojíte k RequestBin získat upozornění při požáru pravidel pomocí webhooky.

1. Otevřete [requestbin](https://requestbin.net/).

1. Vytvořte nový RequestBin a zkopírujte **adresu URL přihrádky**.

1. Vytvořte [pravidlo telemetrie](tutorial-create-telemetry-rules.md). Uložte pravidlo a přidejte novou akci.

    ![Webhook vytvoření obrazovky](media/howto-create-webhooks/webhookcreate.png)

1. Zvolte akci webhooku a zadejte zobrazovaný název a vložte adresu URL přihrádky jako adresu URL zpětného volání.

1. Uložte pravidlo.

Nyní, když se aktivuje pravidlo, zobrazí se nový požadavek v RequestBin.

## <a name="payload"></a>Datová část

Při aktivaci pravidla je požadavek HTTP POST na adresu URL zpětného volání obsahující datovou část json s podrobnostmi telemetrie, zařízení, pravidla a aplikace. Datová část může vypadat takto:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Známá omezení

V současné době neexistuje žádný programový způsob přihlášení k odběru / odhlášení z těchto webhooků prostřednictvím rozhraní API.

Pokud máte nápady, jak tuto funkci vylepšit, pořiďte své návrhy na našem [uživatelském hlasovém fóru](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nastavit a používat webhooky, je dalším krokem prozkoumat [konfiguraci skupin akcí Azure Monitor](howto-use-action-groups.md).
