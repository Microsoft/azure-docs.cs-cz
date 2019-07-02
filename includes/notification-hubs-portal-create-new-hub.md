---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509092"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** v nabídce vlevo a pak vyberte **Notification Hubs** v **Mobile** oddílu. Vyberte ikonu hvězdičky vedle názvu služby pro přidání služby **Oblíbené položky** části v nabídce vlevo. Po přidání **Notification Hubs** k **Oblíbené**, vyberte v nabídce vlevo.

      ![Azure portal – vyberte Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na **Notification Hubs** stránce **přidat** na panelu nástrojů.

      ![Notification Hubs – přidání tlačítka panelu nástrojů](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na **centra oznámení** stránce, proveďte následující kroky:

    1. Zadejte název do **centra oznámení**.  

    1. Zadejte název do **vytvořit nový obor názvů**. Obor názvů obsahuje jeden nebo více rozbočovače.

    1. Vyberte hodnotu z **umístění** rozevíracího seznamu. Tato hodnota určuje umístění, ve kterém chcete Centrum vytvořit.

    1. Vyberte existující skupinu prostředků v **skupiny prostředků**, nebo vytvořit název pro novou skupinu prostředků.

    1. Vyberte **Vytvořit**.

        ![Azure Portal – nastavení vlastností centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Vyberte **oznámení** (ikona zvonku) a pak vyberte **přejít k prostředku**. Můžete také aktualizovat sami seznamu **Notification Hubs** stránky a vyberte své centrum.

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Vyberte ze seznamu **Zásady přístupu**. Všimněte si, že k dispozici jsou dva připojovací řetězce. Budete je budete potřebovat později pro zpracování nabízených oznámení.

      >[!IMPORTANT]
      >Proveďte *není* použít **DefaultFullSharedAccessSignature** zásady ve vaší aplikaci. To je určená pro použití v back-endu pouze.
      >

      ![Azure Portal – připojovací řetězce centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
