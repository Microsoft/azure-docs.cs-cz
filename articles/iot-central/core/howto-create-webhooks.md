---
title: Vytvořit Webhooky na pravidlech v Azure IoT Central | Microsoft Docs
description: Vytvoření webhooků ve službě Azure IoT Central pro automatické upozorňování dalších aplikací, když se pravidla aktivují.
author: viv-liu
ms.author: viviali
ms.date: 04/03/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: b2ac4bbf1457144d23a91c4e83b554b3ee806119
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87337224"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Vytváření akcí webhooků u pravidel v Azure IoT Central

*Toto téma se týká tvůrců a správců.*

Webhooky umožňují připojit vaši aplikaci IoT Central k ostatním aplikacím a službám pro vzdálené monitorování a oznámení. Webhooky automaticky upozorňují na další aplikace a služby, které připojíte při každém spuštění pravidla ve vaší aplikaci IoT Central. Při aktivaci pravidla pošle vaše aplikace IoT Central požadavek POST koncovému bodu HTTP jiné aplikace. Datová část obsahuje podrobnosti o zařízení a podrobnosti triggeru pravidla.

## <a name="set-up-the-webhook"></a>Nastavení Webhooku

V tomto příkladu se připojíte k RequestBin a dostanete upozornění, když se pravidla aktivují pomocí webhooků.

1. Otevřete [RequestBin](https://requestbin.net/).

1. Vytvořte novou RequestBin a zkopírujte **adresu URL přihrádky**.

1. Vytvořte [pravidlo telemetrie](tutorial-create-telemetry-rules.md). Uložte pravidlo a přidejte novou akci.

    ![Obrazovka pro vytvoření Webhooku](media/howto-create-webhooks/webhookcreate.png)

1. Vyberte akci Webhooku a zadejte zobrazovaný název a vložte adresu URL přihrádky jako adresu URL zpětného volání.

1. Uložte pravidlo.

Když se teď pravidlo aktivuje, zobrazí se v RequestBin nový požadavek.

## <a name="payload"></a>Délka

Když se aktivuje pravidlo, požadavek HTTP POST se provede na adresu URL zpětného volání obsahující datovou část JSON s podrobnostmi telemetrie, zařízení, pravidla a aplikace. Datová část by mohla vypadat takto:

```json
{
    "timestamp": "2020-04-06T00:20:15.06Z",
    "action": {
        "id": "<id>",
        "type": "WebhookAction",
        "rules": [
            "<rule_id>"
        ],
        "displayName": "Webhook 1",
        "url": "<callback_url>"
    },
    "application": {
        "id": "<application_id>",
        "displayName": "Contoso",
        "subdomain": "contoso",
        "host": "contoso.azureiotcentral.com"
    },
    "device": {
        "id": "<device_id>",
        "etag": "<etag>",
        "displayName": "MXChip IoT DevKit - 1yl6vvhax6c",
        "instanceOf": "<device_template_id>",
        "simulated": true,
        "provisioned": true,
        "approved": true,
        "cloudProperties": {
            "City": {
                "value": "Seattle"
            }
        },
        "properties": {
            "deviceinfo": {
                "firmwareVersion": {
                    "value": "1.0.0"
                }
            }
        },
        "telemetry": {
            "<interface_instance_name>": {
                "humidity": {
                    "value": 47.33228889360127
                }
            }
        }
    },
    "rule": {
        "id": "<rule_id>",
        "displayName": "Humidity monitor"
    }
}
```
Pokud pravidlo monitoruje agregovanou telemetrii v časovém intervalu, datová část bude obsahovat jiný oddíl telemetrie.

```json
{
    "telemetry": {
        "<interface_instance_name>": {
            "Humidity": {
                "avg": 39.5
            }
        }
    }
}
```

## <a name="data-format-change-notice"></a>Upozornění na změnu formátu dat

Pokud máte vytvořený a uložený jeden Webhook před **3. dubna 2020**, budete muset Webhook odstranit a vytvořit nový Webhook. Je to proto, že starší Webhooky používají starší formát datové části, který bude v budoucnu už zastaralý.

### <a name="webhook-payload-format-deprecated-as-of-3-april-2020"></a>Datová část Webhooku (formát se už nepoužívá od 3. dubna 2020)

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
        "displayName": "x - Store Analytics Checkout",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Známá omezení

V současné době neexistuje žádný programový způsob odběru nebo odhlášení odběru těchto webhooků prostřednictvím rozhraní API.

Pokud máte nějaké nápady, jak tuto funkci vylepšit, pošlete své návrhy na našem [fóru hlasového uživatele](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak nastavit a používat Webhooky, je doporučeným dalším krokem zkoumání [konfigurace Azure Monitorch skupin akcí](howto-use-action-groups.md).
