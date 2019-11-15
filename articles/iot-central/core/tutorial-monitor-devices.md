---
title: 'Kurz: monitorování zařízení v Azure IoT Central'
description: 'Kurz: jako operátor použijte aplikaci Azure IoT Central k monitorování vašich zařízení.'
author: dominicbetts
ms.author: dobett
ms.date: 11/13/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: db3a9a6ba51394a0473f64a2ccede97e5283bba1
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106567"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Kurz: Monitorování zařízení pomocí Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

V tomto kurzu se dozvíte, jak jako operátor můžete pomocí aplikace Microsoft Azure IoT Central monitorovat zařízení a změnit nastavení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přijmout oznámení
> * Prozkoumat problém
> * Oprava problému

## <a name="prerequisites"></a>Požadavky

Než se pustíte do práce, měl by tvůrce dokončit tři kurzy pro tvůrce a vytvořit aplikaci Azure IoT Central:

* [Definování nového typu zařízení](tutorial-define-device-type.md)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md)
* [Přizpůsobení zobrazení operátora](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Přijmout oznámení

Azure IoT Central posílá oznámení o zařízení v podobě e-mailových zpráv. Tvůrce přidal pravidlo, které odešle oznámení, když teplota v připojeném klimatizačním zařízení překročí prahovou hodnotu. Zprávu najdete v e-mailech odeslaných na účet, který tvůrce zvolil pro příjem oznámení.

Otevřete e-mailovou zprávu, kterou jste obdrželi na konci kurzu [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md). V e-mailu vyberte v části **Podrobnosti** odkaz na zařízení vedle **názvu zařízení** :

![E-mail s oznámením výstrah](media/tutorial-monitor-devices/email.png)

V prohlížeči se otevře stránka **Device** (Zařízení) simulovaného zařízení **Connected Air Conditioner-1**, které jste vytvořili v předchozích kurzech:

![Zařízení, které aktivovalo e-mailovou zprávu s oznámením](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Prozkoumat problém

Jako operátor se můžete podívat na informace o zařízení na stránkách **Measurements** (Měření), **Settings** (Nastavení), **Properties** (Vlastnosti), **Rules** (Pravidla) a **Dashboard** (Řídicí panel). Tvůrce přizpůsobil stránku **Dashboard**, aby se na ní zobrazovaly důležité informace o připojeném klimatizačním zařízení.

Zvolte zobrazení **Dashboard** a podívejte se na informace o zařízení.

![Řídicí panel zařízení](media/tutorial-monitor-devices/initial_screen.png)

Graf na řídicím panelu vykresluje teplotní křivku zařízení. Aktuální cílovou teplotu pro zařízení můžete zobrazit také na dlaždici **vlastnosti zařízení** . Rozhodnete se, že cílová teplota je příliš vysoká.

## <a name="remediate-an-issue"></a>Oprava problému

Cílovou teplotu zařízení můžete změnit na stránce **Settings**:

1. Zvolte **Settings**. Změňte nastavení **Set Temperature** (Nastavit teplotu) na hodnotu 75. Zvolte **Update** (Aktualizovat), aby se do zařízení odeslala nová cílová teplota. Když zařízení potvrdí změnu nastavení, stav nastavení se změní na **Synchronizovaný**:

    ![Aktualizace nastavení](media/tutorial-monitor-devices/change_settings.png)

2. Zvolte **Dashboard** a ověřte novou hodnotu nastavení:

    ![Aktualizovaný řídicí panel zařízení](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="nextstepaction"]
> * Přijmout oznámení
> * Prozkoumat problém
> * Oprava problému

Když už víte, jak monitorovat zařízení, dalším navrhovaným krokem je [přidání zařízení](tutorial-add-device.md).