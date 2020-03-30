---
title: Spuštění více akcí z pravidla Azure IoT Central | Dokumenty společnosti Microsoft
description: Spusťte více akcí z jednoho pravidla IoT Central a vytvořte opakovaně použitelné skupiny akcí, které můžete spustit z více pravidel.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157683"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Seskupit více akcí, které chcete spustit, z jednoho nebo více pravidel

*Tento článek se vztahuje na tvůrce a správce.*

Ve Službě Azure IoT Central vytvoříte pravidla pro spouštění akcí při splnění podmínky. Pravidla jsou založena na telemetrii zařízení nebo událostech. Můžete například upozornit operátora, když teplota zařízení překročí prahovou hodnotu. Tento článek popisuje, jak pomocí *skupin akcí* [Azure Monitor](../../azure-monitor/overview.md) připojit více akcí k pravidlu IoT Central. Skupinu akcí můžete připojit k více pravidlům. [Skupina akcí](../../azure-monitor/platform/action-groups.md) je kolekce předvoleb oznámení definované vlastníkem předplatného Azure.

## <a name="prerequisites"></a>Požadavky

- Aplikace vytvořená pomocí standardního cenového plánu
- Účet Azure a předplatné pro vytváření a správu skupin akcí Azure Monitoru

## <a name="create-action-groups"></a>Vytvoření skupin akcí

Skupiny akcí můžete [vytvářet a spravovat na webu Azure Portal](../../azure-monitor/platform/action-groups.md) nebo pomocí šablony Azure Resource [Manager](../../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Skupina akcí může:

- Posílejte oznámení, jako je e-mail, SMS nebo uskutečně hlasový hovor.
- Spusťte akci, jako je volání webhooku.

Následující snímek obrazovky ukazuje skupinu akcí, která odesílá e-mailová a SMS oznámení a volá webhooku:

![Skupina akcí](media/howto-use-action-groups/actiongroup.png)

Chcete-li použít skupinu akcí v pravidle IoT Central, musí být skupina akcí ve stejném předplatném Azure jako aplikace IoT Central.

## <a name="use-an-action-group"></a>Použití skupiny akcí

Chcete-li použít skupinu akcí v aplikaci IoT Central, nejprve vytvořte pravidlo. Když do pravidla přidáte akci, vyberte **Skupiny akcí sledování Azure**:

![Zvolit akci](media/howto-use-action-groups/chooseaction.png)

Zvolte skupinu akcí z předplatného Azure:

![Výběr skupiny akcí](media/howto-use-action-groups/chooseactiongroup.png)

Vyberte **Uložit**. Skupina akcí se nyní zobrazí v seznamu akcí, které mají být spuštěny při aktivaci pravidla:

![Uložená skupina akcí](media/howto-use-action-groups/savedactiongroup.png)

Následující tabulka shrnuje informace odeslané podporovaným typům akcí:

| Typ akce | Výstupní formát |
| ----------- | -------------- |
| E-mail       | Standardní šablona e-mailu IoT Central |
| SMS         | Výstraha Azure IoT Central: ${applicationName} - "${ruleName}" aktivovaná v "${deviceName}" na ${triggerDate} ${triggerTime} |
| Hlas       | Výstraha Azure I.O.T Central: pravidlo ${ruleName}, aktivované na zařízení ${deviceName}při ${triggerDate} ${triggerTime}, v aplikaci ${applicationName} |
| Webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook užitečné zatížení](howto-create-webhooks.md#payload)}} |

Následující text je příkladem SMS zprávy ze skupiny akcí:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat skupiny akcí s pravidly, je dalším doporučeným krokem naučit se [spravovat zařízení](howto-manage-devices.md).
