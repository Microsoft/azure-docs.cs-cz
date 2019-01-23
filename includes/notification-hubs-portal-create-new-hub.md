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
ms.openlocfilehash: b68fa345d4772134c30ce8b8b559f98113a0496f
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54453086"
---
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyberte **Vytvořit prostředek** > **Mobilní zařízení** > **Centrum oznámení**.

      ![Azure Portal – vytvoření centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)

3. Do pole **Centrum oznámení** zadejte jedinečný název. Vyberte svou **Oblast**, **Předplatné** a **Skupinu prostředků** (pokud už nějakou máte).

      Pokud ještě obor názvů služby Service Bus nemáte, můžete použít výchozí název vytvořený na základě názvu centra (pokud je název oboru názvů dostupný).

      Pokud už máte obor názvů služby Service Bus, ve kterém chcete centrum vytvořit, postupujte následovně:

    a. V poli **Obor názvů** vyberte odkaz **Vybrat existující**.

    b. Vyberte **Vytvořit**.

    ![Azure Portal – nastavení vlastností centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Vyberte **Oznámení** (ikona zvonku) a vyberte **Přejít k prostředku**.

      ![Portál Azure Portal – oznámení -> Přejít k prostředku](./media/notification-hubs-portal-create-new-hub/notification-go-to-resource.png)
5. Vyberte ze seznamu **Zásady přístupu**. Poznamenejte si dva připojovací řetězce, které máte k dispozici. Budete je potřebovat později pro zpracování nabízených oznámení.

      >[!IMPORTANT]
      >**NEPOUŽÍVEJTE** ve své aplikaci připojovací řetězec DefaultFullSharedAccessSignature. Ten je určený pouze pro použití na back-endu.
      >

      ![Azure Portal – připojovací řetězce centra oznámení](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
