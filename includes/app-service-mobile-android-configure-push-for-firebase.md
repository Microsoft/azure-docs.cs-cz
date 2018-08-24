---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: bb03e4b5b04a0272d8fa9b032da5adb50878b620
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42811569"
---
1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **Procházet vše** > **App Services**a potom klikněte na back-endu Mobile Apps. V části **nastavení**, klikněte na tlačítko **App Service Push**a potom klikněte na název vašeho centra oznámení.
2. Přejděte na **Google (GCM)**, zadejte **klíč serveru** hodnotu, která jste získali z Firebase v předchozím postupu a potom klikněte na **Uložit**.

    ![Nastavte klíč rozhraní API na portálu](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Back-endu Mobile Apps je nyní nakonfigurováno pro použití služby Firebase Cloud Messaging. To umožňuje odesílat nabízená oznámení do vaší aplikace běžící na zařízení s Androidem pomocí centra oznámení.
