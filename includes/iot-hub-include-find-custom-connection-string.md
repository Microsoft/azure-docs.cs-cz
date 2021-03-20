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
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "70050429"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Pokud chcete vytvořit zásadu sdíleného přístupu, která uděluje oprávnění **ke čtení** **služby** a registru a k získání připojovacího řetězce pro tuto zásadu, postupujte podle těchto kroků:

1. V [Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, ve které se nachází vaše centrum, a potom v seznamu prostředků vyberte své centrum.

1. V levém podokně centra vyberte **zásady sdíleného přístupu**.

1. V horní nabídce nad seznamem zásad vyberte **Přidat**.

1. V části **Přidat zásadu sdíleného přístupu** zadejte popisný název zásady, například *serviceAndRegistryRead*. V části **oprávnění** vyberte **čtení z registru** a **Služba připojit** a pak vyberte **vytvořit**.

    ![Ukázat, jak přidat nové zásady sdíleného přístupu](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. V seznamu zásad vyberte nové zásady.

1. V části **sdílené přístupové klíče** vyberte ikonu kopírování pro **připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit způsob načtení připojovacího řetězce](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu IoT Hub najdete v tématu [řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
