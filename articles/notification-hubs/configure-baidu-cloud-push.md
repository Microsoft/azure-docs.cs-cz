---
title: Konfigurace služby Baidu Cloud Push v centru oznámení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat nastavení Baidu pro centrum oznámení Azure.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 759e35ba353f470ea3abc5f5d4182fa2b2ea0e73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71212517"
---
# <a name="deprecated-configure-baidu-cloud-push-settings-for-a-notification-hub-in-the-azure-portal"></a>Zastaralé: Konfigurace nastavení baidu cloudpush pro oznamovací centrum na webu Azure Portal

Tento článek ukazuje, jak nakonfigurovat nastavení Baidu Cloud Push pro centrum oznámení Azure pomocí portálu Azure.

> [!IMPORTANT]
> Tento kurz je zastaralé. 

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho nyní. Další informace najdete [v tématu Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-baidu-cloud-push"></a>Konfigurace nabízených oznámení cloudu Baidu
Následující postup poskytuje kroky ke konfiguraci nastavení Baidu Cloud Push pro centrum oznámení:

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte **baidu (Android China)** v levé nabídce. 
2. Zadejte **klíč api,** který jste získali z konzoly Baidu v projektu push cloud Baidu. 
3. Zadejte **tajný klíč,** který jste získali z konzoly Baidu v projektu push cloud Baidu. 
4. Vyberte **Uložit**. 

    ![Snímek obrazovky s centry oznámení, který zobrazuje konfiguraci Baidu (Android China) pro nabízená oznámení](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

## <a name="next-steps"></a>Další kroky
Kurz s podrobnými pokyny pro odesílání oznámení do Baidu pomocí Azure Notification Hubs a Baidu Cloud Push najdete v tématu [Začínáme s centry oznámení pomocí Baidu](notification-hubs-baidu-china-android-notifications-get-started.md).
