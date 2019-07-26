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
ms.openlocfilehash: b6ca43616a2e7e7611b122bce5c95084e1fd5012
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68402379"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Pro získání připojovacího řetězce IoT Hub pro zásady **služby** použijte následující postup:

1. Otevřete Centrum IoT v [Azure Portal](https://portal.azure.com).  Nejjednodušší způsob, jak se dostat do služby IoT Hub, je vybrat **skupiny prostředků**, vybrat skupinu prostředků, ve které se nachází vaše centrum IoT, a pak ze seznamu prostředků vybrat centrum IoT.

2. V levém podokně Centra IoT vyberte **zásady sdíleného přístupu**.

3. V seznamu zásad vyberte zásadu **služby** .

4. V části **sdílené přístupové klíče**vyberte ikonu kopírování pro **připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit způsob načtení připojovacího řetězce](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu IoT Hub najdete v tématu [řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
