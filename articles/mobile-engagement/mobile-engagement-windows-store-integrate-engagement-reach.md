---
title: Univerzální aplikace Windows dosáhnout integraci sady SDK
description: Postup pro integraci Azure Mobile Engagement Reach univerzálních aplikací pro Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8472af2ac72a1976871970963851be775b19859b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Univerzální aplikace Windows dosáhnout integraci sady SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Je třeba provést postup integrace popsaný v tématu [integraci sady Windows Universal Engagement SDK](mobile-engagement-windows-store-integrate-engagement.md) před těchto pokynů.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Vložení Engagement Reach SDK do projektu univerzální pro Windows
Nemáte nic přidat. `EngagementReach` odkazy a prostředky jsou už ve vašem projektu.

> [!TIP]
> Můžete přizpůsobit bitové kopie, které jsou umístěné v `Resources` složky projektu, zejména ikonu značky (této výchozí ikonu Engagement). Pro univerzální aplikace také můžete přesunout `Resources` složky v projektu sdíleného sdílet jeho obsah mezi aplikací, ale bude třeba ponechat `Resources\EngagementConfiguration.xml` souborů na výchozího umístění, jako je platforma závislé.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Povolení služby oznámení Windows
### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x a Windows Phone 8.1 pouze
Chcete-li použít **služby oznámení Windows** (označované jako WNS) ve vaší `Package.appxmanifest` souborů na `Application UI` klikněte na `All Image Assets` do pole levé robota. Na pravé straně pole v `Notifications`, změňte `toast capable` z `(not set)` k `(Yes)`.

### <a name="all-platforms"></a>Všechny platformy
Budete muset synchronizovat vaší aplikaci účtu Microsoft a platforma pro zapojení. Pro tento potřebujete vytvořit účet nebo se přihlásit [Centrum vývojářů pro windows](https://dev.windows.com). Poté vytvořte novou aplikaci a najít identifikátor SID a tajný klíč. Na front-endu zapojení, přejděte na nastavení vaší aplikace v `native push` a vložte svoje přihlašovací údaje. Potom klikněte pravým tlačítkem na projekt, vyberte `store` a `Associate App with the Store...`. Stačí vybrat aplikaci, které vytvoříte před k synchronizaci.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializace Engagement Reach SDK
Změnit `App.xaml.cs`:

* Vložit `EngagementReach.Instance.Init` právě po `EngagementAgent.Instance.Init` ve vaší `InitEngagement` metoda:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init` Běží ve vyhrazené vlákno. Nemusíte dělat sami.

> [!NOTE]
> Pokud používáte nabízená oznámení jinde v aplikaci, pak budete muset [sdílet kanál nabízené](#push-channel-sharing) s Engagement Reach.
> 
> 

## <a name="integration"></a>Integrace
Engagement nabízí dva způsoby, jak do aplikace přidat Reach v aplikaci plakáty a vkládaná zobrazení pro oznámení a hlasování: integrace překrytí a ruční integraci webové zobrazení. Nesmí kombinovat obě přístup na stejné stránce.

Volba mezi dvěma integrace může shrnout takto:

* Můžete se rozhodnout integrace překrytí Pokud vaše stránky již dědí od agenta `EngagementPage`, bude stačit nahrazení `EngagementPage` podle `EngagementPageOverlay` a `xmlns:engagement="using:Microsoft.Azure.Engagement"` podle `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"` na stránkách.
* Můžete se rozhodnout webové zobrazení ruční integrace Pokud chcete přesněji umístit dosáhnout uživatelského rozhraní v rámci vaší stránky, nebo pokud nechcete přidat jinou úroveň dědičnosti na stránky. 

### <a name="overlay-integration"></a>Integrace překrytí
Překrytí Engagement dynamicky přidá prvky uživatelského rozhraní slouží k zobrazení kampaně Reach v stránku. Pokud překrytí nebude vyhovovat rozložení byste měli zvážit webové zobrazení ruční integrace místo.

V souboru změny XAML `EngagementPage` odkaz na `EngagementPageOverlay`

* Přidejte do deklarací oborů názvů:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* Nahraďte `engagement:EngagementPage` s `engagement:EngagementPageOverlay`:

**S EngagementPage:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**S EngagementPageOverlay:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

V souboru .cs značky stránku v `EngagementPageOverlay` místo `EngagementPage` a import `Microsoft.Azure.Engagement.Overlay`.

            using Microsoft.Azure.Engagement.Overlay;

* Nahraďte `EngagementPage` s `EngagementPageOverlay`:

**S EngagementPage:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPage
              {
                [...]
              }
            }

**S EngagementPageOverlay:**

            using Microsoft.Azure.Engagement.Overlay;

            namespace Example
            {
              public sealed partial class ExamplePage : EngagementPageOverlay 
              {
                [...]
              }
            }


Přidá do překrytí Engagement `Grid` elementu na stránku skládá z vaší rozložení a dvě `WebView` prvky jeden pro informační zprávě a druhá pro vkládaná zobrazení.

