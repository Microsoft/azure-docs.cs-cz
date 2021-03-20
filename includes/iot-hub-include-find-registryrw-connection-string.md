---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993264"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Pokud chcete získat připojovací řetězec IoT Hub pro zásady **registryReadWrite** , postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, ve které se nachází vaše centrum, a potom v seznamu prostředků vyberte své centrum.

2. V levém podokně centra vyberte **zásady sdíleného přístupu**.

3. V seznamu zásad vyberte zásady **registryReadWrite** .

4. V části **sdílené přístupové klíče** vyberte ikonu kopírování pro **připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit způsob načtení připojovacího řetězce](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Další informace o zásadách a oprávněních sdíleného přístupu IoT Hub najdete v tématu [řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
