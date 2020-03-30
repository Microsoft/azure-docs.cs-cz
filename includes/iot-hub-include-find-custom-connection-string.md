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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050429"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Chcete-li vytvořit zásadu sdíleného přístupu, která uděluje oprávnění ke čtení **služby** a **registru** a získat připojovací řetězec pro tuto zásadu, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte **skupiny prostředků**. Vyberte skupinu prostředků, kde se nachází vaše centrum, a pak vyberte rozbočovač ze seznamu prostředků.

1. V levém podokně centra vyberte **Zásady sdíleného přístupu**.

1. V horní nabídce nad seznamem zásad vyberte **Přidat**.

1. V části **Přidat zásadu sdíleného přístupu**zadejte popisný název zásady, například *serviceAndRegistryRead*. V části **Oprávnění**vyberte **položku Čtení registru** a Připojení **služby**a pak vyberte **Vytvořit**.

    ![Ukázka přidání nové zásady sdíleného přístupu](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Vyberte novou zásadu ze seznamu zásad.

1. V části **Sdílené přístupové klávesy**vyberte ikonu kopírování **pro připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit, jak načíst připojovací řetězec](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu centra IoT Hub najdete [v tématu Řízení přístupu a oprávnění](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
