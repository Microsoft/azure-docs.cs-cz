---
title: Vytvořit Webhooky na pravidlech v Azure IoT Central | Microsoft Docs
description: Vytvoření webhooků ve službě Azure IoT Central pro automatické upozorňování dalších aplikací, když se pravidla aktivují.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 5c2bef7f3eb8d6f8d6d78755d839a33556259b65
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72953665"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Vytváření akcí Webhooku u pravidel v Azure IoT Central

*Toto téma se týká tvůrců a správců.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Webhooky umožňují připojit vaši aplikaci IoT Central k ostatním aplikacím a službám pro vzdálené monitorování a oznámení. Webhooky automaticky upozorňují na další aplikace a služby, které připojíte při každém spuštění pravidla ve vaší aplikaci IoT Central. Při aktivaci pravidla pošle vaše aplikace IoT Central požadavek POST koncovému bodu HTTP jiné aplikace. Datová část obsahuje podrobnosti o zařízení a podrobnosti triggeru pravidla.

## <a name="set-up-the-webhook"></a>Nastavení Webhooku

V tomto příkladu se připojíte k RequestBin a dostanete upozornění, když se pravidla aktivují pomocí webhooků.

1. Otevřete [RequestBin](https://requestbin.net/).

1. Vytvořte novou RequestBin a zkopírujte **adresu URL přihrádky**.

1. Vytvořte [pravidlo telemetrie](howto-create-telemetry-rules.md) nebo [pravidlo události](howto-create-event-rules.md). Uložte pravidlo a přidejte novou akci.

    ![Obrazovka pro vytvoření Webhooku](media/howto-create-webhooks/webhookcreate.png)

1. Vyberte akci Webhooku a zadejte zobrazovaný název a vložte adresu URL přihrádky jako adresu URL zpětného volání.

1. Uložte pravidlo.

Když se teď pravidlo aktivuje, zobrazí se v RequestBin nový požadavek.

## <a name="payload"></a>Délka

Při aktivaci pravidla se provede požadavek HTTP POST na adresu URL zpětného volání obsahující datovou část JSON s podrobnostmi o měřeních, zařízeních, pravidlech a aplikacích. V případě pravidla telemetrie vypadá datová část následujícím způsobem:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Známá omezení

V současné době neexistuje žádný programový způsob odběru nebo odhlášení odběru těchto webhooků prostřednictvím rozhraní API.

Pokud máte nějaké nápady, jak tuto funkci vylepšit, pošlete své návrhy na [Fórum UserVoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nastavit a používat Webhooky, je doporučený další krok prozkoumat [vytváření pracovních postupů v Microsoft Flow](howto-add-microsoft-flow.md).
