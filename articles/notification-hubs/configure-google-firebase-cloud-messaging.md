---
title: Konfigurace zasílání zpráv v cloudu Google Firebase v Azure Notification Hubs | Microsoft Docs
description: Naučte se konfigurovat centrum oznámení Azure s nastavením cloudového zasílání zpráv Google Firebase.
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
ms.openlocfilehash: 0da103c11e2412108535ca322917632f5d95559d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87760834"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Konfigurace nastavení Google Firebase pro Centrum oznámení v Azure Portal

V tomto článku se dozvíte, jak nakonfigurovat nastavení FCM (Google Firebase Cloud Messaging) pro Centrum oznámení Azure pomocí Azure Portal.  

## <a name="prerequisites"></a>Požadavky

Pokud jste centrum oznámení ještě nevytvořili, vytvořte ho hned teď. Další informace najdete v tématu [vytvoření centra oznámení Azure v Azure Portal](create-notification-hub-portal.md).

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Konfigurace zasílání zpráv v cloudu Google Firebase (FCM)

Následující postup popisuje kroky pro konfiguraci nastavení FCM (Google Firebase Cloud Messaging) pro Centrum oznámení:

1. V Azure Portal na stránce **centra oznámení** vyberte **Google (GCM/FCM)** v nabídce vlevo.
2. Vložte **klíč rozhraní API** pro projekt FCM, který jste předtím uložili.
3. Vyberte **Uložit**.

   ![Snímek obrazovky, který ukazuje, jak nakonfigurovat Notification Hubs pro Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Další kroky

Kurz s podrobnými pokyny pro posílání oznámení na zařízení s Androidem pomocí služby Azure Notification Hubs a zasílání zpráv v cloudu Google Firebase najdete v tématu [Posílání nabízených oznámení na zařízení s Androidem pomocí Notification Hubs a Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).
