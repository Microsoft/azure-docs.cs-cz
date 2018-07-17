---
title: zahrnout soubor
description: zahrnout soubor
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 03/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 22c1d24042072de5d57d41da379a5fad18180de7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972508"
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

