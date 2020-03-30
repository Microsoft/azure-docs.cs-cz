---
title: Konfigurace služby nabízených oznámení Microsoftu v centrech oznámení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat nastavení služby Nabízených oznámení Microsoft u centra oznámení Azure.
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
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127327"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Konfigurace nastavení služby Microsoft Push Notification Service (MPNS) na webu Azure Portal

Tento článek ukazuje, jak nakonfigurovat nastavení služby Microsoft Push Notification Service (MPNS) pro centrum oznámení Azure pomocí portálu Azure. 

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho nyní. Další informace najdete [v tématu Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurace služby nabízených oznámení společnosti Microsoft (MPNS)

Následující postup poskytuje postup konfigurace nastavení služby Microsoft Push Notification Service (MPNS) pro centrum oznámení: 

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte windows **phone (MPNS)** v levé nabídce.
1. Povolte neověřená nebo ověřená nabízená oznámení:

   a. Chcete-li povolit neověřená nabízená oznámení, vyberte **možnost Povolit neověřené nabízené** > **uložení**.

      ![Snímek obrazovky, který ukazuje, jak povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Povolení ověřených nabízených oznámení:
      * Na panelu nástrojů vyberte **Nahrát certifikát**.
      * Vyberte ikonu souboru a pak vyberte soubor certifikátu.
      * Zadejte heslo pro certifikát.
      * Vyberte **OK**.
      * Na stránce **Windows Phone (MPNS)** vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky
Kurz s podrobnými pokyny pro odesílání oznámení do zařízení S Windows Phone pomocí center oznámení Azure a služby Nabízených oznámení Microsoftu (MPNS) najdete v tématu [Nabízená oznámení do aplikací pro Windows Phone pomocí center oznámení](notification-hubs-windows-mobile-push-notifications-mpns.md).

