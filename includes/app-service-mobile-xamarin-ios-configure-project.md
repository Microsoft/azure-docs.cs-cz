---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: a69df0cc9ea14a2c9fa172c77663afb1d6861f9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175507"
---
#### <a name="configure-the-ios-project-in-xamarin-studio"></a>Konfigurace projektu iOS v Xamarin Studiu
1. V Xamarin.Studiu otevřete **soubor Info.plist**a aktualizujte **identifikátor sady** s ID sady, které jste vytvořili dříve, pomocí nového ID aplikace.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-21.png)
2. Posuňte se dolů do **režimu pozadí**. Zaškrtněte **políčko Povolit režimy na pozadí** a **Vzdálená oznámení.**

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-22.png)
3. Poklepáním na projekt v panelu Řešení otevřete **možnosti projektu**.
4. V **části Sestavení**zvolte **podepisování balíčků pro iOS**a vyberte odpovídající profil identity a zřizování, který jste právě nastavili pro tento projekt.

   ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-20.png)

   Tím je zajištěno, že projekt používá nový profil pro podepisování kódu. Oficiální dokumentace pro zřizování zařízení Xamarin naleznete v tématu [Xamarin Device Provisioning].

#### <a name="configure-the-ios-project-in-visual-studio"></a>Konfigurace projektu iOS v sadě Visual Studio
1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt a potom klikněte na **příkaz Vlastnosti**.
2. Na stránkách vlastností klikněte na kartu **Aplikace pro iOS** a aktualizujte **identifikátor** s ID, které jste vytvořili dříve.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-23.png)
3. Na kartě **podepisování balíčků iOS** vyberte odpovídající profil identity a zřizování, který jste právě nastavili pro tento projekt.

    ![](./media/app-service-mobile-xamarin-ios-configure-project/mobile-services-ios-push-24.png)

    Tím je zajištěno, že projekt používá nový profil pro podepisování kódu. Oficiální dokumentace pro zřizování zařízení Xamarin naleznete v tématu [Xamarin Device Provisioning].
4. Poklepáním na soubor Info.plist jej otevřete a povolte **vzdálené oznámení** v části **Režimy pozadí**.

[Zřizování zařízení Xamarin]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/
