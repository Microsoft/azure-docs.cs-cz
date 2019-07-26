---
title: zahrnout soubor
description: zahrnout soubor
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55dc712d323c43849fa3de23c83e29fc348a0f1e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403980"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Pokud chcete vytvořit zásadu sdíleného přístupu, která uděluje oprávnění **ke čtení** **služby** a registru a k získání připojovacího řetězce pro tuto zásadu, postupujte podle těchto kroků:

1. Otevřete Centrum IoT v [Azure Portal](https://portal.azure.com). Nejjednodušší způsob, jak se dostat do služby IoT Hub, je vybrat **skupiny prostředků**, vybrat skupinu prostředků, ve které se nachází vaše centrum IoT, a pak ze seznamu prostředků vybrat centrum IoT.

2. V levém podokně Centra IoT vyberte **zásady sdíleného přístupu**.

3. V horní nabídce nad seznamem zásad vyberte **Přidat**.

4. V podokně **Přidat zásady sdíleného přístupu** zadejte popisný název zásady. například: *serviceAndRegistryRead*. V části **oprávnění**vyberte **čtení z registru** a **Služba připojit**a pak vyberte **vytvořit**.

    ![Ukázat, jak přidat nové zásady sdíleného přístupu](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

5. Zpátky v podokně **zásady sdíleného přístupu** vyberte ze seznamu zásad novou zásadu.

6. V části **sdílené přístupové klíče**vyberte ikonu kopírování pro **připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit způsob načtení připojovacího řetězce](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu IoT Hub najdete v tématu [řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
