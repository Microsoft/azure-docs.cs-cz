---
title: Správa zasílání zpráv o zařízeních Azure IoT Hub pomocí VS Cloud Exploreru
description: Naučte se používat Cloud Explorer pro Visual Studio ke sledování zařízení na cloudové zprávy a odesílat zprávy z cloudu do zařízení v Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74079481"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Odesílání a přijímání zpráv mezi zařízením a službou IoT Hub pomocí Průzkumníka Cloud pro Visual Studio

![Diagram koncového bodu](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) je užitečné rozšíření Sady Visual Studio, které umožňuje zobrazit prostředky Azure, kontrolovat jejich vlastnosti a provádět klíčové akce vývojáře z visual studia. Tento článek se zaměřuje na to, jak pomocí Průzkumníka Cloud odesílat a přijímat zprávy mezi zařízením a centrem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Co se naučíte

V tomto článku se dozvíte, jak pomocí Průzkumníka Cloud pro Visual Studio sledovat zprávy mezi zařízeními a odesílat zprávy z cloudu. Zprávy mezi zařízeními a cloudem mohou být data ze senzorů, která vaše zařízení shromažďuje a pak odesílá do služby IoT Hub. Zprávy mezi cloudy a zařízením mohou být příkazy, které vaše služba IoT Hub odesílá do vašeho zařízení. Můžete například blikat led diodu připojenou k zařízení.

## <a name="what-you-do"></a>Co děláte

V tomto článku provést následující úkoly:

- Pomocí Průzkumníka Cloud pro Visual Studio můžete monitorovat zprávy mezi zařízeními a cloudy.

- Pomocí Průzkumníka Cloud pro Visual Studio můžete odesílat zprávy z cloudu na zařízení.

## <a name="what-you-need"></a>Co potřebujete

Potřebujete následující předpoklady:

- Aktivní předplatné Azure.

- Azure IoT Hub v rámci vašeho předplatného.

- Aktualizace Microsoft Visual Studio 2017 Aktualizace 9 nebo novější. Tento článek používá [Visual Studio 2019](https://www.visualstudio.com/vs/).

- Součást Cloud Explorer z Instalační služby Visual Studia, která je ve výchozím nastavení vybraná s úlohami Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizace Průzkumníka Cloud na nejnovější verzi

Komponenta Průzkumníka cloudu z Instalační služby Visual Studia pro Visual Studio 2017 podporuje jenom monitorování zpráv mezi zařízeními a cloudem. Chcete-li používat Visual Studio 2017, stáhněte a nainstalujte nejnovější [Průzkumníka Cloud .](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)

## <a name="sign-in-to-access-your-hub"></a>Přihlášení pro přístup k rozbočovači

Chcete-li získat přístup k rozbočovači, postupujte takto:

1. V Visual Studiu vyberte **Zobrazit** > **Průzkumníka cloudu** a otevřete Cloud Explorer.

1. Výběrem ikony Správa účtu zobrazíte svá předplatná.

    ![Ikona Správy účtu](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Pokud jste přihlášení k Azure, zobrazí se vaše účty. Pokud se chcete do Azure přihlásit poprvé, zvolte **Přidat účet**.

1. Vyberte předplatná Azure, která chcete použít, a zvolte **Použít**.

1. Rozbalte své předplatné a pak rozbalte **služby IoT Hubs**.  Pod každým rozbočovačem uvidíte svá zařízení pro tento rozbočovač.

    ![Seznam zařízení](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Sledování zpráv mezi zařízeními a cloudy

Chcete-li sledovat zprávy odeslané ze zařízení do centra IoT Hub, postupujte takto:

1. Klikněte pravým tlačítkem myši na centrum IoT hub nebo zařízení a vyberte **Spustit sledování zprávy D2C**.

    ![Spustit sledování zprávy D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. Sledované zprávy se zobrazí v části **Výstup**.

    ![Sledování výsledku zprávy D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Chcete-li sledování zastavit, klikněte pravým tlačítkem myši na libovolné centrum IoT Hub nebo zařízení a vyberte **možnost Zastavit sledování zprávy D2C**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete do zařízení poslat zprávu z centra IoT Hub, postupujte takto:

1. Klikněte pravým tlačítkem myši na zařízení a vyberte **Odeslat zprávu C2D**.

1. Zadejte zprávu do vstupního pole.

    ![Odeslat zprávu C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Výsledky se zobrazí v části **Výstup**.

    ![Odeslat výsledek zprávy C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak monitorovat zprávy mezi zařízeními a cloudy a odesílat zprávy z cloudu na zařízení mezi zařízením IoT a službou Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]