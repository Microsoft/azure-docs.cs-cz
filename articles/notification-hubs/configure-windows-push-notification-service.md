---
title: Konfigurace služby nabízených oznámení windows v centrech oznámení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat nastavení služby nabízených oznámení Windows pro centrum oznámení Azure.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127313"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Konfigurace nastavení služby nabízených oznámení Windows na webu Azure Portal

Tento článek ukazuje, jak nakonfigurovat nastavení služby Windows Notification Service (WNS) pro centrum oznámení Azure pomocí portálu Azure.  

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho nyní. Další informace najdete [v tématu Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurace služby nabízených oznámení systému Windows (WNS)

Následující postup poskytuje postup konfigurace nastavení služby NABÍZENÝCH Oznámení systému Windows (WNS) pro centrum oznámení: 

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte **windows (WNS)** v levé nabídce.
2. Zadejte hodnoty pro **balíček SID** a **bezpečnostní klíč**.
3. Vyberte **Uložit**.

   ![Snímek obrazovky s okny Sid balíčku a bezpečnostního klíče](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Další kroky
Kurz s podrobnými pokyny pro odesílání oznámení do aplikací univerzální platformy Windows pomocí center oznámení Azure a služby nabízených oznámení Windows (WNS) najdete v tématu [Odesílání oznámení do aplikací UPW pomocí center oznámení Azure](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


