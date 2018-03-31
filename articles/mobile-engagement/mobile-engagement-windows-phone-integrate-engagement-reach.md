---
title: Windows Phone Silverlight Reach integraci sady SDK
description: Postup při integraci Azure Mobile Engagement Reach s aplikacemi pro Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 34e5ce414ebf72fbecef6c921e57128e2658c921
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone Silverlight Reach integraci sady SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

Je třeba provést postup integrace popsaný v tématu [integraci sady Windows Phone Silverlight Engagement SDK](mobile-engagement-windows-phone-integrate-engagement.md) před těchto pokynů.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Vložení Engagement Reach SDK do projektu Windows Phone Silverlight
Nemáte nic přidat. `EngagementReach` odkazy a prostředky jsou už ve vašem projektu.

> [!TIP]
> Můžete přizpůsobit bitové kopie, které jsou umístěné v `Resources` složky projektu, zejména ikonu značky (této výchozí ikonu Engagement).
> 
> 

## <a name="add-the-capabilities"></a>Přidání funkcí
Engagement Reach SDK potřebuje některé další funkce.

Otevřete váš `WMAppManifest.xml` souboru a ujistěte se, že jsou deklarovány následující možnosti:

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

První z nich se používá služba MPNS povolit zobrazení oznámení informační zprávy. Druhá slouží k vložení úlohu prohlížeče do sady SDK.

Upravit `WMAppManifest.xml` souboru a přidejte uvnitř `<Capabilities />` značky:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Povolit službu Microsoft Push Notification Service
Chcete-li použít **Microsoft službu nabízených oznámení** (označované jako MPNS) vaší `WMAppManifest.xml` soubor musí mít `<App />` značku s `Publisher` atributu nastavena na název projektu.

## <a name="initialize-the-engagement-reach-sdk"></a>Inicializace Engagement Reach SDK
### <a name="engagement-configuration"></a>Konfigurace zapojení
Konfigurace zapojení je centralizovaná v `Resources\EngagementConfiguration.xml` souboru projektu.

Upravte tento soubor k určení reach konfigurace:

* *Volitelné*, zda je aktivována nativního nabízení (MPNS) nebo není mezi `<enableNativePush>` a `</enableNativePush>` značky, (`true` ve výchozím nastavení).
* *Volitelné*, označení názvu nabízené kanál mezi `<channelName>` a `</channelName>` značky, poskytují stejné, vaše aplikace může aktuálně použít nebo hodnotu nevyplňujte.

Pokud chcete zadat za běhu, můžete volat metodu před Engagement inicializaci agenta:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> Můžete zadat název kanálu nabízené MPNS vaší aplikace. Ve výchozím nastavení vytvoří název založený na appId zapojení. Nemáte žádné potřeba zadat název sami, s výjimkou Pokud plánujete použít kanál nabízené mimo zapojení.
> 
> 

### <a name="engagement-initialization"></a>Inicializace zapojení
Změnit `App.xaml.cs`:

* Přidat do vaší `using` příkazy:
  
      using Microsoft.Azure.Engagement;
* Vložit `EngagementReach.Instance.Init` právě po `EngagementAgent.Instance.Init` v `Application_Launching` :
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* Vložit `EngagementReach.Instance.OnActivated` v `Application_Activated` metoda:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> `EngagementReach.Instance.Init` Běží ve vyhrazené vlákno. Nemusíte dělat sami.
> 
> 

## <a name="app-store-submission-considerations"></a>Aspekty odeslání úložiště aplikací
Microsoft ukládá některá pravidla při používání nabízených oznámení:

Z Microsoft [zásady aplikací] části 2.9, dokumentace:

1) Musíte požádat uživatele tak, aby přijímal přijímat nabízená oznámení. Potom si v nastaveních, přidejte způsob, jak zakázat nabízená oznámení.

Objekt EngagementReach nabízí dvě metody pro správu opt souhlas/nesouhlas, `EnableNativePush()` a `DisableNativePush()`. Možnost může například vytvořit v nastavení s přepínání zakázat nebo povolit MPNS.

Můžete také rozhodnout deaktivovat MPNS prostřednictvím konfigurace Engagement\<windows phone-sdk-reach konfigurace\>.

> 2.9.1) aplikace musí nejprve popisují oznámení, musíte zadat a **získat express oprávnění (výslovný souhlas)**, a **mechanismus, pomocí kterého uživatel může vyjádření výslovného nesouhlasu s nabízená oznámení musíte zadat**. Všechna oznámení zadaný pomocí Microsoft službu nabízených oznámení musí být v souladu s uživateli poskytnutý popis a musí dodržovat všechny příslušné [zásady aplikací] [ Content Policies] a [další požadavky pro konkrétní typy aplikací].
> 
> 

2) Neměli byste používat příliš mnoho nabízená oznámení. Zapojení bude zpracovávat oznámení za vás.

