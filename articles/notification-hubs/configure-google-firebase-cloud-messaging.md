---
title: Konfigurace zasílání cloudových zpráv Google Firebase v centru oznámení Azure | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nakonfigurovat centrum oznámení Azure pomocí nastavení Cloud Messaging služby Google Firebase.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127473"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurace nastavení Google Firebase pro centrum oznámení na webu Azure Portal

V tomto článku se ukazuje, jak nakonfigurovat nastavení Cloud Messaging (FCM) Služby Google Firebase pro centrum oznámení Azure pomocí portálu Azure.  

## <a name="prerequisites"></a>Požadavky
Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho nyní. Další informace najdete [v tématu Vytvoření centra oznámení Azure na webu Azure Portal](create-notification-hub-portal.md). 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurace zasílání zpráv google firebase cloud (FCM)

Následující postup poskytuje postup konfigurace nastavení služby Google Firebase Cloud Messaging (FCM) pro centrum oznámení: 

1. Na webu Azure Portal na stránce **Centrum oznámení** vyberte v levé nabídce **Google (GCM/FCM).** 
2. Vložte **klíč rozhraní API** pro projekt FCM, který jste uložili dříve. 
3. Vyberte **Uložit**. 

   ![Snímek obrazovky, který ukazuje, jak nakonfigurovat centra oznámení pro Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Další kroky
Kurz s podrobnými pokyny pro odesílání oznámení do zařízení Android pomocí Center oznámení Azure a Cloud Messaging Google Firebase najdete [v tématu Nabízená oznámení na zařízení se systémem Android pomocí center oznámení a Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

