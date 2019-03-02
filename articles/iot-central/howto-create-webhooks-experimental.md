---
title: Vytváření webhooků v pravidlech v Azure IoT Central | Dokumentace Microsoftu
description: Vytváření webhooků v Azure IoT Central automatické zaslání oznámení jiné aplikace při vyvolat pravidla.
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 291307fe8d8911a969e11a8d0e9d5b9078132eda
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2019
ms.locfileid: "57213776"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Vytvoření akce webhooku pro pravidla v Azure IoT Central

*Toto téma platí pro tvůrce a správce.*

Webhooky umožňují propojení aplikace IoT Central pro další aplikace a služby pro vzdálené monitorování a oznámení. Webhooky upozorňují automaticky další aplikace a služby, ke kterým se připojujete pokaždé, když se aktivuje pravidlo v aplikaci pro IoT Central. Pokaždé, když se aktivuje pravidlo aplikace IoT Central odešle požadavek POST do koncového bodu HTTP jiná aplikace. Datová část obsahuje podrobnosti o zařízení a podrobnosti o triggeru pravidlo.

## <a name="set-up-the-webhook"></a>Vytvořit webhook

V tomto příkladu připojíte k RequestBin nechte se informovat pravidla vyvolat pomocí webhooků.

1. Otevřít [RequestBin](http://requestbin.net/).

1. Vytvořte nový RequestBin a zkopírujte **URL přihrádky**.

1. Vytvoření [telemetrie pravidlo](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) nebo [pravidlo události](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Uložte pravidlo a přidejte novou akci.

    ![Vytvoření obrazovky Webhooku](media/howto-create-webhooks-experimental/webhookcreate.png)

1. Zvolte Akce webhooku a zadat zobrazovaný název a vložte adresu URL přihrádky jako adresu URL zpětného volání.

1. Uložte pravidlo.

Když se pravidlo aktivuje, nyní uvidíte novou žádost o joinkind RequestBin.

## <a name="payload"></a>Datová část

Když se aktivuje pravidlo požadavku HTTP POST se provede na adresu URL zpětného volání, obsahující datovou část json s měření, zařízení, pravidla a podrobnosti o aplikaci. Pro pravidlo telemetrická data datové části vypadá takto:

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

Současné době neexistuje žádný programový způsob přihlášení k odběru nebo registraci z těchto webhooky prostřednictvím rozhraní API.

Pokud máte nápady, jak vylepšit tuto funkci, zveřejněte své návrhy k naší [fórum Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak nastavit a použití webhooků, navrhované dalším krokem je prozkoumat [vytváření pracovních postupů v Microsoft Flow](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