> 2.9.2) aplikace a jeho použití programu Microsoft službu nabízených oznámení nesmí nadměrně použít dostatečnou kapacitu sítě nebo šířka pásma Microsoft službu nabízených oznámení, nebo jinak neoprávněně nebyli Windows Phone nebo jiné zařízení Microsoft nebo služby s nadměrné nabízená oznámení, jak stanoví společnost Microsoft dle svého uvážení a musí poškodit nebo narušit žádné sítě, Microsoft nebo servery nebo serverech třetích stran nebo sítě připojené k Microsoft službu nabízených oznámení.
> 
> 

3) Nespoléhejte na MPNS k odeslání informací o criticals. Zapojení používá MPNS, takže toto pravidlo platí také pro kampaně vytvářet v zapojení front-endu.

> Microsoft službu nabízených oznámení 2.9.3) nemusí být používá k odesílání oznámení, které jsou zvláště důležité nebo jinak mohou ovlivnit záležitosti životnosti nebo úmrtí, včetně bez omezení kritické oznámení souvisejících s lékařské zařízení nebo podmínku. MICROSOFT VÝSLOVNĚ ZŘÍKÁ JAKÉKOLI ZÁRUKY, POUŽITÍ MICROSOFT NABÍZENÁ OZNÁMENÍ SLUŽBY NEBO DORUČOVÁNÍ OZNÁMENÍ O SLUŽBÁCH MICROSOFT NABÍZENÁ OZNÁMENÍ BUDE BEZ PŘERUŠENÍ, BEZ CHYB, NEBO JINAK ZÁRUKU, ŽE DOJDE K NA ZÁKLADĚ V REÁLNÉM ČASE.
> 
> 

**Nemůžeme zaručit, že vaše aplikace předá proces ověření Pokud nerespektují tato doporučení.**

## <a name="handle-data-push-optional"></a>Zpracování datová oznámení (volitelné)
Pokud chcete aplikace nebudou moct přijímat Reach datová oznámení, je nutné implementovat dvě události EngagementReach třídy:

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

Potom nastavte obsah `EngagementReach.Instance.Handler` pole s vaší vlastní objekt v vaše `App.xaml.cs` třídy v rámci `Application_Launching` metoda.

**Ukázkový kód:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Ve výchozím nastavení používá Engagement jejich vlastní implementaci `EngagementReachHandler`. Nemusíte vytvářet vlastní, a pokud tak učiníte, nemusíte přepsat každou metodu. Výchozí chování je výběr základní objekt zapojení.
> 
> 

### <a name="layouts"></a>Rozložení
Ve výchozím nastavení použije Reach vložené prostředky knihovny DLL k zobrazení oznámení a stránky.

Můžete však použít vlastní prostředky tak, aby odrážela vaší značkou v těchto součástí.

Můžete přepsat `EngagementReachHandler` metody v vaše podtřída říct Engagement používat vaše rozložení:

**Ukázkový kód:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> `CreateNotification` Metoda může vrátit hodnotu null. Oznámení se nezobrazí a budou vynechána kampaně reach.
> 
> 

Pro zjednodušení implementace rozložení, poskytujeme také vlastní xaml, který může sloužit jako základ pro váš kód. Se nacházejí v archivu Engagement SDK (/ src/reach /).

> [!WARNING]
> Zdroje, které poskytujeme jsou přesně stejnou ty, které používáme. Takže pokud chcete změnit přímo, nezapomeňte změnit obor názvů a název.
> 
> 

### <a name="notification-position"></a>Pozice oznámení
Ve výchozím nastavení zobrazí se ve spodní levé straně aplikace oznámení v aplikaci. Toto chování můžete změnit přepsání `GetNotificationPosition` metodu `EngagementReachHandler` objektu.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

V současné době můžete zvolit, jestli `BOTTOM` (výchozí) a `TOP` pozic.

### <a name="launch-message"></a>Spusťte zprávu
Když uživatel klikne na systémové oznámení (oznámení), Engagement spuštění aplikace, obsah zprávy nabízené načíst a zobrazit stránku pro odpovídající kampaně.

Dochází ke zpoždění mezi spuštění aplikace a zobrazení stránky (v závislosti na rychlosti sítě).

Chcete-li uživatele upozornit, načítání něco, by měl poskytovat vizuální informace, jako je indikátor průběhu nebo indikátor průběhu. Zapojení nemůže zpracovat samostatně, ale poskytuje několik obslužné rutiny pro vás.

Chcete-li implementovat zpětné volání, proveďte:

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

Zpětné volání můžete nastavit v vaše `Application_Launching` metodu vaše `App.xaml.cs` soubor, pokud možno před `EngagementReach.Instance.Init()` volání.

> [!TIP]
> Každý obslužná rutina je volána službou vlákna uživatelského rozhraní. Nemusíte si dělat starosti při použití a MessageBox nebo něco související uživatelského rozhraní.
> 
> 

[zásady aplikací]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[další požadavky pro konkrétní typy aplikací]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

