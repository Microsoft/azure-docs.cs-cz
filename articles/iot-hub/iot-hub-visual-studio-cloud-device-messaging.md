---
title: Použití Průzkumníka cloudu VS ke správě zasílání zpráv o zařízeních Azure IoT Hub
description: Naučte se používat Cloud Explorer pro Visual Studio k monitorování zpráv ze zařízení do cloudu a posílání cloudu na zprávy zařízení v Azure IoT Hub.
author: shizn
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: xshi
ms.openlocfilehash: c56bb7030b2ebc12e3afc24e2d8cb29ce2dda0bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "74079481"
---
# <a name="use-cloud-explorer-for-visual-studio-to-send-and-receive-messages-between-your-device-and-iot-hub"></a>Použití Průzkumníka cloudu pro Visual Studio k posílání a přijímání zpráv mezi zařízením a IoT Hub

![Komplexní diagram](./media/iot-hub-visual-studio-cloud-device-messaging/e-to-e-diagram.png)

[Průzkumník cloudu](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS) je užitečnou příponou sady Visual Studio, která umožňuje zobrazit prostředky Azure, kontrolovat jejich vlastnosti a provádět klíčové vývojářské akce v rámci sady Visual Studio. Tento článek se zaměřuje na použití Průzkumníka cloudu k posílání a přijímání zpráv mezi zařízením a vaším rozbočovačem.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="what-you-learn"></a>Co se naučíte

V tomto článku se dozvíte, jak používat Průzkumníka cloudu pro Visual Studio k monitorování zpráv ze zařízení do cloudu a k posílání zpráv z cloudu na zařízení. Zprávy ze zařízení do cloudu můžou být senzorem dat, která vaše zařízení shromažďuje a odesílá IoT Hub. Zprávy z cloudu na zařízení můžou být příkazy, které váš IoT Hub posílá do vašeho zařízení. Například blikající indikátory LED, který je připojen k vašemu zařízení.

## <a name="what-you-do"></a>Co dělat

V tomto článku provedete následující úlohy:

- Pomocí Průzkumníka cloudu pro Visual Studio můžete monitorovat zprávy ze zařízení do cloudu.

- Použijte Průzkumníka cloudu pro Visual Studio k posílání zpráv z cloudu na zařízení.

## <a name="what-you-need"></a>Co budete potřebovat

Potřebujete následující požadavky:

- Musíte mít aktivní předplatné Azure.

- IoT Hub Azure v rámci vašeho předplatného.

- Microsoft Visual Studio 2017 Update 9 nebo novější. Tento článek používá [Visual Studio 2019](https://www.visualstudio.com/vs/).

- Součást Průzkumníka cloudu z Instalační program pro Visual Studio, která je ve výchozím nastavení vybrána s úlohou Azure.

## <a name="update-cloud-explorer-to-latest-version"></a>Aktualizovat Průzkumníka cloudu na nejnovější verzi

Součást Průzkumníka cloudu z Instalační program pro Visual Studio pro Visual Studio 2017 podporuje pouze monitorování zpráv ze zařízení do cloudu a z cloudu na zařízení. Pokud chcete použít Visual Studio 2017, Stáhněte a nainstalujte si nejnovější [Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS).

## <a name="sign-in-to-access-your-hub"></a>Přihlaste se, abyste měli přístup ke svému centru.

Chcete-li získat přístup k centru, postupujte podle následujících kroků:

1. V aplikaci Visual Studio vyberte **Zobrazit**  >  **Průzkumníka cloudu** a otevřete Průzkumníka cloudu.

1. Vyberte ikonu Správa účtů a zobrazte si Vaše předplatná.

    ![Ikona správy účtů](media/iot-hub-visual-studio-cloud-device-messaging/account-management-icon.png)

1. Pokud jste přihlášeni k Azure, zobrazí se vaše účty. Pokud se chcete přihlásit k Azure poprvé, vyberte **Přidat účet**.

1. Vyberte předplatná Azure, která chcete použít, a klikněte na **použít**.

1. Rozbalte své předplatné a potom rozbalte položku **centra IoT**.  V rámci každého centra si můžete zobrazit vaše zařízení pro toto centrum.

    ![Seznam zařízení](media/iot-hub-visual-studio-cloud-device-messaging/hub-device-list.png)

## <a name="monitor-device-to-cloud-messages"></a>Monitorování zpráv ze zařízení do cloudu

Pokud chcete monitorovat zprávy odesílané ze svého zařízení do vašeho IoT Hub, postupujte podle následujících kroků:

1. Klikněte pravým tlačítkem na IoT Hub nebo zařízení a vyberte **Spustit monitorování zpráva D2C**.

    ![Spustit monitorování zprávy D2C](media/iot-hub-visual-studio-cloud-device-messaging/start-monitoring-d2c-message-vs2019.png)

1. Monitorované zprávy se zobrazí v části **výstup**.

    ![Monitorování výsledku zprávy D2C](media/iot-hub-visual-studio-cloud-device-messaging/monitor-d2c-message-result-vs2019.png)

1. Pokud chcete sledování zastavit, klikněte pravým tlačítkem na libovolné IoT Hub nebo zařízení a vyberte **zastavit monitorování zpráva D2C**.

## <a name="send-cloud-to-device-messages"></a>Odesílání zpráv z cloudu do zařízení

Pokud chcete poslat zprávu z IoT Hub do zařízení, postupujte podle těchto kroků:

1. Klikněte pravým tlačítkem na zařízení a vyberte **Odeslat zprávu C2D**.

1. Do vstupního pole zadejte zprávu.

    ![Poslat zprávu C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-test.png)

    Výsledky se zobrazí v části **výstup**.

    ![Odeslat výsledek zprávy C2D](media/iot-hub-visual-studio-cloud-device-messaging/send-c2d-message-result-vs2019.png)

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak monitorovat zprávy ze zařízení do cloudu a jak odesílat zprávy z cloudu na zařízení mezi zařízením IoT a službou Azure IoT Hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]