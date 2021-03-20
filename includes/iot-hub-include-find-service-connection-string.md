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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "69558500"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Pro získání připojovacího řetězce IoT Hub pro zásady **služby** použijte následující postup:

1. V [Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, ve které se nachází vaše centrum, a potom v seznamu prostředků vyberte své centrum.

1. V levém podokně Centra IoT vyberte **zásady sdíleného přístupu**.

1. V seznamu zásad vyberte zásadu **služby** .

1. V části **sdílené přístupové klíče** vyberte ikonu kopírování pro **připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit způsob načtení připojovacího řetězce](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu IoT Hub najdete v tématu [řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
