---
title: 'Tutorial: Monitor your devices in Azure IoT Central'
description: 'Tutorial: As an operator, use your Azure IoT Central application to monitor your devices.'
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: a07a822e863c5ff5d695a20c02bd8d295721efda
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480244"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Kurz: Monitorování zařízení pomocí Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

V tomto kurzu se dozvíte, jak jako operátor můžete pomocí aplikace Microsoft Azure IoT Central monitorovat zařízení a změnit nastavení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příjem oznámení
> * Průzkum problému
> * Opravit problém

## <a name="prerequisites"></a>Předpoklady

Než se pustíte do práce, měl by tvůrce dokončit tři kurzy pro tvůrce a vytvořit aplikaci Azure IoT Central:

* [Definování nového typu zařízení](tutorial-define-device-type.md)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Příjem oznámení

Azure IoT Central posílá oznámení o zařízení v podobě e-mailových zpráv. Tvůrce přidal pravidlo, které odešle oznámení, když teplota v připojeném klimatizačním zařízení překročí prahovou hodnotu. Zprávu najdete v e-mailech odeslaných na účet, který tvůrce zvolil pro příjem oznámení.

Otevřete e-mailovou zprávu, kterou jste obdrželi na konci kurzu [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md). In the email, select the link to the device next to **Device Name** in the **Details** section:

![Alert notification email](media/tutorial-monitor-devices/email.png)

V prohlížeči se otevře stránka **Device** (Zařízení) simulovaného zařízení **Connected Air Conditioner-1**, které jste vytvořili v předchozích kurzech:

![Zařízení, které aktivovalo e-mailovou zprávu s oznámením](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Průzkum problému

Jako operátor se můžete podívat na informace o zařízení na stránkách **Measurements** (Měření), **Settings** (Nastavení), **Properties** (Vlastnosti), **Rules** (Pravidla) a **Dashboard** (Řídicí panel). Tvůrce přizpůsobil stránku **Dashboard**, aby se na ní zobrazovaly důležité informace o připojeném klimatizačním zařízení.

Zvolte zobrazení **Dashboard** a podívejte se na informace o zařízení.

![Řídicí panel zařízení](media/tutorial-monitor-devices/initial_screen.png)

Graf na řídicím panelu vykresluje teplotní křivku zařízení. You can also see the current target temperature for the device in the **Device properties** tile. Rozhodnete se, že cílová teplota je příliš vysoká.

## <a name="remediate-an-issue"></a>Opravit problém

Cílovou teplotu zařízení můžete změnit na stránce **Settings**:

1. Zvolte **Settings** (Nastavení). Změňte nastavení **Set Temperature** (Nastavit teplotu) na hodnotu 75. Zvolte **Update** (Aktualizovat), aby se do zařízení odeslala nová cílová teplota. When the device confirms the settings change, the status of the setting changes to **synced**:

    ![Aktualizace nastavení](media/tutorial-monitor-devices/change_settings.png)

2. Zvolte **Dashboard** a ověřte novou hodnotu nastavení:

    ![Aktualizovaný řídicí panel zařízení](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="nextstepaction"]
> * Příjem oznámení
> * Průzkum problému
> * Opravit problém

Když už víte, jak monitorovat zařízení, dalším navrhovaným krokem je [přidání zařízení](tutorial-add-device.md).
