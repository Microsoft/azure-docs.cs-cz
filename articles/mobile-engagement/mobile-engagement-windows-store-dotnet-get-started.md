---
title: Začínáme s Azure Mobile Engagementem pro univerzální aplikace pro Windows
description: Naučte se používat Azure Mobile Engagement s analytickými funkcemi a nabízenými oznámeními pro univerzální aplikace pro Windows.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4f1de1092458bfc12236802d829a2043dcc74540
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Začínáme s Azure Mobile Engagementem pro univerzální aplikace pro Windows
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

V tomto tématu si ukážeme, jak používat Azure Mobile Engagement, jak porozumět používání aplikace a jak odesílat nabízená oznámení segmentovaným uživatelům univerzální aplikace pro Windows.
Tento kurz představuje scénář jednoduchého vysílání přes Mobile Engagement. Vytvoříte prázdnou univerzální aplikaci pro Windows, která bude shromažďovat základní data o využití aplikace a přijímat nabízená oznámení pomocí služby oznamování systému Windows (WNS).

> [!NOTE]
> Službu Azure Mobile Engagement vyřadíme z provozu v březnu 2018. V současnosti je dostupná jenom pro stávající zákazníky. Další informace najdete v tématu [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/).

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Nastavení Mobile Engagementu pro univerzální aplikaci pro Windows
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Připojení aplikace k back-endu Mobile Engagementu
V tomto kurzu si představíme „základní integraci“, čili minimální sadu, která je zapotřebí pro shromažďování dat a odesílání nabízených oznámení. Kompletní dokumentaci k integraci najdete v článku [Integrace sady Mobile Engagement Windows Universal SDK](mobile-engagement-windows-store-sdk-overview.md)

Pomocí sady Visual Studio vytvoříte základní aplikaci, na které si tuto integraci předvedeme.

### <a name="create-a-windows-universal-app-project"></a>Vytvoření projektu univerzální aplikace pro Windows
Následující postup předpokládá použití sady Visual Studio 2015, ačkoliv kroky se podobají předchozím verzím sady Visual Studio.

1. Spusťte Visual Studio a na obrazovce **Domů** vyberte **Nový projekt**.
2. V místní nabídce vyberte **Windows** -> **Universal** -> **Prázdná aplikace (Universal Windows)**. Vyplňte **Název** aplikace a **Název řešení** a potom klikněte na **OK**.

    ![][1]

Nyní jste vytvořili projekt univerzální aplikace pro Windows, do kterého budete dál integrovat sadu Azure Mobile Engagement SDK.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Připojení aplikace k back-endu Mobile Engagementu
1. Nainstalujte balíček NuGet [MicrosoftAzure.MobileEngagement] do projektu. Pokud cílíte na platformy Windows i Windows Phone, musíte to provést u obou projektů. U systému Windows 8.x a Windows Phone 8.1 umístí jeden balíček NuGet do každého projektu správné binární soubory pro konkrétní platformu.
2. Otevřete **Package.appxmanifest** a ujistěte se, zda je přidána následující možnost:

        Internet (Client)

    ![][2]
3. Nyní zkopírujte připojovací řetězec, který jste zkopírovali dříve pro aplikaci Mobile Engagementu, a vložte jej do souboru `Resources\EngagementConfiguration.xml` mezi značky `<connectionString>` a `</connectionString>`:

    ![][3]

    > [!TIP]
    > Pokud vaše aplikace cílí na platformy Windows i Windows Phone, měli byste stále vytvořit dvě aplikace Mobile Engagementu – jednu pro každou podporovanou platformu. Dvě aplikace zajišťují, že můžete správně segmentovat publika a posílat vhodně cílená oznámení na každou platformu.

    > [!IMPORTANT]
    > NuGet automaticky nekopíruje prostředky sady SDK v aplikaci Windows 10 UWP. Je potřeba to provést ručně podle pokynů, které se zobrazí (readme.txt) při instalaci balíčku Nuget.  

1. V souboru `App.xaml.cs`:

    a. Přidejte příkaz `using`:

            using Microsoft.Azure.Engagement;

    b. Přidejte metodu, která inicializuje Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    c. Inicializujte sadu SDK v metodě **OnLaunched**:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    c. Do metody **OnActivated** vložte následující a metodu přidejte (pokud zatím není přítomná):

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>Povolení sledování v reálném čase
Pokud chcete začít odesílat data a zajistit, že uživatelé jsou aktivní, musíte odeslat alespoň jednu obrazovku (aktivitu) na back-end Mobile Engagementu.

