---
title: Integraci sady Windows Phone Silverlight Engagement SDK
description: Postup při integraci Azure Mobile Engagement s aplikacemi pro Windows Phone Silverlight
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 447fea8d-f4e3-4ad4-8ec0-8e3cf1ad3ab0
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: f041f19ceff366a34b1761bfdca12242f4bdf9b8
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-engagement-sdk-integration"></a>Integraci sady Windows Phone Silverlight Engagement SDK
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální pro Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Tento postup popisuje nejjednodušší způsob, jak aktivovat Azure Mobile Engagement analýzy a monitorování funkce v aplikaci Silverlight pro Windows Phone.

Následující kroky jsou dost aktivovat sestavy protokolů, které jsou potřebné k výpočtu všechny statistické údaje týkající se uživatelů, relací, aktivity, dojde k chybě a Technicals. Sestavy protokolů, které jsou potřebné k výpočtu další statistiky, jako jsou události a chyby úlohy je třeba provést ručně pomocí rozhraní API Engagement (najdete v části [jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md) níže) vzhledem k tomu, že tyto statistické údaje jsou závislé aplikace.

## <a name="supported-versions"></a>Podporované verze
Mobile Engagement SDK pro Windows aplikace Silverlight lze pouze integrovat do aplikace cílený na:

* Windows Phone 8.0
* Windows Phone 8.1 Silverlight

> [!NOTE]
> Pokud je cílem Windows Phone 8.1 (bez Silverlight) můžete odkazovat [univerzální pro Windows postup integrace](mobile-engagement-windows-store-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-silverlight-sdk"></a>Nainstalovat sadu SDK Mobile Engagement Silverlight
Mobile Engagement SDK pro Windows aplikace Silverlight je k dispozici jako balíček Nuget s názvem *MicrosoftAzure.MobileEngagement*. Můžete ho nainstalovat z Visual Studio Správce balíčků Nuget. 

## <a name="add-the-capabilities"></a>Přidání funkcí
Sady Engagement SDK potřebuje některé funkce sady Windows Phone Silverlight SDK správné fungování.

Otevřete váš `WMAppManifest.xml` souboru a ujistěte se, že tyto možnosti jsou deklarované v `Capabilities` panelu:

* `ID_CAP_NETWORKING`
* `ID_CAP_IDENTITY_DEVICE`

## <a name="initialize-the-engagement-sdk"></a>Inicializace Engagement SDK
### <a name="engagement-configuration"></a>Konfigurace zapojení
Konfigurace zapojení je centralizovaná v `Resources\EngagementConfiguration.xml` souboru projektu.

Upravte tento soubor k určení:

* Připojovací řetězec aplikací mezi značky `<connectionString>` a `<\connectionString>`.

Pokud chcete zadat za běhu, můžete volat metodu před Engagement inicializaci agenta:

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

Připojovací řetězec pro vaši aplikaci se zobrazí na portálu Azure.

### <a name="engagement-initialization"></a>Inicializace zapojení
Když vytvoříte nový projekt, `App.xaml.cs` se vygeneruje soubor. Tato třída dědí z `Application` a obsahuje mnoho důležité metody. Je také použije k chybě při inicializaci sady Engagement SDK.

Změnit `App.xaml.cs`:

* Přidat do vaší `using` příkazy:
  
      using Microsoft.Azure.Engagement;
* Vložit `EngagementAgent.Instance.Init` v `Application_Launching` metoda:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
        EngagementAgent.Instance.Init();
      }
* Vložit `EngagementAgent.Instance.OnActivated` v `Application_Activated` metoda:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
      }

> [!WARNING]
> Jsme důrazně bránit můžete přidat inicializace Engagement na jiném místě vaší aplikace. Ale pozor, který `EngagementAgent.Instance.Init` metoda se spouští na vyhrazené vlákno a ne na vlákna uživatelského rozhraní.
> 
> 

## <a name="basic-reporting"></a>Vytváření základních sestav
### <a name="recommended-method--overload-your-phoneapplicationpage-classes"></a>Doporučená metoda: přetížení vaší `PhoneApplicationPage` třídy
Chcete-li aktivovat sestavu všechny protokoly, které vyžadují zapojení k výpočtu uživatelů, relací, aktivity, dojde k chybě a technické statistiky, můžete jednoduše provést všechny vaše `PhoneApplicationPage` dílčí třídy dědí `EngagementPage` třídy.

Tady je příklad toho, jak to udělat pro stránku vaší aplikace. Můžete to samé pro všechny stránky vaší aplikace.

#### <a name="c-source-file"></a>C# zdrojový soubor
Upravit stránku `.xaml.cs` souboru:

* Přidat do vaší `using` příkazy:
  
      using Microsoft.Azure.Engagement;
* Nahraďte `PhoneApplicationPage` s `EngagementPage` :

**Bez Engagement:**

        namespace Example
        {
          public partial class ExamplePage : PhoneApplicationPage
          {
            [...]
          }
        }

**S Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!WARNING]
> Pokud stránka dědí z `OnNavigatedTo` metody dávejte pozor, abyste mohli `base.OnNavigatedTo(e)` volání. Jinak nebudou ohlášena aktivity. Ve skutečnosti `EngagementPage` volá `StartActivity` uvnitř `OnNavigatedTo` metoda.
> 
> 

#### <a name="xaml-file"></a>Souboru XAML
Upravit stránku `.xaml` souboru:

