---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 79459be30a5a2018dc82486a84895b1a954941bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175506"
---
1. Na [webu Azure Portal](https://portal.azure.com/)vyberte **Procházet všechny** > **služby aplikací**. Pak vyberte back-end mobilních aplikací. V části **Nastavení**vyberte **Nabízená služba Push**. Pak vyberte název centra oznámení.
2. Přejděte na **windows (WNS)**. Poté zadejte **klíč zabezpečení** (tajný klíč **klienta) a sid balíčku,** které jste získali z webu služeb Live Services. Dále vyberte **Uložit**.

    ![Nastavení klíče WNS na portálu](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

Back-end je nyní nakonfigurován tak, aby k odesílání nabízených oznámení používal a používal nabízená oznámení.
