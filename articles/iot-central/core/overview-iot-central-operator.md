---
title: Průvodce operátorem Azure IoT Central
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT. Tento článek poskytuje přehled role operátora v IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: b8692973f187743e282de6f8e54a55363cc3105b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104669932"
---
# <a name="iot-central-operator-guide"></a>Průvodce operátorem IoT Central

*Tento článek se týká operátorů.*

IoT Central aplikace umožňuje monitorovat a spravovat miliony zařízení během jejich životního cyklu. Tato příručka je určena pro operátory, které používají aplikaci IoT Central ke správě zařízení IoT.

Operátor:

- Monitoruje a spravuje zařízení připojená k aplikaci.
- Vyřeší problémy a napravuje problémy se zařízeními.
- Zřídí nová zařízení.

## <a name="monitor-and-manage-devices"></a>Monitorování a Správa zařízení

:::image type="content" source="media/overview-iot-central-operator/simulated-telemetry.png" alt-text="Snímek obrazovky zobrazující zobrazení zařízení":::

K monitorování zařízení může operátor použít zobrazení zařízení definovaná tvůrcem řešení jako součást šablony zařízení. Tato zobrazení můžou zobrazovat telemetrie zařízení a hodnoty vlastností. Příkladem je **přehledové** zobrazení zobrazené na předchozím snímku obrazovky.

Pro podrobnější informace může operátor používat skupiny zařízení a integrované analytické funkce. Další informace najdete v tématu [jak pomocí analýzy analyzovat data zařízení](howto-create-analytics.md).

Aby bylo možné spravovat jednotlivá zařízení, může operátor použít zobrazení zařízení k nastavení vlastností zařízení a cloudu a volání příkazů zařízení. Příklady zahrnují zobrazení **Správa zařízení** a **příkazů** na předchozím snímku obrazovky.

Aby bylo možné hromadně spravovat zařízení, může operátor vytvářet a plánovat úlohy. Úlohy mohou aktualizovat vlastnosti a spouštět příkazy na více zařízeních. Další informace najdete v tématu [Vytvoření a spuštění úlohy v aplikaci Azure IoT Central](howto-run-a-job.md).

## <a name="troubleshoot-and-remediate-issues"></a>Řešení potíží a náprava problémů

Operátor zodpovídá za stav aplikace a její zařízení. [Průvodce odstraňováním potíží](troubleshoot-connection.md) pomáhá operátorům diagnostikovat a vyřešit běžné problémy. Operátor může pomocí stránky **zařízení** blokovat zařízení, která se jeví jako nefunkční, dokud se problém nevyřeší.

## <a name="add-and-remove-devices"></a>Přidání a odebrání zařízení

Operátor může do aplikace IoT Central přidat nebo odebrat zařízení buď samostatně, nebo hromadně. Další informace najdete v tématu [Správa zařízení ve vaší aplikaci Azure IoT Central](howto-manage-devices.md).

## <a name="personalize"></a>Přizpůsobení

Operátoři mohou vytvořit přizpůsobené řídicí panely v aplikaci IoT Central, které obsahují odkazy na prostředky, které používají nejčastěji. Další informace najdete v tématu [Správa řídicích panelů](howto-create-personal-dashboards.md#manage-dashboards).

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o použití IoT Central, je doporučeným dalším postupem vyzkoušet rychlé starty, počínaje [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md).
