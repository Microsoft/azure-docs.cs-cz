---
title: Postupy upgradu systému Windows Universal SDK aplikace
description: Postupy upgradu systému Windows Universal SDK aplikací pro Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a9d6cbcdf353f7eea991c344c3efe65378abe336
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Postupy upgradu systému Windows Universal SDK aplikace
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Pokud již jste spojili starší verze zapojení do své aplikace, je nutné zvážit následující body při upgradu sady SDK.

Možná budete muset několik postupy použijte, pokud provedena několik verzí sady SDK. Například pokud migrujete z 0.10.1 0.11.0 budete muset nejdřív postupujte podle pokynů "od 0.9.0 k 0.10.1" pak postupu "od 0.10.1 k 0.11.0".

## <a name="from-330-to-340"></a>Z 3.3.0 k 3.4.0
### <a name="test-logs"></a>Protokolů testování
Protokoly konzoly vyprodukované sady SDK teď může být povolena nebo zakázána nebo filtrovat. Chcete-li přizpůsobit tím, aktualizujte vlastnost `EngagementAgent.Instance.TestLogEnabled` na jednu z hodnota dostupná z `EngagementTestLogLevel` výčtu pro instanci:

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>Zdroje a prostředky
Bylo vylepšeno Reach překrytí. Je součástí zdroje balíčku NuGet sady SDK.

Při upgradu na novou verzi sady SDK můžete zvolit, zda chcete zachovat existující soubory ve složce překrytí vašich prostředků, nebo není:

* Pokud předchozí překrytí pracuje pro vás nebo jsou integrací `WebView` elementy ručně pak můžete rozhodnout zachovat stávající soubory, ji budou i nadále fungovat. 
* Pokud chcete aktualizovat na novou překrytí, právě nahradit všechny `overlay` složku z vašich prostředků s novým z balíčku SDK (aplikace UWP: Po dokončení upgradu, můžete získat novou složku překrytí % USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources).

> [!WARNING]
> Pomocí nové překrytí přepíše všechny úpravy probíhají v předchozí verzi.
> 
> 

## <a name="from-320-to-330"></a>Z 3.2.0 k 3.3.0
### <a name="resources"></a>Zdroje a prostředky
Tento krok se týká jenom vlastní prostředky. Pokud jste upravili prostředky poskytované sadě SDK (html, obrázky, překrytí) budete muset zálohování je před upgradem a znovu použít vlastní na upgradovaný prostředky.

## <a name="from-310-to-320"></a>Z 3.1.0 k 3.2.0
### <a name="resources"></a>Zdroje a prostředky
Tento krok se týká jenom vlastní prostředky. Pokud jste upravili prostředky poskytované sadě SDK (html, obrázky, překrytí) budete muset zálohování je před upgradem a znovu použít vlastní na upgradovaný prostředky.

### <a name="webview-integration"></a>Integrace webové zobrazení
Některé vylepšení tak, aby odpovídaly velikostem jiné zařízení byly zavedeny v této verzi. Zajistěte, aby odpovídaly svoji integraci webové zobrazení následující:

Ve vaší () stránky XAML:

            <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
            <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

A v souboru přidružené .cs:

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
            /// <summary>
            /// An empty page that can be used on its own or navigated to within a Frame.
            /// </summary>
            public sealed partial class ExampleEngagementReachPage : EngagementPage
            {
              public ExampleEngagementReachPage()
              {
                this.InitializeComponent();

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

              #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

              /* When page is left ensure to detach SizeChanged handler. */
              protected override void OnNavigatedFrom(NavigationEventArgs e)
              {
                Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
                base.OnNavigatedFrom(e);
              }

              /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
              double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
              double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
              double width =  Window.Current.Bounds.Width;
              double height =  Window.Current.Bounds.Height;
    #endif

              /// <summary>
              /// Set your webview elements to the correct size.
              /// </summary>
              /// <param name="width">The width of your current display.</param>
              /// <param name="height">The height of your current display.</param>
              private void SetWebView(double width, double height)
              {
                #pragma warning disable 4014
                CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
                        () =>
                        {
                          this.engagement_notification_content.Width = width;
                          this.engagement_announcement_content.Width = width;
                          this.engagement_announcement_content.Height = height;
                        });
              }

              /// <summary>
              /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
              /// </summary>
              /// <param name="sender">Original event trigger.</param>
              /// <param name="e">Window Size Changed Event arguments.</param>
              private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
              {
                double width = e.Size.Width;
                double height = e.Size.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP              
              /// <summary>
              /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
              /// </summary>
              /// <param name="sender">The related application view.</param>
              /// <param name="e">Related event arguments.</param>
              private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
              {
                double width = sender.VisibleBounds.Width;
                double height = sender.VisibleBounds.Height;

                /* Set your webview elements to the correct size. */
                SetWebView(width, height);
              }
    #endif
              #endregion
            }
    }