* Přidejte do deklarací oborů názvů:
  
      xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
* Nahraďte `phone:PhoneApplicationPage` s `engagement:EngagementPage` :

**Bez Engagement:**

        <phone:PhoneApplicationPage>
            <!-- layout -->
        </phone:PhoneApplicationPage>

**S Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP">

            <!-- layout -->
        </engagement:EngagementPage >

#### <a name="override-the-default-behavior"></a>Přepsat výchozí chování
Ve výchozím nastavení je název třídy stránky uvedená jako název aktivity, s žádné další. Pokud třída používá příponou "Stránka", Engagement také odebere.

Pokud chcete přepsat výchozí chování pro název, jednoduše přidejte tuto kódu:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
           /* your code */
           return "new name";
        }

Pokud chcete ohlásit některé doplňující informace s vaší aktivitou, můžete to přidat do kódu:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
           /* your code */
           return extra;
        }

Tyto metody jsou volány prostřednictvím `OnNavigatedTo` metoda stránky.

### <a name="alternate-method-call-startactivity-manually"></a>Alternativní metoda: volání `StartActivity()` ručně
Pokud nemůžete nebo nechcete přetížení vaše `PhoneApplicationPage` třídy, můžete místo toho spustit vaše aktivity voláním `EngagementAgent` metody přímo.

Doporučujeme, abyste volání `StartActivity` uvnitř vaší `OnNavigatedTo` metoda vaší PhoneApplicationPage.

        protected override void OnNavigatedTo(NavigationEventArgs e)
        {
           base.OnNavigatedTo(e);
           EngagementAgent.Instance.StartActivity("MyPage");
        }

> [!IMPORTANT]
> Zajistěte, aby že správně ukončete svou relaci.
> 
> Sada SDK automaticky zavolá `EndActivity` metoda při ukončení aplikace. Z toho důvodu je **vysoce** doporučuje volat `StartActivity` metoda vždy, když aktivita uživatele změnit a **nikdy** volání `EndActivity` metoda. Tato metoda odešle zprávu do serveru zapojení, že má aktuální uživatel opustil aplikace, a to ovlivní všechny protokoly aplikací.
> 
> 

## <a name="advanced-reporting"></a>Rozšířená tvorba sestav
Volitelně můžete chtít sestav aplikace konkrétní události, chyb a úlohy, Uděláte to tak, použijte jiné metody v nalezen `EngagementAgent` třídy. Rozhraní API Engagement umožňuje používat všechny rozšířené možnosti Engagement.

Další informace najdete v tématu [jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci Windows Phone Silverlight](mobile-engagement-windows-phone-use-engagement-api.md).

## <a name="advanced-configuration"></a>Pokročilá konfigurace
### <a name="disable-automatic-crash-reporting"></a>Zakázat automatické hlášení chyb
Můžete vypnout automatické hlášení funkci Engagement generování sestav. Pak když dojde k neošetřené výjimce, Engagement nebude provádět žádné kroky.

> [!WARNING]
> Pokud budete chtít tuto funkci zakázat, mějte na paměti, když dojde k nezpracované chybě ve vaší aplikaci, nepošle Engagement havárii **a** zavřít relaci a úlohy.
> 
> 

Chcete-li zakázat automatické hlášení chyb, upravte konfiguraci. v závislosti na způsobu, jakým jste ji deklarovaný:

#### <a name="from-engagementconfigurationxml-file"></a>Z `EngagementConfiguration.xml` souboru
Nastavte Sestavy havárií na `false` mezi `<reportCrash>` a `</reportCrash>` značky.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Z `EngagementConfiguration` objektu v době běhu
Nastavit na hodnotu false pomocí objektu EngagementConfiguration sestavy havárií.

        /* Engagement configuration. */

        EngagementConfiguration engagementConfiguration = new EngagementConfiguration(); engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
        /\* Disable Engagement crash reporting. \*/ engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Režim shluku
Ve výchozím nastavení sestavy služby zapojení protokolů v reálném čase. Pokud vaše aplikace hlásí protokoly velmi často, je lepší ukládat do vyrovnávací paměti protokoly a sestavy je všechny najednou pravidelné základní časové (to se nazývá "shluků režim").

Chcete-li tak učinit, zavolejte metodu:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argument je hodnota **milisekundách**. Kdykoli Pokud chcete znovu aktivovat protokolování v reálném čase, stačí zavoláte metoda bez zadání parametru nebo s hodnotou 0.

Režim shluků mírně zvýšit výdrž baterie, ale má vliv na zapojení monitorování: všechny dobu trvání relace a úlohy zaokrouhlen shluků prahovou hodnotu (tedy relací a úlohy kratší, než je prahová hodnota shluků nemusí být zobrazeny). Doporučujeme používat práh shluků než 30000 (30s). Budete muset mějte na paměti, uložené protokoly jsou omezeny na 300 položek. Pokud odesílání je příliš dlouhý. může dojít ke ztrátě některých protokoly.

> [!WARNING]
> Prahová hodnota shluků nelze nakonfigurovat na dřívější dobu než jedna sekunda. Pokud se pokusíte tím, že sada SDK zobrazí trasování se chyba a bude automaticky resetovat na výchozí hodnotu, který je nula sekund. Tím se aktivuje sady SDK k hlášení protokolů v reálném čase.
> 
> 

