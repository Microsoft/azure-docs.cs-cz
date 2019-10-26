---
title: Spuštění více akcí z pravidla Azure IoT Central | Microsoft Docs
description: Spuštění více akcí z jednoho pravidla IoT Central a vytvoření opakovaně použitelných skupin akcí, které lze spustit z více pravidel.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: cc89033cedcdf185ce7e18c3981045e2a20569f5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952170"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Seskupit více akcí ke spuštění z jednoho nebo více pravidel

*Tento článek se týká tvůrců a správců.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

V Azure IoT Central vytvoříte pravidla ke spouštění akcí, když je splněna podmínka. Pravidla jsou založená na telemetrie a událostech zařízení. Například můžete informovat operátora, když teplota v zařízení překročí prahovou hodnotu. Tento článek popisuje, jak pomocí [Azure Monitorch](../../azure-monitor/overview.md) *skupin akcí* připojit k pravidlu IoT Central více akcí. Skupinu akcí můžete připojit k několika pravidlům. [Skupina akcí](../../azure-monitor/platform/action-groups.md) je kolekce předvoleb oznámení definovaných vlastníkem předplatného Azure.

## <a name="prerequisites"></a>Předpoklady

- Aplikace s průběžnými platbami
- Účet a předplatné Azure pro vytváření a správu Azure Monitorch skupin akcí

## <a name="create-action-groups"></a>Vytvoření skupin akcí

Můžete [vytvořit a spravovat skupiny akcí v Azure Portal](../../azure-monitor/platform/action-groups.md) nebo pomocí [šablony Azure Resource Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Skupina akcí může:

- Odesílat oznámení, jako je e-mail, SMS nebo hlasové volání.
- Spusťte akci, jako je například volání Webhooku.

Na následujícím snímku obrazovky vidíte skupinu akcí, která odesílá e-maily a oznámení SMS a volá Webhook:

![Skupina akcí](media/howto-use-action-groups/actiongroup.png)

Pokud chcete v pravidle IoT Central použít skupinu akcí, musí být skupina akcí ve stejném předplatném Azure jako aplikace IoT Central.

## <a name="use-an-action-group"></a>Použití skupiny akcí

Chcete-li v aplikaci IoT Central použít skupinu akcí, nejprve vytvořte telemetrii nebo pravidlo události. Když do pravidla přidáte akci, vyberte **Azure monitor skupiny akcí**:

![Zvolit akci](media/howto-use-action-groups/chooseaction.png)

Vyberte skupinu akcí z předplatného Azure:

![Zvolit skupinu akcí](media/howto-use-action-groups/chooseactiongroup.png)

Vyberte **Save** (Uložit). Skupina akcí se nyní zobrazí v seznamu akcí, které mají být spuštěny při aktivaci pravidla:

![Uložená skupina akcí](media/howto-use-action-groups/savedactiongroup.png)

Následující tabulka shrnuje informace odesílané do podporovaných typů akcí:

| Typ akce | Výstupní formát |
| ----------- | -------------- |
| E-mail       | Šablona e-mailu Standard IoT Central |
| SMS         | Výstraha Azure IoT Central: $ {applicationName} – "$ {Rule}" aktivované v "$ {název_zařízení}" na $ {triggerDate} $ {triggerTime} |
| Hlas       | Azure I. O. T centrální výstrahu: pravidlo "$ {Rule}" aktivované na zařízení "$ {název_zařízení}" na $ {triggerDate} $ {triggerTime}, v aplikaci $ {applicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook"; "data": {[běžný datový obsah Webhooku](#payload)}} |

Následující text je příkladem zprávy SMS ze skupiny akcí:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>Následující JSON ukazuje příklad datové části akce Webhooku:

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

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s používáním skupin akcí s pravidly, je navržený další krok, kde se dozvíte, jak [spravovat vaše zařízení](howto-manage-devices.md).