## <a name="from-200-to-300"></a>Z 2.0.0 k 3.0.0
### <a name="resources"></a>Zdroje a prostředky
Tento krok se týká jenom vlastní prostředky. Pokud jste upravili prostředky poskytované sadě SDK (html, obrázky, překrytí) budete muset zálohování je před upgradem a znovu použít vlastní na upgradovaný prostředky.

## <a name="from-111-to-200"></a>Z 1.1.1 k 2.0.0
Následující část popisuje postup migrace integraci sady SDK z Capptain služby, které do aplikace používá technologii Azure Mobile Engagement nabízí Capptain SAS. 

> [!IMPORTANT]
> Capptain a Mobile Engagement nejsou stejné služby a postup níže uvedené jenom dozvíte, jak migrovat klientské aplikace. Migrace sady SDK v aplikaci není migrovat data ze serverů Capptain na servery Mobile Engagement
> 
> 

Pokud provádíte migraci ze starší verze, najdete na webu společnosti Capptain nejdřív přenést 1.1.1 pak použije následující postup

### <a name="nuget-package"></a>Balíček Nuget
Nahraďte **Capptain.WindowsPhone** podle **MicrosoftAzure.MobileEngagement** balíček Nuget.

### <a name="applying-mobile-engagement"></a>Použití Mobile Engagement
Sada SDK používá termín `Engagement`. Je potřeba aktualizovat projekt tak, aby odpovídaly tuto změnu.

Je potřeba odinstalovat váš aktuální Capptain balíček nuget. Zvažte, se odeberou všechny změny ve složce Capptain prostředky. Pokud chcete, aby tyto soubory pak proveďte jejich kopii.

Potom nainstalujte nový balíček nuget Microsoft Azure Engagement na projektu. Najdete ho přímo na [nuget webu]. nebo sem index. Tato akce nahradí všechny soubory prostředky používané Engagement a přidá nová knihovna DLL Engagement odkazy projektu.

Je nutné vyčistit odkazy projektu odstraněním Capptain DLL odkazy. Pokud to neuděláte, budou v konfliktu verze Capptain a dojde k chybám.

Pokud jste upravili Capptain prostředky, zkopírujte původní soubory obsahu a vložit do nové soubory zapojení. Upozorňujeme, že soubory xaml a cs muset aktualizovat.

Po dokončení těchto kroků stačí nahraďte staré odkazy Capptain pomocí nové odkazy zapojení.

1. Všechny obory názvů Capptain muset aktualizovat.
   
    Před migrací:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    Po dokončení migrace:
   
        using Microsoft.Azure.Engagement;
2. Všechny třídy Capptain, které obsahují "Capptain" by měly obsahovat "Engagement".
   
    Před migrací:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    Po dokončení migrace:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. Pro soubory xaml Capptain obor názvů a atributy také změnit.
   
    Před migrací:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    Po dokončení migrace:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. Změny stránky překrytí
   
   > [!IMPORTANT]
   > Překrytí také změní. Jeho nový obor názvů je `Microsoft.Azure.Engagement.Overlay`. Je třeba použít v souboru xaml a cs. Kromě toho `CapptainGrid` je s názvem `EngagementGrid`, `capptain_notification_content` a `capptain_announcement_content` jsou pojmenované `engagement_notification_content` a `engagement_announcement_content`.
   > 
   > 
   
    Pro překrytí:
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    Stane se:
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Pro jiné prostředky jako jsou soubory HTML a obrázky Capptain Všimněte si, že se také přejmenovaná používat "Engagement".

### <a name="project-declaration"></a>Deklarace projektu
Na Package.appxmanifest `File Type Associations` byla aktualizována z:

* capptain\_dosáhnout\_obsahu engagement\_dosáhnout\_obsahu
* capptain\_protokolu\_soubor engagement\_protokolu\_souboru

### <a name="application-id--sdk-key"></a>ID aplikace nebo klíč SDK
Zapojení používá připojovací řetězec. Nemáte a zadejte ID aplikace a klíč SDK s Mobile Engagement, stačí zadat připojovací řetězec. Můžete ho nastavit tak v souboru EngagementConfiguration.

Konfigurace Engagement může být nastavena v vaší `Resources\EngagementConfiguration.xml` souboru projektu.

Upravte tento soubor k určení:

* Připojovací řetězec aplikací mezi značky `<connectionString>` a `<\connectionString>`.

Pokud chcete zadat za běhu, můžete volat metodu před Engagement inicializaci agenta:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

Připojovací řetězec pro vaši aplikaci se zobrazí na portálu Azure.

### <a name="items-name-change"></a>Změna názvu položky
Všechny položky s názvem *capptain* byly pojmenovány *engagement*. Podobně jako pro *Capptain* k *Engagement*.

Příklady běžně používané Capptain položek:

* CapptainConfiguration nyní s názvem EngagementConfiguration
* Nyní s názvem EngagementAgent CapptainAgent
* Nyní s názvem EngagementReach CapptainReach
* Nyní s názvem EngagementHttpConfig CapptainHttpConfig
* Nyní s názvem GetEngagementPageName GetCapptainPageName

Všimněte si, že přejmenovat také ovlivňuje přepsat metody.