Můžete upravit přímo v překrytí elementy `EngagementPageOverlay.cs` souboru.

### <a name="web-views-manual-integration"></a>Webové zobrazení ruční integrace
Reach bude hledání svoje stránky pro dva `WebView` elementy odpovědná za zobrazování hlavičky a vkládaná zobrazení. Jediné, co musíte udělat, je přidání těchto dvou `WebView` elementy někde na stránkách, tady je příklad:

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


V tomto příkladu `WebView` elementy jsou roztažen tak, aby vyhovovaly jejich kontejneru, který automaticky je znovu velikostí v případě změna velikosti obrazovky otočení nebo okna.

> [!WARNING]
> Je důležité zachovat stejnou pojmenování `engagement_notification_content` a `engagement_announcement_content` pro `WebView` elementy. Reach je identifikace je podle názvu. 
> 
> 

## <a name="handle-datapush-optional"></a>Popisovač datapush (volitelné)
Pokud chcete aplikace nebudou moct přijímat Reach datová oznámení, je nutné implementovat dvě události EngagementReach třídy:

V souboru App.xaml.cs v konstruktoru App() přidejte:

            EngagementReach.Instance.DataPushStringReceived += (body) =>
            {
              Debug.WriteLine("String data push message received: " + body);
              return true;
            };

            EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
            {
              Debug.WriteLine("Base64 data push message received: " + encodedBody);
              // Do something useful with decodedBody like updating an image view
              return true;
            };

Uvidíte, že zpětné volání každá metoda vrátí logickou hodnotu. Zapojení odešle zpětnou vazbu k jeho back-end po odeslání nabízeného oznámení data. Pokud vrátí hodnotu false, zpětné volání `exit` zpětné vazby bude odesílat. Jinak bude `action`. Pokud je pro události, nastavené žádné zpětného volání `drop` zapojení se vrátí zpětnou vazbu.

> [!WARNING]
> Zapojení není schopný přijímat násobky názory pro datová oznámení. Pokud budete chtít nastavit několik obslužné rutiny na události, uvědomte si, zda bude poslední odpovídají zpětné vazby jeden odeslána. V takovém případě doporučujeme vždy vrací stejnou hodnotu, abyste nemuseli matoucí zpětnou vazbu na front-endu.
> 
> 

## <a name="customize-ui-optional"></a>Přizpůsobení uživatelského rozhraní (volitelné)
### <a name="first-step"></a>Prvním krokem
Můžeme vám umožňují přizpůsobit reach uživatelského rozhraní.

Uděláte to tak, budete muset vytvořit podtřídou třídy `EngagementReachHandler` třídy.

**Ukázkový kód:**

            using Microsoft.Azure.Engagement;

            namespace Example
            {
              internal class ExampleReachHandler : EngagementReachHandler
              {
               // Override EngagementReachHandler methods depending on your needs
              }
            }

Potom nastavte obsah `EngagementReach.Instance.Handler` pole s vaší vlastní objekt v vaše `App.xaml.cs` třídy v rámci `App()` metoda.

**Ukázkový kód:**

            protected override void OnLaunched(LaunchActivatedEventArgs args)
            {
              // your app initialization 
              EngagementReach.Instance.Handler = new ExampleReachHandler();
              // Engagement Agent and Reach initialization
            }

> [!NOTE]
> Ve výchozím nastavení používá Engagement jejich vlastní implementaci `EngagementReachHandler`.
> Nemusíte vytvářet vlastní, a pokud tak učiníte, nemusíte přepsat každou metodu. Výchozí chování je výběr základní objekt zapojení.
> 
> 

### <a name="web-view"></a>Webové zobrazení
Ve výchozím nastavení použije Reach vložené prostředky knihovny DLL k zobrazení oznámení a stránky.

K poskytování úplné přizpůsobení možnosti používáme pouze zobrazení webové stránky. Pokud chcete přizpůsobit rozložení, potlačí přímo soubory zdrojů `EngagementAnnouncement.html` a `EngagementNotification.html`. Zapojení musí všechny kód v `<body></body>` správné fungování. Ale můžete přidat značky vnější `engagement_webview_area`.

Můžete ale použít vlastní prostředky.

Můžete přepsat `EngagementReachHandler` metody v vaše podtřída říct Engagement používat vaše rozložení, ale pečlivě vložených mechanismus engagement:

**Ukázkový kód:**

            // In your subclass of EngagementReachHandler

            public override string GetAnnouncementHTML()
            {
              return base.GetAnnouncementHTML();
            }
            public override string GetAnnouncementName()
            {
              return base.GetAnnouncementName();
            }
            public override string GetNotfificationHTML()
            {
              return base.GetNotfificationHTML();
            }
            public override string GetNotfificationName()
            {
              return base.GetNotfificationName();
            }


Ve výchozím nastavení je AnnouncementHTML `ms-appx-web:///Resources/EngagementAnnouncement.html`. Reprezentuje soubor html, který návrh obsah nabízené zprávy (Text oznámení, webové anoucement a dotazování oznámení). Je AnnouncementName `engagement_announcement_content`. Je název webového zobrazení návrhu ve vašem xaml – stránka.

