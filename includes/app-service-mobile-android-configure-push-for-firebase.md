---
author: conceptdev
ms.author: crdun
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.openlocfilehash: 51a75ee7bf87c38e3916bdbc8d85abcfb14dca8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175512"
---
1. Na [webu Azure Portal](https://portal.azure.com/)klikněte na **Procházet všechny** > **služby aplikací**a potom klikněte na back-end mobilních aplikací. V části **Nastavení**klikněte na **Nabízená služba nabízená**a potom klikněte na název centra oznámení.
2. Přejděte na **Google (GCM),** zadejte hodnotu **Klíč serveru,** kterou jste získali z Firebase v předchozím postupu, a klepněte na tlačítko **Uložit**.

    ![Nastavení klíče rozhraní API na portálu](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

Back-end mobilních aplikací je teď nakonfigurovaný tak, aby používal Cloud messaging Firebase. To vám umožní odesílat nabízená oznámení do aplikace spuštěné na zařízení android pomocí centra oznámení.
