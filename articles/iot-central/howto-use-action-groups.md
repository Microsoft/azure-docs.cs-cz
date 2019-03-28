---
title: Spouštění více akcí z pravidla protokolu Azure IoT Central | Dokumentace Microsoftu
description: Spouštění více akcí z jednoho pravidla IoT Central a vytvořit znovu použitelných skupin akcí, které můžete spustit z více pravidel.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522905"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Seskupit více akcí pro spuštění z jednoho nebo více pravidel

*Tento článek se týká pro tvůrce a správce.*

V Azure IoT Central můžete vytvořit pravidla ke spouštění akcí, když je splněna podmínka. Pravidla jsou založeny na telemetrii zařízení nebo událostí. Operátor může například upozornit, pokud teplota v zařízení překračuje prahovou hodnotu. Tento článek popisuje způsob použití [Azure Monitor](../azure-monitor/overview.md) *skupiny akcí* připojit více akcí pro pravidlo IoT Central. Skupiny akcí můžete připojit k více pravidel. [Skupiny akcí](../azure-monitor/platform/action-groups.md) je kolekce předvolby oznamování určené vlastník předplatného Azure.

## <a name="prerequisites"></a>Požadavky

- Aplikace s průběžnými platbami
- Účet Azure a předplatného můžete vytvořit a spravovat skupiny akcí Azure Monitor

## <a name="create-action-groups"></a>Vytvoření skupin akcí

Je možné [vytváření a Správa skupin akcí na webu Azure Portal](../azure-monitor/platform/action-groups.md) nebo se [šablony Azure Resource Manageru](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Skupiny akcí můžete:

- Odesílání oznámení, jako je e-mail, SMS, nebo nastavit jako hlasové volání.
- Spusťte akce, jako je zavolání webhooku.

Následující snímek obrazovky ukazuje skupiny akcí, která odesílá e-mailu a oznámení SMS a volá webhook:

![Skupina akcí](media/howto-use-action-groups/actiongroup.png)

Pokud chcete použít skupiny akcí v pravidle IoT Central, musí být skupina akcí ve stejném předplatném Azure jako aplikace IoT Central.

## <a name="use-an-action-group"></a>Použít skupiny akcí

Pomocí skupiny akcí ve vaší aplikaci IoT Central, nejprve vytvořte pravidlo telemetrická data nebo události. Když přidáte akci pravidla, vyberte **skupiny akcí Azure Monitor**:

![Vybrat akci](media/howto-use-action-groups/chooseaction.png)

Vyberte skupinu akcí z vašeho předplatného Azure:

![Vyberte skupinu akcí](media/howto-use-action-groups/chooseactiongroup.png)

Vyberte **Uložit**. Skupina akcí se teď zobrazí v seznamu akcí spustit, když se aktivuje pravidlo:

![Uložit skupinu akcí](media/howto-use-action-groups/savedactiongroup.png)

Následující tabulka shrnuje informace o typy podporovaných akcí:

| Typ akce | Výstupní formát |
| ----------- | -------------- |
| Email       | Standardní šablona e-mailu IoT Central |
| SMS         | Upozornění Azure IoT Central: ${applicationName} - "{ruleName}" na "${název_zařízení}" $ ${triggerDate} {triggerTime} aktivuje |
| Hlas       | Upozornění Azure I.O.T střed: pravidlo "${ruleName}" aktivuje na zařízení "${název_zařízení}" na {triggerTime}, ${triggerDate} $ $ aplikace {applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[datová část webhooku regulární](#payload)}} |

Následující text je ukázková zpráva SMS z skupiny akcí:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Následující kód JSON ukazuje datovou část akce webhooku příkladu:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Další postup

Teď, když jste zjistili, jak použít skupiny akcí s pravidly, navrhované dalším krokem je další způsob [spravujte svá zařízení](howto-manage-devices.md).
