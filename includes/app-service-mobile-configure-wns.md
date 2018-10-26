---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134402"
---
1. V [webu Azure portal](https://portal.azure.com/)vyberte **Procházet vše** > **App Services**. Potom vyberte Mobile Apps back-endu. V části **nastavení**vyberte **App Service Push**. Vyberte název vašeho centra oznámení.
2. Přejděte na **Windows (WNS)**. Zadejte **klíč zabezpečení** (tajný klíč klienta) a **SID balíčku** , který jste získali z web služeb Live Services. V dalším kroku vyberte **Uložit**.

    ![Nastavte klíč služby nabízených oznámení Windows na portálu](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Back-endu je nyní nakonfigurováno pro použití služby nabízených oznámení Windows k odesílání nabízených oznámení.
