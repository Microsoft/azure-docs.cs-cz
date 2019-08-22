---
title: Monitorování zařízení v Azure IoT Central | Microsoft Docs
description: Jako operátor můžete monitorovat zařízení prostřednictvím aplikace Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/06/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: cf50fd80bdbce5895bd1da39700d1c6e4cdcc230
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878114"
---
# <a name="tutorial-use-azure-iot-central-to-monitor-your-devices-preview-features"></a>Kurz: Monitorování zařízení pomocí služby Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu se dozvíte, jak jako operátor můžete pomocí aplikace Microsoft Azure IoT Central monitorovat zařízení a změnit nastavení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přijmout oznámení
> * Prozkoumat problém
> * Opravit problém

## <a name="prerequisites"></a>Požadavky

Než se pustíte do práce, měl by tvůrce dokončit tři kurzy pro tvůrce a vytvořit aplikaci Azure IoT Central:

* [Definování nového typu zařízení](tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Přidat zařízení](tutorial-add-device-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
* [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

## <a name="receive-a-notification"></a>Přijmout oznámení

Azure IoT Central posílá oznámení o zařízení v podobě e-mailových zpráv. Tvůrce přidal pravidlo pro odeslání oznámení v případě, že teplota v připojeném zařízení snímače prostředí překročila prahovou hodnotu. Zprávu najdete v e-mailech odeslaných na účet, který tvůrce zvolil pro příjem oznámení.

Otevřete e-mailovou zprávu, kterou jste obdrželi na konci kurzu [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). V e-mailu vyberte odkaz na zařízení:

![E-mail s oznámením výstrah](media/tutorial-monitor-devices-pnp/email.png)

Zobrazení **řídicího panelu** pro simulované zařízení, které jste vytvořili v předchozích kurzech, se otevře v prohlížeči:

![Zařízení, které aktivovalo e-mailovou zprávu s oznámením](media/tutorial-monitor-devices-pnp/dashboard.png)

## <a name="investigate-an-issue"></a>Prozkoumat problém

Jako operátor můžete zobrazit informace o zařízení na **řídicím panelu**, **vlastnostech snímače životního prostředí**a stránkách **příkazů** . Tvůrce přizpůsobil stránky vlastností **řídicího panelu** a snímače **životního prostředí** , aby zobrazoval důležité informace o připojeném zařízení snímače životního prostředí.

Zvolte zobrazení **Dashboard** a podívejte se na informace o zařízení.

Graf na řídicím panelu vykresluje teplotní křivku zařízení. Aktuální cílovou teplotu pro zařízení můžete zobrazit také na dlaždici **vlastnosti zařízení** . Rozhodnete se, že cílová teplota je příliš vysoká.

## <a name="remediate-an-issue"></a>Opravit problém

Chcete-li provést změnu zařízení, použijte stránku **vlastností senzoru prostředí** :

1. Vyberte **vlastnosti senzoru životního prostředí**. Změňte **úroveň jasu** na 10. Pokud chcete zařízení aktualizovat, klikněte na **Uložit** . Když zařízení potvrdí změnu nastavení, stav vlastnosti se změní na **Synchronizovaný**:

    ![Aktualizovat nastavení](media/tutorial-monitor-devices-pnp/change-settings.png)

2. Vyberte **řídicí panel** a ověřte novou hodnotu vlastnosti:

    ![Aktualizovaný řídicí panel zařízení](media/tutorial-monitor-devices-pnp/new-settings.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Přijmout oznámení
* Prozkoumat problém
* Opravit problém

Teď, když už víte, jak zařízení monitorovat, je navržený další krok:

> [!div class="nextstepaction"]
> [Konfigurace pravidel a akcí pro zařízení](tutorial-configure-rules.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)