Je NotfificationHTML `ms-appx-web:///Resources/EngagementNotification.html`. Reprezentuje soubor html, který navrhnout oznámení nabízená zpráva. Je NotfificationName `engagement_notification_content`. Je název webového zobrazení návrhu ve vašem xaml – stránka.

### <a name="customization"></a>Přizpůsobení
Můžete přizpůsobit oznámení a oznámení, že má webové zobrazení, je-li zachovat Engagement objektu. Opatrní této webové zobrazení objekt je popsán třikrát - poprvé ve vašem jazyce xaml, ještě jednou v souboru .cs v metodě "setwebview()" a třetí čas v souboru html.

* Ve vašem xaml popisují aktuální webové zobrazení součást grafické rozložení.
* V souboru .cs můžete definovat "setwebview()", ve kterém můžete nastavit dimenzi dva webové zobrazení (oznámení, oznámení). Je velmi účinné při aplikace je změna velikosti.
* V souboru html Engagement jsme popisují webové zobrazení obsahu, návrhu a pozice prvky mezi sebou.

### <a name="launch-message"></a>Spusťte zprávu
Když uživatel klikne na systémové oznámení (oznámení), Engagement spustí aplikaci, načíst obsah nabízená oznámení a zobrazení stránky pro odpovídající kampaně.

Dochází ke zpoždění mezi spuštění aplikace a zobrazení stránky (v závislosti na rychlosti sítě).

Chcete-li uživatele upozornit, načítání něco, by měl poskytovat vizuální informace, jako je indikátor průběhu nebo indikátor průběhu. Zapojení nemůže zpracovat samostatně, ale poskytuje několik obslužné rutiny pro vás.

Chcete-li implementovat zpětné volání, v souboru App.xaml.cs v "Veřejné App() {}" přidáte:

            /* The application has launched and the content is loading.
             * You should display an indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };

            /* The application has finished loading the content and the page
             * is about to be displayed.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };

            /* The content has been loaded, but an error has occurred.
             * You can provide an information to the user.
             * You should hide the indicator here.
             */
            EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

Zpětné volání můžete nastavit vaše metoda "Veřejné App() {}" vaší `App.xaml.cs` soubor, pokud možno před `EngagementReach.Instance.Init()` volání.

> [!TIP]
> Každý obslužná rutina je volána službou vlákna uživatelského rozhraní. Nemusíte si dělat starosti při použití a MessageBox nebo něco související uživatelského rozhraní.
> 
> 

## <a id="push-channel-sharing"></a> Push sdílení kanálu
Pokud používáte nabízená oznámení pro jiný účel ve vaší aplikaci budete muset použít nabízenou kanál sdílení funkce sady Engagement SDK. Tím se vyhnete zmeškaných push.

* Můžete zadat vlastní kanál nabízené k inicializaci Engagement Reach. Sada SDK použije místo požaduje novou.

Aktualizovat inicializace Engagement Reach s nabízené kanál v `InitEngagement` metoda z `App.xaml.cs` souboru:

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* Případně pokud chcete využívat kanál nabízené po inicializaci Reach pak můžete nastavení zpětné volání na Engagement Reach získat kanál nabízené jednou vytváří se sadou SDK.

Nastavit vaše zpětné volání v jakémkoli místě **po** inicializace Reach:

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
        /* Your own push channel logic... */
      });
    }

## <a name="custom-scheme-tip"></a>Tip pro vlastní schéma
Poskytujeme použití vlastní schéma. Můžete odeslat jiný typ identifikátoru URI z front-endu engagement mají být použity v zapojení aplikace. Výchozí schéma jako `http, ftp, ...` jsou spravovat v systému Windows, bude okno řádku by šlo žádná výchozí aplikace nainstalované v zařízení. Můžete také vytvořit své vlastní schéma pro vaši aplikaci.

Jednoduchý způsob, jak nastavit vlastní schéma v aplikaci je otevřete váš `Package.appxmanifest` přejděte v `Declarations` panelu. Vyberte `Protocol` v dostupné deklarace posuňte pole a přidejte ji. Upravit `Name` pole s nový protokol požadovaný název.

Nyní Pokud chcete použít tento protokol, upravte vaše `App.xaml.cs` s `OnActivated` metoda a nezapomeňte také inicializovat engagement zde:

            /// <summary>
            /// Enter point when app his called by another way than user click
            /// </summary>
            /// <param name="args">Activation args</param>
            protected override void OnActivated(IActivatedEventArgs args)
            {
              /* Init engagement like it was launch by a custom uri scheme */
              EngagementAgent.Instance.Init(args);
              EngagementReach.Instance.Init(args);

              //TODO design action to do when app is launch

              #region Custom scheme use
              if (args.Kind == ActivationKind.Protocol)
              {
                ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;

                if (myProtocol.Uri.Scheme.Equals("protocolName"))
                {
                  string path = myProtocol.Uri.AbsolutePath;
                }
              }
              #endregion

