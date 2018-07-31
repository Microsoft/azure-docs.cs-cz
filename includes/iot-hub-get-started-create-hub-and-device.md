---
title: zahrnout soubor
description: zahrnout soubor
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 05/17/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9f7fee71fb2b80be93d978569791dbb57f137949
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346830"
---
## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Teď máte vytvořené centrum IoT Hub a můžete zjistit důležité informace, které umožňují připojit k centru IoT Hub zařízení a aplikace. 

V navigační nabídce Centrum IoT, otevřete **zásady sdíleného přístupu**. Vyberte **iothubowner** zásady a pak nakopírujte **připojovací řetězec---primární klíč** služby IoT hub. Další informace najdete v tématu [Řízení přístupu ke službě IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Připojovací řetězec iothubowner potřebovat není pro tento kurz nastavení. Ale možná ho budete potřebovat pro některé kurzy nebo jiné scénáře IoT po dokončení tohoto nastavení.

   ![Získání připojovacího řetězce centra IoT](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-your-device-in-the-iot-hub"></a>Registrace zařízení ve službě IoT hub

1. V navigační nabídce Centrum IoT, otevřete **zařízení IoT**, pak klikněte na tlačítko **přidat** k registraci zařízení ve službě IoT hub.

   ![Přidání zařízení IoT zařízení služby IoT hub](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Zadejte **ID zařízení** pro nové zařízení. V ID zařízení se rozlišují malá a velká písmena.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

3. Klikněte na **Uložit**.

4. Po vytvoření zařízení ho otevřete v seznamu **zařízení IoT** podokně.

5. Kopírovat **připojovací řetězec---primární klíč** pro pozdější použití.

   ![Získání připojovacího řetězec zařízení](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
