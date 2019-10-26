---
title: Vytvořit Webhooky na pravidlech v Azure IoT Central | Microsoft Docs
description: Vytvoření webhooků ve službě Azure IoT Central pro automatické upozorňování dalších aplikací, když se pravidla aktivují.
author: viv-liu
ms.author: viviali
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 77c57b8c20b3fd181cdf6ade58e478845304da8d
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950545"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Vytváření akcí Webhooku u pravidel v Azure IoT Central

*Toto téma se týká tvůrců a správců.*

Webhooky umožňují připojit vaši aplikaci IoT Central k ostatním aplikacím a službám pro vzdálené monitorování a oznámení. Webhooky automaticky upozorňují na další aplikace a služby, které připojíte při každém spuštění pravidla ve vaší aplikaci IoT Central. Při aktivaci pravidla pošle vaše aplikace IoT Central požadavek POST koncovému bodu HTTP jiné aplikace. Datová část obsahuje podrobnosti o zařízení a podrobnosti triggeru pravidla.

## <a name="set-up-the-webhook"></a>Nastavení Webhooku

V tomto příkladu se připojíte k RequestBin a dostanete upozornění, když se pravidla aktivují pomocí webhooků.

1. Otevřete [RequestBin](https://requestbin.net/).

1. Vytvořte novou RequestBin a zkopírujte **adresu URL přihrádky**.

1. Vytvořte [pravidlo telemetrie](tutorial-create-telemetry-rules-pnp.md). Uložte pravidlo a přidejte novou akci.

    ![Obrazovka pro vytvoření Webhooku](media/howto-create-webhooks-pnp/webhookcreate.png)

1. Vyberte akci Webhooku a zadejte zobrazovaný název a vložte adresu URL přihrádky jako adresu URL zpětného volání.

1. Uložte pravidlo.

Když se teď pravidlo aktivuje, zobrazí se v RequestBin nový požadavek.

## <a name="payload"></a>Délka

Když se aktivuje pravidlo, požadavek HTTP POST se provede na adresu URL zpětného volání obsahující datovou část JSON s podrobnostmi telemetrie, zařízení, pravidla a aplikace. Datová část by mohla vypadat takto:

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

V současné době neexistuje žádný programový způsob odběru nebo odhlášení odběru těchto webhooků prostřednictvím rozhraní API.

Pokud máte nějaké nápady, jak tuto funkci vylepšit, pošlete své návrhy na [Fórum UserVoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nastavit a používat Webhooky, je doporučeným dalším krokem zkoumání [konfigurace Azure Monitorch skupin akcí](howto-use-action-groups-pnp.md).
