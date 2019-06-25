---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 11/06/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d70544866b9e321d98acd3978da145276e036025
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175106"
---
<!-- put the ## header in the file that includes this file -->

V této části vytvoření identity zařízení v registru identit ve službě IoT hub. Zařízení lze připojit ke službě IoT Hub, pouze pokud má záznam v registru identit. Další informace najdete v části "Registr identit" [Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md) 

1. V navigační nabídce Centrum IoT, otevřete **zařízení IoT**a pak vyberte **přidat** k registraci nového zařízení ve službě IoT hub.

    ![Vytvoření identity zařízení na portálu](./media/iot-hub-include-create-device/create-identity-portal.png)

1. Zadejte název nové zařízení, jako třeba **myDeviceId**a vyberte **Uložit**. Tato akce vytvoří novou identitu zařízení služby IoT hub.

   ![Přidat nové zařízení](./media/iot-hub-include-create-device/create-a-device.png)

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]


1. Po vytvoření zařízení ho otevřete v seznamu **zařízení IoT** podokně. Kopírovat **připojovací řetězec---primární klíč** pro pozdější použití.

    ![Připojovací řetězec zařízení](./media/iot-hub-include-create-device/device-details.png)

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají pouze identity zařízení za účelem bezpečného přístupu ke službě IoT Hub. Ukládají se zde ID zařízení a jejich klíče, které slouží jako zabezpečené přihlašovací údaje, a příznak povoleno/zakázáno, s jehož pomocí můžete zakázat přístup k jednotlivým zařízením. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Další informace najdete v tématu [Příručka vývojáře pro IoT Hub](../articles/iot-hub/iot-hub-devguide-identity-registry.md).
