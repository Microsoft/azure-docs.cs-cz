---
title: Integraci sady Engagement SDK univerzálních aplikací pro Windows
description: Postup pro integraci Azure Mobile Engagement univerzálních aplikací pro Windows
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: dc075ffe6565b4ef60e77f74ba4f065629c99952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Integraci sady Engagement SDK univerzálních aplikací pro Windows
> [!IMPORTANT]
> Na 3/31/2018 IT vyřadí Azure Mobile Engagement. Tato stránka bude odstraněna krátce po.
> 

> [!div class="op_single_selector"]
> * [Univerzální platforma Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

Tento postup popisuje nejjednodušší způsob, jak aktivovat Engagement analýzy a monitorování funkce v aplikaci univerzální pro Windows.

Následující kroky jsou dost aktivovat sestavy protokolů, které jsou potřebné k výpočtu všechny statistické údaje týkající se uživatelů, relací, aktivity, dojde k chybě a Technicals. Sestava protokolů, které jsou potřebné k výpočtu další statistiky, jako jsou události a chyby úlohy je třeba provést ručně pomocí rozhraní API Engagement (najdete v části [jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci Windows Universal](mobile-engagement-windows-store-use-engagement-api.md) vzhledem k tomu, že tyto statistické údaje jsou závislé aplikace.

## <a name="supported-versions"></a>Podporované verze
Mobile Engagement SDK pro Windows Universal aplikace lze pouze integrovat do prostředí Windows Runtime a aplikací pro univerzální platformu Windows:

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10 (desktop a mobile rodiny)

> [!NOTE]
> Pokud jsou cílem Windows Phone Silverlight pak odkazovat [postup integrace Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md).
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Nainstalovat sadu SDK Mobile Engagement univerzální aplikace
### <a name="all-platforms"></a>Všechny platformy
Mobile Engagement SDK pro univerzální aplikace Windows je k dispozici jako balíček Nuget s názvem *MicrosoftAzure.MobileEngagement*. Můžete ho nainstalovat z Visual Studio Správce balíčků Nuget.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x a Windows Phone 8.1
NuGet automaticky nasadí prostředků sady SDK v `Resources` složku v kořenovém adresáři projektu aplikace.

### <a name="windows-10-universal-windows-platform-applications"></a>Aplikace Windows 10 univerzální platformu Windows
NuGet automaticky Nenasazuje prostředků sady SDK v aplikaci UWP ještě. Je nutné provést ručně dokud prostředky nasazení je znovu zavedena v NuGet:

1. Otevřete váš Průzkumníka souborů.
2. Přejděte do následujícího umístění (**x.x.x** je verze Engagement instalujete): *% USERPROFILE %\\.nuget\packages\MicrosoftAzure.MobileEngagement\\  **x.x.x**\\content\win81*
3. Přetáhnout myší **prostředky** složky v Průzkumníku souborů do kořenového adresáře projektu v sadě Visual Studio.
4. V sadě Visual Studio vyberte projekt a aktivovat **zobrazit všechny soubory** ikonu na **Průzkumníku řešení**.
5. Některé soubory nejsou zahrnuty v projektu. K importu je najednou klikněte pravým tlačítkem na **prostředky** složky, **vyloučit z projektu** pak jiné klikněte pravým tlačítkem na **prostředky** složky, **zahrnout do projektu** znovu zahrnout celou složku. Všechny soubory z **prostředky** složky jsou teď součástí projektu.

## <a name="add-the-capabilities"></a>Přidání funkcí
Sady Engagement SDK potřebuje některé funkce sady Windows SDK správné fungování.

Otevřete váš `Package.appxmanifest` souboru a ujistěte se, že jsou deklarovány následující možnosti:

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Inicializace Engagement SDK
### <a name="engagement-configuration"></a>Konfigurace zapojení
Konfigurace zapojení je centralizovaná v `Resources\EngagementConfiguration.xml` souboru projektu.

Upravte tento soubor k určení:

* Připojovací řetězec aplikací mezi značky `<connectionString>` a `<\connectionString>`.

Pokud chcete zadat za běhu, můžete volat metodu před Engagement inicializaci agenta:

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

Připojovací řetězec pro vaši aplikaci se zobrazí na portálu Azure.

### <a name="engagement-initialization"></a>Inicializace zapojení
Když vytvoříte nový projekt, `App.xaml.cs` se vygeneruje soubor. Tato třída dědí z `Application` a obsahuje mnoho důležité metody. Je také použije k chybě při inicializaci sady Engagement SDK.

Změnit `App.xaml.cs`:

* Přidat do vaší `using` příkazy:
  
      using Microsoft.Azure.Engagement;
* Zadejte způsob, jak sdílet inicializace Engagement jednou pro všechna volání:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* Volání `InitEngagement` v `OnLaunched` metoda:
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* Když je aplikace spuštěna pomocí vlastní schéma, jiná aplikace nebo příkazového řádku potom `OnActivated` metoda je volána. Musíte také zahájit sady Engagement SDK, když je aktivován vaší aplikace. Chcete-li to provést, přepište `OnActivated` metoda:
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> Jsme důrazně bránit můžete přidat inicializace Engagement na jiném místě vaší aplikace.
> 
> 

## <a name="basic-reporting"></a>Vytváření základních sestav
### <a name="recommended-method-overload-your-page-classes"></a>Doporučená metoda: přetížení vaší `Page` třídy
Chcete-li aktivovat sestavu všechny protokoly, které vyžadují zapojení k výpočtu uživatelů, relací, aktivity, dojde k chybě a technické statistiky, můžete jednoduše provést všechny vaše `Page` dílčí třídy dědí `EngagementPage` třídy.

Tady je příklad toho, jak to udělat pro stránku vaší aplikace. Můžete to samé pro všechny stránky vaší aplikace.

#### <a name="c-source-file"></a>C# zdrojový soubor
Upravit stránku `.xaml.cs` souboru:

* Přidat do vaší `using` příkazy:
  
      using Microsoft.Azure.Engagement;
* Nahraďte `Page` s `EngagementPage`:

**Bez Engagement:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**S Engagement:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage 
          {
            [...]
          }
        }

> [!IMPORTANT]
> Pokud stránka přepíše metodu `OnNavigatedTo`, nezapomeňte volat `base.OnNavigatedTo(e)`. Jinak aktivita nebude hlášena (`EngagementPage` volá `StartActivity` uvnitř své metody `OnNavigatedTo`).
> 
> 

#### <a name="xaml-file"></a>Souboru XAML
Upravit stránku `.xaml` souboru:

* Přidejte do deklarací oborů názvů:
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* Nahraďte `Page` s `engagement:EngagementPage`:

**Bez Engagement:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**S Engagement:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

#### <a name="override-the-default-behaviour"></a>Přepsat výchozí chování
Ve výchozím nastavení je název třídy stránky uvedená jako název aktivity, s žádné další. Pokud třída používá příponou "Stránka", Engagement také odebere.

Pokud chcete přepsat výchozí chování pro název, jednoduše přidejte tuto kódu:

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

Pokud chcete ohlásit některé další – informace s vaší aktivitou, můžete to přidat do kódu:

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

Tyto metody jsou volány prostřednictvím `OnNavigatedTo` metoda stránky.

### <a name="alternate-method-call-startactivity-manually"></a>Alternativní metoda: volání `StartActivity()` ručně
Pokud nemůžete nebo nechcete přetížení vaše `Page` třídy, můžete místo toho spustit vaše aktivity voláním `EngagementAgent` metody přímo.

Doporučujeme, abyste k volání `StartActivity` uvnitř vaší `OnNavigatedTo` metoda stránky.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> Zajistěte, aby že správně ukončete svou relaci.
> 
> Sady Windows Universal SDK automaticky zavolá `EndActivity` metoda při ukončení aplikace. Z toho důvodu je **vysoce** doporučuje volat `StartActivity` metoda vždy, když aktivita uživatele změnit a **nikdy** volání `EndActivity` metodu, tato metoda odešle na server zapojení, že aktuální uživatel má nechat aplikaci, budou ovlivňuje všechny protokoly aplikací.
> 
> 

## <a name="advanced-reporting"></a>Rozšířená tvorba sestav
Volitelně můžete chtít sestav aplikace konkrétní události, chyb a úlohy, Uděláte to tak, použijte jiné metody v nalezen `EngagementAgent` třídy. Rozhraní API Engagement umožňuje používat všechny rozšířené možnosti Engagement.

Další informace najdete v tématu [jak používat rozšířené Mobile Engagement označování rozhraní API v aplikaci Windows Universal](mobile-engagement-windows-store-use-engagement-api.md).

## <a name="advanced-configuration"></a>Pokročilá konfigurace
### <a name="disable-automatic-crash-reporting"></a>Zakázat automatické hlášení chyb
Můžete vypnout automatické hlášení funkci Engagement generování sestav. Pak když dojde k neošetřené výjimce, Engagement nebude provádět žádné kroky.

> [!WARNING]
> Pokud budete chtít tuto funkci zakázat, mějte na paměti, když dojde k nezpracované chybě ve vaší aplikaci, nepošle Engagement havárii **a** nebude zavřete relaci a úlohy.
> 
> 

Chcete-li zakázat automatické hlášení chyb, upravte konfiguraci. v závislosti na způsobu, jakým jste ji deklarovaný:

#### <a name="from-engagementconfigurationxml-file"></a>Z `EngagementConfiguration.xml` souboru
Nastavte Sestavy havárií na `false` mezi `<reportCrash>` a `</reportCrash>` značky.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>Z `EngagementConfiguration` objektu v době běhu
Nastavit na hodnotu false pomocí objektu EngagementConfiguration sestavy havárií.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>Režim shluku
Ve výchozím nastavení sestavy služby zapojení protokolů v reálném čase. Pokud vaše aplikace hlásí protokoly velmi často, je lepší ukládat do vyrovnávací paměti protokoly a sestavy je všechny najednou pravidelné základní časové (to se nazývá "shluků režim").

Chcete-li tak učinit, zavolejte metodu:

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

Argument je hodnota **milisekundách**. Kdykoli Pokud chcete znovu aktivovat protokolování v reálném čase, stačí zavoláte metoda bez zadání parametru nebo s hodnotou 0.

Režim shluků mírně zvýšit výdrž baterie, ale má vliv na zapojení monitorování: všechny dobu trvání relace a úlohy zaokrouhlen shluků prahovou hodnotu (tedy relací a úlohy kratší, než je prahová hodnota shluků nemusí být zobrazeny). Doporučujeme používat práh shluků než 30000 (30s). Budete muset mějte na paměti, uložené protokoly jsou omezeny na 300 položek. Pokud odesílání je příliš dlouhý. může dojít ke ztrátě některých protokoly.

> [!WARNING]
> Prahová hodnota shluků nelze nakonfigurovat na dobu, která je menší než hodnotami 1. Pokud se pokusíte učinit, sady SDK se zobrazí trasování došlo k chybě a automaticky obnoví na výchozí hodnotu, tedy 0s. Tím se aktivuje sady SDK k hlášení protokolů v reálném čase.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

