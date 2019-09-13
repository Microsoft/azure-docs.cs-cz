---
title: Konfigurace nabízených oznámení cloudu Baidu v Azure Notification Hubs | Microsoft Docs
description: Naučte se konfigurovat nastavení Baidu pro Centrum oznámení Azure.
services: notification-hubs
author: jwargo
manager: patniko
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: jowargo
ms.openlocfilehash: 9feb5f66c5ae979a239c3349b2b1347ad307ce49
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70884645"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Zastaralé Konfigurace nastavení Baidu cloudového nabízeného oznámení pro Centrum oznámení v Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat nastavení služby Baidu Cloud push pro Centrum oznámení Azure pomocí Azure Portal.

> [!IMPORTANT]
> Tento kurz je zastaralý. 

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurace nabízených oznámení cloudu Baidu
Následující postup popisuje, jak nakonfigurovat nastavení služby Baidu Cloud push pro Centrum oznámení:

1. V Azure Portal na stránce **centra oznámení** vyberte v nabídce vlevo možnost **Baidu (Android China)** . 
2. Zadejte **klíč rozhraní API** , který jste získali z konzoly Baidu v projektu nabízená oznámení cloudu Baidu. 
3. Zadejte **tajný klíč** , který jste získali z konzoly Baidu v projektu nabízená oznámení cloudu Baidu. 
4. Vyberte **Uložit**. 

    ![Snímek obrazovky Notification Hubs zobrazující konfiguraci Baidu (Android China) pro nabízená oznámení](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Další postup
Kurz s podrobnými pokyny pro doručování oznámení do Baidu pomocí Azure Notification Hubs a Baidu Cloud push najdete v tématu Začínáme [s Notification Hubs pomocí Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
