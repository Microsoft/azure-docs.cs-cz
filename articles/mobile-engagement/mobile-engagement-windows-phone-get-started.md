---
title: Začínáme s Azure Mobile Engagementem pro aplikace Silverlight pro Windows Phone
description: Naučte se používat Azure Mobile Engagement s analytickými funkcemi a nabízenými oznámeními pro aplikace Silverlight pro Windows Phone
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9bf51bf6a4e2b84771edb9841e1b0ae18953826c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Začínáme s Azure Mobile Engagementem pro aplikace Silverlight pro Windows Phone
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

V tomto tématu si ukážeme, jak používat Azure Mobile Engagement, jak porozumět používání aplikace a jak odesílat nabízená oznámení segmentovaným uživatelům aplikace Silverlight pro Windows Phone.
Tento kurz představuje scénář jednoduchého vysílání přes Mobile Engagement. Vytvoříte prázdnou aplikaci Silverlight pro Windows Phone, která bude shromažďovat základní data a přijímat nabízená oznámení pomocí Microsoft Služby nabízených oznámení (MPNS).

> [!NOTE]
> Službu Azure Mobile Engagement vyřadíme z provozu v březnu 2018. V současnosti je dostupná jenom pro stávající zákazníky. Další informace najdete v tématu [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/).

> [!NOTE]
> Projekty pro Windows Phone 8.1 a starší verze nejsou podporovány v sadě Visual Studio 2017.  Další informace najdete v tématu [Cílení na platformy a kompatibilita v sadě Visual Studio 2017](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs).

> [!NOTE]
> Pokud cílíte na platformu Windows Phone 8.1 (ne Silverlight), přejděte na [kurz pro univerzální aplikace pro Windows](mobile-engagement-windows-store-dotnet-get-started.md).
> 
> 

V tomto kurzu budete potřebovat následující:

* Visual Studio 2013
* Balíček NuGet [MicrosoftAzure.MobileEngagement]

> [!NOTE]
> K dokončení tohoto kurzu potřebujete mít aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit bezplatný zkušební účet. Podrobnosti najdete v článku [Bezplatná zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).
> 
> 

## <a id="setup-azme"></a>Nastavení Mobile Engagementu pro aplikaci pro Windows Phone
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Připojení aplikace k back-endu Mobile Engagementu
V tomto kurzu si představíme „základní integraci“, čili minimální sadu požadovanou pro shromažďování dat a odesílání nabízených oznámení. Kompletní dokumentaci k integraci najdete v článku [Integrace sady Mobile Engagement Windows Phone SDK](mobile-engagement-windows-phone-sdk-overview.md)

Pomocí Visual Studia si vytvoříme základní aplikaci, na které si tuto integraci předvedeme.

### <a name="create-a-new-windows-phone-silverlight-project"></a>Vytvoření nového projektu Windows Phone Silverlight
Následující postup předpokládá použití sady Visual Studio 2015, ačkoliv kroky se podobají předchozím verzím sady Visual Studio. 

1. Spusťte Visual Studio a na obrazovce **Domů** vyberte **Nový projekt**.
2. V místní nabídce vyberte **Windows 8** -> **Windows Phone** -> **Prázdná aplikace (Windows Phone Silverlight)**. Vyplňte **Název** aplikace a **Název řešení** a potom klikněte na **OK**.
   
    ![][1]
3. Můžete se rozhodnout cílit na platformu **Windows Phone 8.0** nebo **Windows Phone 8.1**.

Nyní jste vytvořili novou aplikaci Silverlight pro Windows Phone, do které budeme integrovat sadu Azure Mobile Engagement SDK.

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Připojení aplikace k back-endu Mobile Engagementu
1. Nainstalujte balíček NuGet [MicrosoftAzure.MobileEngagement] do projektu.
2. Otevřete `WMAppManifest.xml` (ve složce Properties) a zkontrolujte, zda jsou ve značce `<Capabilities />` deklarovány následující řetězce (pokud nejsou, přidejte je):
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. Nyní vložte připojovací řetězec, který jste zkopírovali dříve pro aplikaci Mobile Engagementu, do souboru `Resources\EngagementConfiguration.xml` mezi značky `<connectionString>` a `</connectionString>`:
   
    ![][3]
4. V souboru `App.xaml.cs`:
   
    a. Přidejte příkaz `using`:
   
            using Microsoft.Azure.Engagement;
   
    b. Inicializujte sadu SDK v metodě `Application_Launching`:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. Do `Application_Activated` vložte následující:
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a id="monitor"></a>Povolení sledování v reálném čase
Pokud chcete začít odesílat data a zajistit, že uživatelé jsou aktivní, musíte odeslat alespoň jednu obrazovku (aktivitu) na back-end Mobile Engagementu.

1. V MainPage.xaml.cs přidejte příkaz `using`:
   
        using Microsoft.Azure.Engagement;
2. Změňte základní třídu **MainPage**, která je před **PhoneApplicationPage**, na **EngagementPage**.
   
        class MainPage : EngagementPage 
3. V souboru `MainPage.xml`:
   
    a. Přidejte do deklarací oborů názvů:
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b. Nahraďte kód `phone:PhoneApplicationPage` v názvu značky XML kódem `engagement:EngagementPage`.

## <a id="monitor"></a>Připojení aplikace se sledováním v reálném čase
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Povolení nabízených oznámení a zasílání zpráv v aplikaci
Mobile Engagement vám umožňuje v rámci kampaní oslovit uživatele a komunikovat s nimi prostřednictvím nabízených oznámení a zpráv v aplikacích. Tento modul se na portálu Mobile Engagement nazývá REACH.
V následujících sekcích nastavíte aplikaci, aby tato nabízená oznámení a zprávy přijímala.

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>Povolení přijímání nabízených oznámení MPNS v aplikaci
Přidejte nové schopnosti do souboru `WMAppManifest.xml`:

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicializace sady REACH SDK
1. V `App.xaml.cs` volejte `EngagementReach.Instance.Init();` ve funkci **Application_Launching** hned po inicializaci agenta:
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. V `App.xaml.cs` volejte `EngagementReach.Instance.OnActivated(e);` ve funkci **Application_Activated** hned po inicializaci agenta:
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Nyní je vše připraveno. A teď ověříme, zda jste základní integraci provedli správně.

## <a id="send"></a>Odeslání oznámení do vaší aplikace
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Nyní by se ve vašem zařízení mělo zobrazit oznámení, které se zobrazí v aplikaci, pokud je aplikace otevřená, v opačném případě se následujícím způsobem zobrazí s informační zprávou: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
