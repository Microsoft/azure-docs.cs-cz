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
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756931"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Chcete-li vytvořit zásadu sdíleného přístupu, která uděluje oprávnění **služby připojit** a **zápis do registru** a získat připojovací řetězec pro tuto zásadu, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, kde se nachází vaše centrum, a pak vyberte rozbočovač ze seznamu prostředků.

1. V levém podokně centra vyberte **Zásady sdíleného přístupu**.

1. V horní nabídce nad seznamem zásad vyberte **Přidat**.

1. V části **Přidat zásadu sdíleného přístupu**zadejte popisný název zásady, například *serviceAndRegistryReadWrite*. V části **Oprávnění**vyberte **možnost Zápis registru** a Připojení **služby**a pak vyberte **Vytvořit**. (Oprávnění **ke čtení registru** je zahrnuto automaticky, když vyberete zápis **registru**.)

    ![Ukázka přidání nové zásady sdíleného přístupu](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Vyberte novou zásadu ze seznamu zásad.

1. V části **Sdílené přístupové klávesy**vyberte ikonu kopírování **pro připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit, jak načíst připojovací řetězec](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu centra IoT Hub najdete [v tématu Řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
