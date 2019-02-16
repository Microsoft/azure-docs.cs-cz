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
ms.openlocfilehash: a5d29e77f6ba10ed3069cb3f5a3c8089f49c237d
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313768"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v levé nabídce a vyberte **Notification Hubs** v **Mobile** oddílu. Vyberte hvězdičku (`*`) vedle názvu služby a přidejte ji tak **Oblíbené** části v nabídce vlevo. Po **Notification Hubs** se přidá do **Oblíbené**, vyberte v nabídce vlevo. 

      ![Azure portal – vyberte Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Na **Notification Hubs** stránce **přidat** na panelu nástrojů. 

      ![Notification Hubs – přidání tlačítka panelu nástrojů](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Na **centra oznámení** stránce, proveďte následující kroky: 
    1. Zadejte **název** pro oznámení **centra**.  
    2. Zadejte **název** pro **obor názvů**. Obor názvů obsahuje jeden nebo více rozbočovače. 
    3. Vyberte **umístění** ve kterém chcete vytvořit v centru oznámení. 
    4. Vyberte existující skupinu prostředků nebo zadejte název pro nový **skupiny prostředků**.
    5. Vyberte **Vytvořit**. 

        ![Azure Portal – nastavení vlastností centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Vyberte **Oznámení** (ikona zvonku) a vyberte **Přejít k prostředku**. Můžete také aktualizovat v seznamu **Notification Hubs** stránky a vyberte vaše Centrum oznámení. 

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Vyberte ze seznamu **Zásady přístupu**. Poznamenejte si dva připojovací řetězce, které máte k dispozici. Budete je potřebovat později pro zpracování nabízených oznámení.

      >[!IMPORTANT]
      >**NEPOUŽÍVEJTE** ve své aplikaci připojovací řetězec DefaultFullSharedAccessSignature. Ten je určený pouze pro použití na back-endu.
      >

      ![Azure Portal – připojovací řetězce centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
