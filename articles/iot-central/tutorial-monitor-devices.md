---
title: Monitorování zařízení v Azure IoT Central | Microsoft Docs
description: Jako operátor můžete svoje zařízení monitorovat pomocí aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 06/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: f68bc211be3cffb61b3381390ae2eeacaffa4213
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960428"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices"></a>Kurz: použití služby Azure IoT Central k monitorování zařízení

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

V tomto kurzu se dozvíte jako operátor, jak pomocí Microsoft Azure IoT Central aplikace monitorovat vaše zařízení a měnit nastavení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Doručení oznámení
> * Prozkoumat problém
> * Napravit problém

## <a name="prerequisites"></a>Požadavky

Než začnete, je potřeba, aby tvůrce dokončil tři kurzy tvůrce, aby vytvořil aplikaci Azure IoT Central:

* [Definovat nový typ zařízení](tutorial-define-device-type.md)
* [Konfigurace pravidel a akcí pro vaše zařízení](tutorial-configure-rules.md)
* [Přizpůsobení zobrazení operátoru](tutorial-customize-operator.md)

## <a name="receive-a-notification"></a>Doručení oznámení

Azure IoT Central odesílá oznámení o zařízeních jako e-mailové zprávy. Tvůrce přidal pravidlo pro odeslání oznámení v případě, že teplota v připojeném zařízení klimatizačního zařízení překročila prahovou hodnotu. Ověřte e-maily odeslané na účet, který tvůrce zvolil pro příjem oznámení.

Na konci kurzu [Konfigurace pravidel a akcí pro vaše zařízení](tutorial-configure-rules.md) otevřete e-mailovou zprávu, kterou jste dostali. V e-mailu vyberte v části **Podrobnosti** odkaz na zařízení vedle **názvu zařízení** :

![E-mail s oznámením výstrah](media/tutorial-monitor-devices/email.png)

V prohlížeči se otevře stránka **zařízení** pro připojené zařízení klimatizace **– 1** simulované zařízení, které jste vytvořili v předchozích kurzech:

![Zařízení, které aktivovalo e-mailovou zprávu s oznámením](media/tutorial-monitor-devices/sourcedevice.png)

## <a name="investigate-an-issue"></a>Prozkoumat problém

Jako operátor můžete zobrazit informace o zařízení na stránkách **měření**, **Nastavení**, **vlastnosti**, **pravidla**a **řídicí panel** . Tvůrce přizpůsobil **řídicí panel** tak, aby zobrazoval důležité informace o připojeném zařízení klimatizace.

Kliknutím na zobrazení **řídicího panelu** zobrazíte informace o zařízení.

![Řídicí panel zařízení](media/tutorial-monitor-devices/initial_screen.png)

Graf na řídicím panelu zobrazuje graf teploty zařízení. Aktuální cílovou teplotu pro zařízení můžete zobrazit také na dlaždici **vlastnosti zařízení** . Rozhodnete se, že je cílová teplota příliš vysoká.

## <a name="remediate-an-issue"></a>Napravit problém

Chcete-li změnit cílovou teplotu zařízení, použijte stránku **Nastavení** :

1. Vyberte **Nastavení**. Změňte **nastavení teploty** na 75. Zvolením možnosti **aktualizovat** odešlete novou cílovou teplotu do zařízení. Když zařízení potvrdí změnu nastavení, stav nastavení se změní na **Synchronizovaný**:

    ![Aktualizovat nastavení](media/tutorial-monitor-devices/change_settings.png)

2. Vyberte **řídicí panel** a ověřte novou hodnotu nastavení:

    ![Aktualizovaný řídicí panel zařízení](media/tutorial-monitor-devices/new_settings.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak:

> [!div class="nextstepaction"]
> * Doručení oznámení
> * Prozkoumat problém
> * Napravit problém

Teď, když už víte, jak zařízení monitorovat, je navržený další krok [Přidání zařízení](tutorial-add-device.md).