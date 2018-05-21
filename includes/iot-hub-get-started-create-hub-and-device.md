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
ms.openlocfilehash: f8cd78e63099f864c5fc54b6268f6e558d738626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-create-hub](iot-hub-create-hub.md)]

Teď, když jste vytvořili centrum IoT, vyhledejte důležité informace, které používáte pro připojení zařízení a aplikací do služby IoT hub. 

V navigační nabídce centra IoT, otevřete **zásady sdíleného přístupu**.
Vyberte **iothubowner** zásady a pak nakopírujte **připojovací řetězec, primární klíč** služby IoT hub. Další informace najdete v tématu [Řízení přístupu ke službě IoT Hub](../articles/iot-hub/iot-hub-devguide-security.md).

   > [!NOTE] 
   > Není nutné tento iothubowner připojovací řetězec pro tento kurz nastavení. Ale musíte ho pro některé kurzy nebo různé scénáře IoT po dokončení tohoto nastavení.

   ![Získání připojovacího řetězce centra IoT](./media/iot-hub-get-started-create-hub-and-device/create-iot-hub5.png)

## <a name="register-a-device-in-the-iot-hub-for-your-device"></a>Registrace zařízení v centru IoT pro vaše zařízení

1. V navigační nabídce centra IoT, otevřete **zařízení IoT**, pak klikněte na tlačítko **přidat** registrovat zařízení ve službě IoT hub.

   ![Přidání zařízení v zařízení IoT služby IoT hub](./media/iot-hub-get-started-create-hub-and-device/create-identity-portal.png)

2. Zadejte **ID zařízení** pro nové zařízení. V ID zařízení se rozlišují malá a velká písmena.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

4. Klikněte na **Uložit**.
5. Po vytvoření zařízení otevřete zařízení v seznamu **zařízení IoT** podokně.
6. Kopírování **připojovací řetězec, primární klíč** pro pozdější použití.

   ![Získání připojovacího řetězec zařízení](./media/iot-hub-get-started-create-hub-and-device/device-connection-string.png)
