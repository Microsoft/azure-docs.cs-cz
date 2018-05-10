---
title: Začínáme s Azure Mobile Engagementem pro Xamarin.iOS
description: Naučte se používat Azure Mobile Engagement s analytickými funkcemi a nabízenými oznámeními pro aplikace pro Xamarin.iOS.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 331ae3f77603ee5fc5153c52c5c103813d7029ed
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Začínáme s Azure Mobile Engagementem pro aplikace Xamarin.iOS
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

V tomto tématu si ukážeme, jak používat Azure Mobile Engagement, jak porozumět používání aplikace a jak odesílat nabízená oznámení segmentovaným uživatelům aplikace pro Xamarin.iOS.
V tomto kurzu vytvoříte prázdnou aplikaci Xamarin.iOS, která bude shromažďovat základní data a přijímat nabízená oznámení pomocí systému nabízených oznámení Apple (APNS). 

> [!NOTE]
> Službu Azure Mobile Engagement vyřadíme z provozu v březnu 2018. V současnosti je dostupná jenom pro stávající zákazníky. Další informace najdete v tématu [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

V tomto kurzu budete potřebovat následující:

* [Xamarin Studio](http://xamarin.com/studio). Můžete také použít Visual Studio s Xamarinem, ale v tomto kurzu používáme Xamarin Studio. Instalační pokyny najdete v tématu o [nastavení a instalaci pro Visual Studio a Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).
> 
> 

## <a id="setup-azme"></a>Nastavení Mobile Engagementu pro vaši aplikaci pro iOS
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Připojení aplikace k back-endu Mobile Engagementu
V tomto kurzu si představíme „základní integraci“, čili minimální sadu, která je zapotřebí pro shromažďování dat a odesílání nabízených oznámení.

Pomocí Xamarinu si vytvoříme základní aplikaci, na které si tuto integraci předvedeme.

### <a name="create-a-new-xamarinios-project"></a>Vytvoření nového projektu Xamarin.iOS
1. Spusťte Xamarin Studio. Klikněte na položky **File** (Soubor)  -> **New** (Nové)  -> **Solution** (Řešení). 
   
    ![][1]
2. Vyberte **Single View App** (Aplikace s jedním zobrazením), zkontrolujte, zda je vybraný jazyk **C#** a klikněte na tlačítko **Next** (Další).
   
    ![][2]
3. Vyplňte pole **App Name** (Název aplikace) a **Organization Identifier** (Identifikátor organizace) a potom klikněte na tlačítko **Next** (Další). 
   
    ![][3]
   
   > [!IMPORTANT]
   > Ujistěte se, že profil publikování, který nakonec použijete k nasazení své aplikace pro iOS, používá ID aplikace, které přesně odpovídá vašemu identifikátoru svazku. 
   > 
   > 
4. Podle potřeby aktualizujte pole **Project Name** (Název projektu), **Solution Name** (Název řešení) a **Location** (Umístění) a klikněte na **Create** (Vytvořit).
   
    ![][4]

Xamarin Studio vytvoří ukázkovou aplikaci, do které budeme integrovat Mobile Engagement. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Připojení aplikace k back-endu Mobile Engagementu
1. Pravým tlačítkem myši klikněte v oknech řešení na složku **Packages** (Balíčky) a vyberte **Add Packages...** (Přidat balíčky...).
   
    ![][5]
2. Vyhledejte **Microsoft Azure Mobile Engagement Xamarin SDK** a přidejte jej do řešení.  
   
    ![][6]
3. Otevřete **AppDelegate.cs** a přidejte následující příkaz using:
   
        using Microsoft.Azure.Engagement.Xamarin;
4. V metodě **FinishedLaunching** přidejte následující, a inicializujte tak připojení s back-endem Mobile Engagementu. Nezapomeňte přidat řetězec **ConnectionString**. Tento kód také používá fiktivní řetězec **NotificationIcon**, který přidává Mobile Engagement SDK a který možná budete chtít nahradit. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>Povolení sledování v reálném čase
Pokud chcete začít odesílat data a zajistit, že uživatelé jsou aktivní, musíte odeslat alespoň jednu obrazovku na back-end Mobile Engagementu.

1. Otevřete **ViewController.cs** a přidejte následující příkaz using:
   
        using Microsoft.Azure.Engagement.Xamarin;
2. Změňte třídu, ze které přebírá metoda `ViewController`, z `UIViewController` na `EngagementViewController`. 

## <a id="monitor"></a>Připojení aplikace se sledováním v reálném čase
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Povolení nabízených oznámení a zasílání zpráv v aplikaci
Mobile Engagement vám umožňuje v rámci kampaní oslovit uživatele a komunikovat s nimi prostřednictvím nabízených oznámení a zpráv v aplikacích. Tento modul se na portálu Mobile Engagement nazývá REACH.
V následujících sekcích nastavíte aplikaci, aby tato nabízená oznámení a zprávy přijímala.

### <a name="modify-your-application-delegate"></a>Úprava delegáta aplikace
1. Otevřete **AppDelegate.cs** a přidejte následující příkaz using:
   
        using System; 
2. Nyní uvnitř metody `FinishedLaunching` přidejte následující, abyste mohli registrovat nabízená oznámení po `EngagementAgent.init(...)`
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. Nakonec - aktualizujte nebo přidejte následující metody:
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. V souboru **Info.plist** v řešení zkontrolujte, zda se položka **Bundle Identifier** (Identifikátor svazku) shoduje s položkou **App ID** (ID aplikace), které máte v profilu zřizování v Centru pro vývojáře Apple. 
   
    ![][7]
5. Ve stejném souboru **Info.plist** nezapomeňte zaškrtnout **Enable Background Modes** (Povolit režimy pozadí) a **Remote Notifications** (Vzdálená oznámení). 
   
     ![][8]
6. Spusťte aplikaci v zařízení, které je propojené s tímto profilem publikování. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
