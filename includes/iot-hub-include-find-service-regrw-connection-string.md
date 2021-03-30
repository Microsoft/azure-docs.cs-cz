---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "80756931"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Pokud chcete vytvořit zásadu sdíleného přístupu, která uděluje oprávnění k **zápisu** **služby** a zápisu do registru, a k získání připojovacího řetězce pro tuto zásadu, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, ve které se nachází vaše centrum, a potom v seznamu prostředků vyberte své centrum.

1. V levém podokně centra vyberte **zásady sdíleného přístupu**.

1. V horní nabídce nad seznamem zásad vyberte **Přidat**.

1. V části **Přidat zásadu sdíleného přístupu** zadejte popisný název zásady, například *serviceAndRegistryReadWrite*. V části **oprávnění** vyberte **zápis do registru** a **Služba připojit** a pak vyberte **vytvořit**. (Oprávnění **ke čtení registru** je zahrnuté automaticky při výběru **zápisu do registru**.)

    ![Ukázat, jak přidat nové zásady sdíleného přístupu](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. V seznamu zásad vyberte nové zásady.

1. V části **sdílené přístupové klíče** vyberte ikonu kopírování pro **připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit způsob načtení připojovacího řetězce](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu IoT Hub najdete v tématu [řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
