---
title: Spuštění více akcí z pravidla Azure IoT Central | Microsoft Docs
description: Spuštění více akcí z jednoho pravidla IoT Central a vytvoření opakovaně použitelných skupin akcí, které lze spustit z více pravidel.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80157683"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Seskupit více akcí ke spuštění z jednoho nebo více pravidel

*Tento článek se týká tvůrců a správců.*

V Azure IoT Central vytvoříte pravidla ke spouštění akcí, když je splněna podmínka. Pravidla jsou založená na telemetrie a událostech zařízení. Například můžete informovat operátora, když teplota zařízení překročí prahovou hodnotu. Tento článek popisuje, jak pomocí [Azure Monitorch](../../azure-monitor/overview.md) *skupin akcí* připojit k pravidlu IoT Central více akcí. Skupinu akcí můžete připojit k několika pravidlům. [Skupina akcí](../../azure-monitor/platform/action-groups.md) je kolekce předvoleb oznámení definovaných vlastníkem předplatného Azure.

## <a name="prerequisites"></a>Požadavky

- Aplikace vytvořená pomocí standardního cenového plánu
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

Chcete-li v aplikaci IoT Central použít skupinu akcí, vytvořte nejprve pravidlo. Když do pravidla přidáte akci, vyberte **Azure monitor skupiny akcí**:

![Zvolit akci](media/howto-use-action-groups/chooseaction.png)

Vyberte skupinu akcí z předplatného Azure:

![Zvolit skupinu akcí](media/howto-use-action-groups/chooseactiongroup.png)

Vyberte **Uložit**. Skupina akcí se nyní zobrazí v seznamu akcí, které mají být spuštěny při aktivaci pravidla:

![Uložená skupina akcí](media/howto-use-action-groups/savedactiongroup.png)

Následující tabulka shrnuje informace odesílané do podporovaných typů akcí:

| Typ akce | Výstupní formát |
| ----------- | -------------- |
| E-mail       | Šablona e-mailu Standard IoT Central |
| SMS         | Výstraha Azure IoT Central: $ {applicationName} – "$ {Rule}" aktivované v "$ {název_zařízení}" na $ {triggerDate} $ {triggerTime} |
| Hlas       | Azure I. O. T centrální výstrahu: pravidlo "$ {Rule}" aktivované na zařízení "$ {název_zařízení}" na $ {triggerDate} $ {triggerTime}, v aplikaci $ {applicationName} |
| Webhook     | {"schemaId": "AzureIoTCentralRuleWebhook"; "data": {[běžný datový obsah Webhooku](howto-create-webhooks.md#payload)}} |

Následující text je příkladem zprávy SMS ze skupiny akcí:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s používáním skupin akcí s pravidly, je navržený další krok, kde se dozvíte, jak [spravovat vaše zařízení](howto-manage-devices.md).
