---
title: Správa zařízení Azure IoT Hub cloudové zasílání zpráv pomocí Průzkumníka cloudu sady Visual Studio | Dokumentace Microsoftu
description: Zjistěte, jak pomocí Průzkumníka cloudu pro sadu Visual Studio můžete monitorovat zařízení, aby zprávy typu cloud a cloud posílat zprávy zařízení ve službě Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/07/2018
ms.author: xshi
ms.openlocfilehash: ab3c02d7207bca70a90df8aa08c73c1484cd635d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61440626"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Pomocí Průzkumníka cloudu sady Visual Studio k odesílání a příjem zpráv mezi zařízením a centrem IoT

![Diagram začátku do konce](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) je užitečná rozšíření sady Visual Studio, která umožňuje zobrazit vaše prostředky Azure, zkoumání jejich vlastností a provádění klíčových vývojářských úkonů ze sady Visual Studio. Tento článek se zaměřuje na tom, jak pomocí Průzkumníka cloudu můžete odesílání a příjem zpráv mezi zařízením a centrem IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-will-learn"></a>Co se dozvíte

Se dozvíte, jak pomocí Průzkumníka cloudu sady Visual Studio pro monitorování zpráv typu zařízení cloud a odesílat zprávy typu cloud zařízení. Zprávy typu zařízení cloud může být data ze senzorů, který shromažďuje vaše zařízení a pak odešle do služby IoT Hub. Zprávy typu cloud zařízení může být příkazy, které služby IoT Hub odesílá do vašeho zařízení. Například blikání kontrolku LED, která je připojená k zařízení.

## <a name="what-you-will-do"></a>Co budete dělat

- Pomocí Průzkumníka cloudu sady Visual Studio pro monitorování zpráv typu zařízení cloud.
- Pomocí Průzkumníka cloudu sady Visual Studio můžete odesílat zprávy typu cloud zařízení.

## <a name="what-you-need"></a>Co potřebujete

- Aktivní předplatné Azure.
- Službu Azure IoT Hub v rámci vašeho předplatného.
- Microsoft Visual Studio 2017 Update 8 nebo novější
- Cloud Explorer komponenty z instalačního programu Visual Studio (zadáno ve výchozím nastavení se sadou Azure)

## <a name="update-cloud-explorer-to-latest-version"></a>Průzkumník cloudu aktualizace na nejnovější verzi

Průzkumník cloudu komponenty z instalačního programu sady Visual Studio podporuje pouze monitorování zpráv typu zařízení cloud a cloud zařízení. Aby bylo možné odesílat zprávy do zařízení nebo v cloudu, stáhněte a nainstalujte nejnovější [Průzkumníka cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-iot-hub"></a>Přihlaste se ke službě IoT Hub

1. V sadě Visual Studio **Průzkumníka cloudu** okna, klikněte na ikonu Správa účtů. Můžete otevřít okno Průzkumníka cloudu ze **zobrazení** > **Průzkumníka cloudu** nabídky.

    ![Klikněte na Správa účtů](media/iot-hub-visual-studio-cloud-device-messaging/click-account-management.png)


2. Klikněte na tlačítko **spravovat účty** v Průzkumníku cloudu.

3. Klikněte na tlačítko **přidat účet...**  v novém okně pro přihlášení do Azure poprvé.

4. Po přihlášení, zobrazí se seznam vašich předplatných Azure. Vyberte předplatná Azure, kterou chcete zobrazit a klikněte na tlačítko **použít**.

5. Rozbalte **předplatného** > **centra IoT hub** > **váš IoT Hub**, zobrazí se seznam zařízení uzlu služby IoT Hub.

    ![Seznam zařízení](media/iot-hub-visual-studio-cloud-device-messaging/device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorování zpráv typu zařízení cloud

Pokud chcete monitorovat zprávy odeslané ze zařízení do služby IoT Hub, postupujte takto:

1. Klikněte pravým tlačítkem na zařízení nebo službu IoT Hub a vyberte **spustit monitorování zpráv D2C**.

    ![Začněte s monitorováním D2C zprávy](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message.png)

2. Sledované zprávy zobrazí ve **služby IoT Hub** podokno výstup.

    ![Zpráva výsledku monitorování D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result.png)

3. Chcete zastavit monitorování, klikněte pravým tlačítkem na jakékoli zařízení nebo službu IoT Hub a vyberte **zastavit monitorování zpráv D2C**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Odeslat zprávu na vaše zařízení ze služby IoT Hub, postupujte podle těchto kroků:

1. Klikněte pravým tlačítkem na zařízení a vyberte **poslat zprávu C2D**.

    ![Odeslat zprávu C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message.png)

2. Zadejte zprávu vstupního pole.

3. Výsledky se zobrazí v **služby IoT Hub** podokno výstup.

    ![Odeslat C2D výsledek zprávy](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result.png)

## <a name="next-steps"></a>Další postup

Jste zjistili, jak pro monitorování zpráv typu zařízení cloud a odesílání zpráv typu cloud zařízení mezi zařízení IoT a Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]