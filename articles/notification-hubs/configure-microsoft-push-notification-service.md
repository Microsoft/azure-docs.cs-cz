---
title: Konfigurace služby nabízených oznámení Microsoftu v Azure Notification Hubs | Microsoft Docs
description: Naučte se konfigurovat nastavení služby nabízených oznámení Microsoftu pro Centrum oznámení Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 6784331dccd779390ace7dc632d54fe54aedc458
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760987"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Konfigurace nastavení MPNS (Microsoft Push Notification Service) v Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat nastavení MPNS (Microsoft Push Notification Service) pro Centrum oznámení Azure pomocí Azure Portal.

## <a name="prerequisites"></a>Požadavky

Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md).

## <a name="configure-microsoft-push-notification-service-mpns"></a>Konfigurace služby nabízených oznámení Microsoftu (MPNS)

Následující postup popisuje, jak nakonfigurovat nastavení MPNS (Microsoft Push Notification Service) pro Centrum oznámení:

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Windows Phone (MPNS)** .
2. Povolit buď neověřené, nebo ověřené nabízené oznámení:

   a. Pokud chcete povolit neověřená nabízená oznámení, vyberte **Povolit neověřené nabízené** oznámení  >  .

      ![Snímek obrazovky, který ukazuje, jak povolit neověřená nabízená oznámení](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Povolení ověřených nabízených oznámení:
      * Na panelu nástrojů vyberte **Odeslat certifikát**.
      * Vyberte ikonu souboru a potom vyberte soubor certifikátu.
      * Zadejte heslo pro certifikát.
      * Vyberte **OK**.
      * Na stránce **Windows Phone (MPNS)** vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

Kurz s podrobnými pokyny pro doručování oznámení do zařízení Windows Phone pomocí Azure Notification Hubs a MPNS (Microsoft Push Notification Service) najdete v tématu [Posílání nabízených oznámení do Windows Phone aplikací pomocí Notification Hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).
