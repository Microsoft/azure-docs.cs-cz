---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175512"
---
1. V [webu Azure portal](https://portal.azure.com/), klikněte na tlačítko **Procházet vše** > **App Services**a potom klikněte na back-endu Mobile Apps. V části **nastavení**, klikněte na tlačítko **App Service Push**a potom klikněte na název vašeho centra oznámení.
2. Přejděte na **Google (GCM)** , zadejte **klíč serveru** hodnotu, která jste získali z Firebase v předchozím postupu a potom klikněte na **Uložit**.

    ![Nastavte klíč rozhraní API na portálu](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Back-endu Mobile Apps je nyní nakonfigurováno pro použití služby Firebase Cloud Messaging. To umožňuje odesílat nabízená oznámení do vaší aplikace běžící na zařízení s Androidem pomocí centra oznámení.