1. V **MainPage.xaml.cs** přidejte následující příkaz `using`:

    použití příkazu Microsoft.Azure.Engagement.Overlay;
2. Změňte základní třídu **MainPage** z **Page** na **EngagementPageOverlay**:

        class MainPage : EngagementPageOverlay
3. V souboru `MainPage.xaml`:

    a. Přidejte do deklarací oborů názvů:

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    b. Nahraďte **Page** v názvu značky XML textem **engagement:EngagementPageOverlay**

> [!IMPORTANT]
> Pokud stránka přepíše metodu `OnNavigatedTo`, nezapomeňte volat `base.OnNavigatedTo(e)`. Jinak aktivita nebude hlášena (`EngagementPage` volá `StartActivity` uvnitř své metody `OnNavigatedTo`). To je obzvláště důležité v projektu Windows Phone, kde má výchozí šablona metodu `OnNavigatedTo`.
>
> Pro **univerzální aplikace pro Windows 10** použijte místo metody nahoře doporučenou metodu v části „Doporučení metoda: přetížení tříd Page“ tématu [Rozšířená tvorba sestav pomocí sady Windows Universal Apps Engagement SDK](mobile-engagement-windows-store-advanced-reporting.md).

## <a id="monitor"></a>Připojení aplikace se sledováním v reálném čase
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>Povolení nabízených oznámení a zasílání zpráv v aplikaci
Mobile Engagement vám umožňuje v rámci kampaní oslovit uživatele a komunikovat s nimi prostřednictvím nabízených oznámení a zpráv v aplikacích. Tento modul se na portálu Mobile Engagement nazývá REACH.
V následujících sekcích nastavíte aplikaci, aby tato nabízená oznámení a zprávy přijímala.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>Povolení přijímání nabízených oznámení WNS v aplikaci
1. V souboru `Package.appxmanifest` na kartě **Aplikace** v části **Oznámení** nastavte **Podporující oznamovací zprávy:** na **Ano**

    ![][5]

### <a name="initialize-the-reach-sdk"></a>Inicializace sady REACH SDK
V `App.xaml.cs` volejte **EngagementReach.Instance.Init(e);** ve funkci **InitEngagement** hned po inicializaci agenta:

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

Jste připravení odeslat oznámení. Dál ověříme, jestli jste základní integraci provedli správně.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>Udělení přístupu k Mobile Engagementu za účelem odesílání oznámení
1. Otevřete [Centrum vývojářů pro Windows Store] ve webovém prohlížeči, přihlaste se a v případě potřeby si vytvořte účet.
2. Klikněte na **Řídicí panel** v pravém horním rohu a pak klikněte na **Vytvořit novou aplikaci** z nabídky na levém panelu.

    ![][9]
3. Vytvoření aplikace rezervací názvu.

    ![][10]
4. Po vytvoření aplikace přejděte do části **Služby -> Nabízená oznámení** v nabídce vlevo.

    ![][11]
5. V části Nabízená oznámení klikněte na odkaz **web služeb Live Services**.

    ![][12]
6. Budete přesměrováni do části Přihlašovací údaje oznámení. Zkontrolujte, zda jste v části **Nastavení aplikace**, a poté zkopírujte **SID balíčku** a **Tajný klíč klienta**

    ![][13]
7. Přejděte do **Nastavení** v portálu Mobile Engagement a klikněte na část **Nativní oznámení** na levé straně. Klikněte na tlačítko **Upravit** a zadejte **Identifikátor zabezpečení balíčku (SID)** a **Tajný klíč**, jak je zobrazeno:

    ![][6]
8. Nakonec nezapomeňte přiřadit aplikaci Visual Studio k této vytvořené aplikaci v App Storu. V sadě Visual Studio klikněte na **Propojit aplikaci se Storem**.

    ![][7]

## <a id="send"></a>Odeslání oznámení do vaší aplikace
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Pokud aplikace běží, zobrazí se oznámení v aplikaci. Pokud je aplikace je zavřená, zobrazí se informační zpráva.
Pokud se zobrazuje oznámení v aplikaci, ale ne oznámení s informační zprávou a aplikace je spuštěna v režimu ladění v sadě Visual Studio, zkontrolujte v části **Události životního cyklu -> Pozastavit**, že je aplikace pozastavená. Po kliknutí na tlačítko Domů při ladění aplikace v sadě Visual Studio nemusí vždy dojít k jejímu pozastavení, a protože se oznámení zobrazuje v aplikaci, nezobrazí se jako informační zpráva.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Centrum vývojářů pro Windows Store]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
