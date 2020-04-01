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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67509092"
---
1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V levé nabídce vyberte **Všechny služby** a v části **Mobilní** vyberte **Centra oznámení.** Vyberte ikonu hvězdičky vedle názvu služby a přidejte službu do oddílu **OBLÍBENÉ položky** v levé nabídce. Po přidání **centra oznámení** do **oblíbených**položek je vyberte v levé nabídce.

      ![Portál Azure – výběr center oznámení](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Na stránce **Centra oznámení** vyberte **Přidat** na panelu nástrojů.

      ![Centra oznámení – tlačítko Přidat panel nástrojů](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Na stránce **Centrum oznámení** postupujte takto:

    1. Zadejte název do **centra oznámení**.  

    1. Zadejte název v pole **Vytvoření nového oboru názvů**. Obor názvů obsahuje jeden nebo více rozbočovačů.

    1. Vrozené pole **Umístění** vyberte hodnotu. Tato hodnota určuje umístění, ve kterém chcete vytvořit rozbočovač.

    1. Vyberte existující skupinu prostředků ve **skupině prostředků**nebo vytvořte název pro novou skupinu prostředků.

    1. Vyberte **Vytvořit**.

        ![Azure Portal – nastavení vlastností centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Vyberte **Oznámení** (ikona zvonku) a pak vyberte **Přejít na zdroj**. Seznam můžete také aktualizovat na stránce **Centra oznámení** a vybrat centrum.

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Vyberte ze seznamu **Zásady přístupu**. Všimněte si, že dva připojovací řetězce jsou k dispozici. Budete je později potřebovat ke zpracování nabízených oznámení.

      >[!IMPORTANT]
      >*Nepoužívejte* zásady **DefaultFullSharedAccessSignature** ve vaší aplikaci. To je chtěl být použit v zadní části pouze.
      >

      ![Azure Portal – připojovací řetězce centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
