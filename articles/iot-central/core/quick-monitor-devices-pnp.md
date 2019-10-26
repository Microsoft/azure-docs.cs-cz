---
title: Monitorování zařízení v Azure IoT Central | Microsoft Docs
description: Jako operátor můžete monitorovat zařízení prostřednictvím aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: db229a6c6d8fded8dc2434653f34ec0c727cc761
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72958074"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Rychlý Start: použití IoT Central Azure ke sledování vašich zařízení (funkce ve verzi Preview)

*Tento článek je pro operátory, tvůrce a správce.*

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto rychlém startu se dozvíte, jak pomocí Microsoft Azure IoT Central aplikace monitorovat vaše zařízení a měnit nastavení.

## <a name="prerequisites"></a>Předpoklady

Než začnete, měli byste provést tři předchozí rychlé starty a [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json), [Přidat simulované zařízení do aplikace IoT Central](./quick-create-pnp-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) a [nakonfigurovat pravidla a akce pro vaše zařízení](quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="receive-a-notification"></a>Příjem oznámení

Azure IoT Central posílá oznámení o zařízení v podobě e-mailových zpráv. Tvůrce přidal pravidlo pro odeslání oznámení v případě, že teplota v připojeném zařízení snímače prostředí překročila prahovou hodnotu. Zprávu najdete v e-mailech odeslaných na účet, který tvůrce zvolil pro příjem oznámení.

Otevřete e-mailovou zprávu, kterou jste dostali na konci [pravidla konfigurace a akcí pro](quick-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) rychlý Start zařízení. V e-mailu vyberte odkaz na zařízení:

![E-mail s oznámením výstrah](media/quick-monitor-devices-pnp/email.png)

Zobrazení **řídicího panelu** pro simulované zařízení, které jste vytvořili v předchozích rychlých startech, se otevře v prohlížeči:

![Zařízení, které aktivovalo e-mailovou zprávu s oznámením](media/quick-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Průzkum problému

Jako operátor můžete zobrazit informace o zařízení na stránkách **Přehled**, **vlastnosti senzoru životního prostředí**a **příkazy** . Tvůrce přizpůsobil stránky vlastností **řídicího panelu** a **snímače životního prostředí** , aby zobrazoval důležité informace o připojeném zařízení snímače životního prostředí.

Kliknutím na zobrazení **přehledu** zobrazíte informace o zařízení.

Graf na řídicím panelu vykresluje teplotní křivku zařízení. Rozhodnete se, že je teplota zařízení příliš vysoká.

## <a name="remediate-an-issue"></a>Opravit problém

Chcete-li provést změnu zařízení, použijte stránku **vlastností senzoru životního prostředí** .

Vyberte **vlastnosti senzoru životního prostředí**. Změňte **úroveň jasu** na 10. Pokud chcete zařízení aktualizovat, klikněte na **Uložit** . Když zařízení potvrdí změnu nastavení, stav vlastnosti se změní na **Synchronizovaný**:

![Aktualizace nastavení](media/quick-monitor-devices-pnp/change-settings.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

* Příjem oznámení
* Průzkum problému
* Opravit problém

Teď, když už víte, jak zařízení monitorovat, je navržený další krok:

> [!div class="nextstepaction"]
> [Sestavování a Správa šablon zařízení](howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).
