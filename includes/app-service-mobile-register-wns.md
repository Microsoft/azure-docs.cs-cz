---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: 75bcb9d27ee6f66a1d9c15093d9f933a3ad25881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175504"
---
1. V Průzkumníkovi řešení Visual Studia klikněte pravým tlačítkem myši na projekt aplikace pro Windows Store. Pak vyberte **Store** > **Přidružit aplikaci ke Storu**.

    ![Přidružení aplikace k Windows Storu](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. V průvodci vyberte **Další**. Pak se přihlaste pomocí svého účtu Microsoft. V **popřípadě Rezervovat nový název aplikace**zadejte název aplikace a pak vyberte **Rezervovat**.
3. Po úspěšném vytvoření registrace aplikace vyberte nový název aplikace. Vyberte **Další**a pak vyberte **Přidružit**. Tento proces přidá požadované informace o registraci windows store do manifestu aplikace.
4. Opakujte kroky 1 a 3 pro projekt aplikace pro Windows Phone Store pomocí stejné registrace, kterou jste pro aplikaci pro Windows Store vytvořili dříve.  
5. Přejděte na [Windows Dev Center](https://dev.windows.com/en-us/overview)a přihlaste se pomocí svého účtu Microsoft. V **části Moje aplikace**vyberte novou registraci aplikace. Potom rozbalte**nabízená oznámení** **služby** > .
6. Na stránce **Nabízená oznámení** vyberte v části **Služby nabízených oznámení Windows (WNS) a Mobilní aplikace Microsoft Azure**v yberte Live Services **.**  Poznamenejte si hodnoty **sid balíčku** a *aktuální* hodnotu v **tajné aplikaci**. 

    ![Nastavení aplikace v centru pro vývojáře](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Tajný klíč aplikace a SID balíčku jsou důležité přihlašovací údaje zabezpečení. Tyto hodnoty s nikým nesdílejte ani je nedistribuujte s aplikací.
   >
   >
