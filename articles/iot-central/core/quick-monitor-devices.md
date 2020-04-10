---
title: Úvodní příručka – monitorování zařízení v Azure IoT Central
description: Jako operátor se dozvíte, jak používat aplikaci Azure IoT Central ke sledování vašich zařízení.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: 1dec52bbf1435cd7e363edf111f769d3e2cffb6a
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998922"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Úvodní příručka: Pomocí Azure IoT Central ke sledování vašich zařízení

*Tento článek je pro operátory, tvůrce a správce.*

Tento rychlý start vám jako operátorovi ukáže, jak používat aplikaci Microsoft Azure IoT Central ke sledování vašich zařízení a změně nastavení.

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste dokončit tři předchozí rychlé starty [Vytvoření aplikace Azure IoT Central](./quick-deploy-iot-central.md), Přidání [simulovaného zařízení do aplikace IoT Central](./quick-create-simulated-device.md) a Konfigurace pravidel a akcí pro vaše [zařízení](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Přijmout oznámení

Azure IoT Central posílá oznámení o zařízení v podobě e-mailových zpráv. Tvůrce přidal pravidlo pro odeslání oznámení, když teplota v senzoru připojeného zařízení překročila prahovou hodnotu. Zprávu najdete v e-mailech odeslaných na účet, který tvůrce zvolil pro příjem oznámení.

Otevřete e-mailovou zprávu, kterou jste obdrželi na konci rychlého startu [Konfigurace pravidel a akcí pro zařízení.](quick-configure-rules.md) V e-mailu vyberte odkaz na zařízení:

![E-mail s upozorněním](media/quick-monitor-devices/email.png)

Otevře se v prohlížeči zobrazení **Přehled** simulovaných zařízení, které jste vytvořili při předchozích rychlých startech:

![Zařízení, které aktivovalo e-mailovou zprávu s oznámením](media/quick-monitor-devices/dashboard.png)

## <a name="investigate-an-issue"></a>Průzkum problému

Jako operátor můžete zobrazit informace o zařízení v zobrazení **Přehled**, **Informace**a **Příkazy.** Tvůrce vytvořil zobrazení **Spravovat zařízení,** abyste mohli upravovat informace o zařízení a nastavovat vlastnosti zařízení.

Graf na řídicím panelu vykresluje teplotní křivku zařízení. Rozhodnete se, že teplota zařízení je příliš vysoká.

## <a name="remediate-an-issue"></a>Oprava problému

Chcete-li provést změnu zařízení, použijte stránku **Spravovat zařízení.**

Změňte **rychlost ventilátoru** na 500, aby se zařízení ochladilo. Zvolte **Uložit,** chcete-li zařízení aktualizovat. Když zařízení potvrdí změnu nastavení, stav vlastnosti se změní na **synchronizované**:

![Aktualizace nastavení](media/quick-monitor-devices/change-settings.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Přijmout oznámení
* Průzkum problému
* Oprava problému

Nyní, když nyní víte, že chcete sledovat své zařízení, je navrhovaným dalším krokem:

> [!div class="nextstepaction"]
> [Vytvořte a spravujte šablonu zařízení](howto-set-up-template.md).
