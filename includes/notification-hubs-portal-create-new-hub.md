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
ms.openlocfilehash: 00b7ffcba876b6abea59cff170331c7413a61d39
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55823129"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v levé nabídce a vyberte **Notification Hubs** v **Mobile** oddílu. Vyberte hvězdičku (`*`) vedle názvu služby a přidejte ji tak **Oblíbené** části v nabídce vlevo. Po **Notification Hubs** se přidá do **Oblíbené**, vyberte v nabídce vlevo. 

      ![Azure portal – vyberte Notification Hubs](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)
3. Na **Notification Hubs** stránce **přidat** na panelu nástrojů. 

      ![Notification Hubs – přidání tlačítka panelu nástrojů](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)
4. Na **centra oznámení** stránce, proveďte následující kroky: 
    1. Zadejte **název** pro oznámení **centra**.  
    2. Zadejte **název** pro **obor názvů**.
    3. Vyberte **umístění** ve kterém chcete vytvořit v centru oznámení. 
    4. Vyberte existující skupinu prostředků nebo zadejte název pro nový **skupiny prostředků**.
    5. Vyberte **Vytvořit**. 

        ![Azure Portal – nastavení vlastností centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Vyberte **oznámení** (ikona zvonku) a vyberte **přejít k prostředku**, nebo aktualizace v seznamu **Notification Hubs** stránky a vyberte vaše Centrum oznámení. 

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)
5. Vyberte ze seznamu **Zásady přístupu**. Poznamenejte si dva připojovací řetězce, které máte k dispozici. Budete je potřebovat později pro zpracování nabízených oznámení.

      >[!IMPORTANT]
      >**NEPOUŽÍVEJTE** ve své aplikaci připojovací řetězec DefaultFullSharedAccessSignature. Ten je určený pouze pro použití na back-endu.
      >

      ![Azure Portal – připojovací řetězce centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
