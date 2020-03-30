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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883790"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Chcete-li získat připojovací řetězec služby IoT Hub pro zásadu **registryReadWrite,** postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, kde se nachází vaše centrum, a pak vyberte rozbočovač ze seznamu prostředků.

2. V levém podokně centra vyberte **Zásady sdíleného přístupu**.

3. Ze seznamu zásad vyberte zásadu **registryReadWrite.**

4. V části **Sdílené přístupové klávesy**vyberte ikonu kopírování **pro připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit, jak načíst připojovací řetězec](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Další informace o zásadách a oprávněních sdíleného přístupu centra IoT Hub najdete [v tématu Řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
