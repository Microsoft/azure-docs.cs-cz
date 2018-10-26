---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50134024"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurace projektu pro iOS v nástroji Xamarin Studio
1. V Xamarin.Studio, otevřete **Info.plist**a aktualizovat **identifikátor sady prostředků** s ID sady prostředků, kterou jste vytvořili pomocí ID aplikace.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Přejděte dolů k položce **režimy běhu na pozadí**. Vyberte **povolit režimy pozadí** pole a **Vzdálená oznámení** pole.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Dvakrát klikněte na projekt v panelu řešení otevřete **možnosti projektu**.
4. V části **sestavení**, zvolte **podepsání sady prostředků aplikace pro iOS**a vyberte odpovídající identitu a zřizovací profil jste právě nastavili nahoru pro tento projekt.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Tím se zajistí, že projekt používá nový profil pro podepisování kódu. Oficiální Xamarin zřizování zařízení dokumentaci, naleznete v tématu [Xamarin Device Provisioning].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurace projektu pro iOS v sadě Visual Studio
1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt a potom klikněte na tlačítko **vlastnosti**.
2. Na stránkách vlastností, klikněte na tlačítko **aplikace pro iOS** kartě a aktualizovat **identifikátor** ID, které jste vytvořili dříve.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. V **podepsání sady prostředků aplikace pro iOS** kartu, vyberte odpovídající identitu a zřizovací profil, stačí nastavit pro tento projekt.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Tím se zajistí, že projekt používá nový profil pro podepisování kódu. Oficiální Xamarin zřizování zařízení dokumentaci, naleznete v tématu [Xamarin Device Provisioning].
4. Poklikejte na soubor Info.plist ji otevřete a potom povolit **RemoteNotifications** pod **režimy běhu na pozadí**.

[Xamarin Device Provisioning]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
