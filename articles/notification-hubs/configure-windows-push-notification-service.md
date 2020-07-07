---
title: Konfigurovat službu nabízených oznámení Windows v Azure Notification Hubs | Microsoft Docs
description: Naučte se konfigurovat nastavení služby nabízených oznámení Windows pro Centrum oznámení Azure.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127313"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Konfigurace nastavení služby nabízených oznámení systému Windows v Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat nastavení WNS (Windows Notification Service) pro Centrum oznámení Azure pomocí Azure Portal.  

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-windows-push-notification-service-wns"></a>Konfigurace služby nabízených oznámení Windows (WNS)

Následující postup popisuje postup konfigurace nastavení WNS (Windows Push Notification Service) pro Centrum oznámení: 

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Windows (WNS)** .
2. Zadejte hodnoty pro **SID balíčku** a **klíč zabezpečení**.
3. Vyberte **Uložit**.

   ![Snímek obrazovky zobrazující pole SID balíčku a klíč zabezpečení](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Další kroky
Kurz s podrobnými pokyny pro doručování oznámení do aplikací Univerzální platforma Windows pomocí Azure Notification Hubs a WNS (Windows Push Notification Service) najdete v tématu [posílání oznámení do aplikací pro UWP pomocí azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md).


