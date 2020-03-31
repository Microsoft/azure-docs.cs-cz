---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558500"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Chcete-li získat připojovací řetězec služby IoT Hub pro zásady **služby,** postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, kde se nachází vaše centrum, a pak vyberte rozbočovač ze seznamu prostředků.

1. V levém podokně centra IoT hub vyberte **Zásady sdíleného přístupu**.

1. Ze seznamu zásad vyberte zásady **služby.**

1. V části **Sdílené přístupové klávesy**vyberte ikonu kopírování **pro připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit, jak načíst připojovací řetězec](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu centra IoT Hub najdete [v tématu Řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
