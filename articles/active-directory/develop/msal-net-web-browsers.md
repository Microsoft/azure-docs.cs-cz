---
title: Používání webových prohlížečů (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních aspektech při používání Xamarin Android s Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262734"
---
# <a name="using-web-browsers-msalnet"></a>Používání webových prohlížečů (MSAL.NET)

Pro interaktivní ověřování jsou vyžadovány webové prohlížeče. Ve výchozím nastavení MSAL.NET podporuje [systémový webový prohlížeč](#system-web-browser-on-xamarinios-xamarinandroid) na Xamarin.iOS a Xamarin.Android. Ale [můžete také povolit vložený webový prohlížeč](#enable-embedded-webviews-on-ios-and-android) v závislosti na vašich požadavcích (UX, potřeba jednotného přihlašování (SSO), zabezpečení) v aplikacích [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) a [Xamarin.Android.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) A můžete si dokonce [dynamicky vybrat,](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) který webový prohlížeč použít na základě přítomnosti Chrome nebo prohlížeč podporující Vlastní záložky Chrome v systému Android. MSAL.NET podporuje pouze systémový prohlížeč v desktopových aplikacích .NET Core.

## <a name="web-browsers-in-msalnet"></a>Webové prohlížeče v MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interakce probíhá ve webovém prohlížeči

Je důležité si uvědomit, že při získávání tokenu interaktivně obsah dialogového okna není poskytována knihovnou, ale STS (Security Token Service). Koncový bod ověřování odešle zpět některé HTML a JavaScript, který řídí interakci, která je vykreslena ve webovém prohlížeči nebo webovém ovládacím prvku. Povolení STS zvládnout interakci HTML má mnoho výhod:

- Heslo (pokud bylo zadáno) není nikdy uloženo aplikací ani ověřovací knihovnou.
- Umožňuje přesměrování na jiné poskytovatele identit (například přihlášení pomocí účtu pracovní školy nebo osobního účtu u MSAL nebo se sociálním účtem s Azure AD B2C).
- Umožňuje řízení sts podmíněného přístupu, například tím, že uživatel provést vícefaktorové ověřování (MFA) během fáze ověřování (zadání Windows Hello pin, nebo volání na jejich telefonu nebo na ověřování aplikace na svém telefonu). V případech, kdy není požadované vícefaktorové ověřování ještě nastaveno, může uživatel nastavit právě včas ve stejném dialogovém okně.  Uživatel zadá číslo svého mobilního telefonu a je veden k instalaci ověřovací aplikace a skenování qr značky pro přidání svého účtu. Tento server řízený interakce je skvělý zážitek!
- Umožňuje uživateli změnit své heslo v tomto dialogovém okně po vypršení platnosti hesla (poskytuje další pole pro staré heslo a nové heslo).
- Umožňuje značky klienta nebo aplikace (image) řízené správce množin y nebo vlastníkem aplikace Azure AD.
- Umožňuje uživatelům souhlas s přístupem aplikace k prostředkům / oborům v jejich názvu těsně po ověření.

### <a name="embedded-vs-system-web-ui"></a>Vložené vs systémové webové uživatelské nastavení

MSAL.NET je víceoborová knihovna a má kód specifický pro architekturu pro hostování prohlížeče v ovládacím prvku uživatelského rozhraní (například na .Net Classic používá WinForms, na Xamarinu používá nativní mobilní ovládací prvky atd.). This control is called `embedded` web UI. Alternativně, MSAL.NET je také schopen nastartovat systém OS prohlížeč.

Obecně se doporučuje použít výchozí platformu, a to je obvykle systémový prohlížeč. Systémový prohlížeč je lepší při zapamatování uživatelů, kteří se přihlásili dříve. Pokud potřebujete toto chování změnit, použijte`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Na první pohled

| Rozhraní .NET Framework        | Vložené | Systém | Výchozí |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Ano | Ano^ | Vložené |
| .NET Core     | Ne | Ano^ | Systém |
| .NET Standard | Ne | Ano^ | Systém |
| UWP | Ano | Ne | Vložené |
| Xamarin.Android | Ano | Ano  | Systém |
| Xamarin.iOS | Ano | Ano  | Systém |
| Xamarin.Mac| Ano | Ne | Vložené |

^ Vyžadujehttp://localhostidentifikátor URI přesměrování

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systémový webový prohlížeč na Xamarin.iOS, Xamarin.Android

Ve výchozím nastavení podporuje MSAL.NET systémový webový prohlížeč na xamarin.iOS, Xamarin.Android a .NET Core. Pro všechny platformy, které poskytují uživatelské rozhraní (to znamená, že ne .NET Core), dialogové okno je poskytována knihovny vkládání ovládacího prvku webového prohlížeče. MSAL.NET také používá vložené webové zobrazení pro .NET Desktop a WAB pro platformu UPW. Ve výchozím nastavení však využívá **systémový webový prohlížeč** pro aplikace Xamarin iOS a Xamarin Android. V systému iOS dokonce zvolí webové zobrazení, které se má použít v závislosti na verzi operačního systému (iOS12, iOS11 a starší).

Použití systémového prohlížeče má významnou výhodu sdílení stavu spřimit s jinými aplikacemi a s webovými aplikacemi bez nutnosti makléře (Portál společnosti / Authenticator). Systémový prohlížeč byl ve výchozím nastavení používán v MSAL.NET pro platformy Xamarin iOS a Xamarin Android, protože na těchto platformách systémový webový prohlížeč zabírá celou obrazovku a uživatelská zkušenost je lepší. Webové zobrazení systému nelze odlišit od dialogu. V iOS však uživatel může mít souhlas s prohlížečem, aby zavolal zpět aplikaci, což může být nepříjemné.

## <a name="system-browser-experience-on-net-core"></a>Systémový prohlížeč na rozhraní .NET Core

V centru .NET spustí MSAL.NET systémový prohlížeč jako samostatný proces. MSAL.NET nemá kontrolu nad tímto prohlížečem, ale jakmile uživatel dokončí ověřování, webová stránka je přesměrována takovým způsobem, že MSAL.NET může zachytit Uri.

Můžete také nakonfigurovat aplikace napsané pro rozhraní .NET Classic tak, aby používaly tento prohlížeč,

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET nelze zjistit, zda uživatel přejde pryč nebo jednoduše zavře prohlížeč. Aplikace používající tuto techniku se doporučuje `CancellationToken`definovat časový čas (přes). Doporučujeme časový rozsah alespoň několik minut, aby se vzal v úvahu případy, kdy je uživatel vyzván ke změně hesla nebo provést vícefaktorové ověřování.

### <a name="how-to-use-the-default-os-browser"></a>Jak používat výchozí prohlížeč operačního systému

MSAL.NET musí naslouchat `http://localhost:port` a zachytit kód, který AAD odešle po dokončení ověřování uživatele (podrobnosti naleznete v [autorizačním kódu).](v2-oauth2-auth-code-flow.md)

Povolení systémového prohlížeče:

1. Během registrace aplikace `http://localhost` nakonfigurujte jako identifikátor uri přesměrování (aktuálně nepodporovaný b2c)
2. Při vytváření aplikace PublicClientApplication zadejte tento identifikátor uri přesměrování:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Pokud nakonfigurujete `http://localhost`, interně MSAL.NET najde náhodný otevřený port a použije jej.

### <a name="linux-and-mac"></a>Linux a MAC

Na Linuxu MSAL.NET otevře výchozí prohlížeč operačního systému pomocí nástroje xdg-open. Chcete-li vyřešit potíže, spusťte nástroj z terminálu, například`xdg-open "https://www.bing.com"`  
Na Macu se prohlížeč otevře vyvoláním`open <url>`

### <a name="customizing-the-experience"></a>Přizpůsobení prostředí

> [!NOTE]
> Vlastní nastavení je k dispozici v MSAL.NET 4.1.0 nebo novějším.

MSAL.NET je schopen odpovědět zprávou HTTP při přijetí tokenu nebo v případě chyby. Můžete zobrazit zprávu HTML nebo přesměrovat na adresu URL podle vašeho výběru:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Otevření konkrétního prohlížeče (Experimentální)

Způsob, jakým MSAL.NET prohlížeč otevírá, si můžete přizpůsobit. Například místo použití libovolného prohlížeče je výchozí, můžete vynutit otevření konkrétního prohlížeče:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>Upw nepoužívá systémwebview

U aplikací klasické pracovní plochy však spuštění webového zobrazení systému vede k podřadné uživatelské prostředí, protože uživatel vidí prohlížeč, kde již mohou mít jiné karty otevřené. A když dojde k ověření, uživatelé zdostane stránku s žádostí o zavření tohoto okna. Pokud uživatel nevěnuje pozornost, může ukončit celý proces (včetně dalších karet, které nesouvisejí s ověřováním). Využití systémového prohlížeče na ploše by také vyžadovalo otevření místních portů a naslouchání na nich, což může vyžadovat pokročilá oprávnění pro aplikaci. Vy jako vývojář, uživatel nebo správce se můžete zdráhat o tomto požadavku.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Povolení vložených webových zobrazení v systémech iOS a Android

Můžete také povolit vložené webové zobrazení v aplikacích Xamarin.iOS a Xamarin.Android. Počínaje MSAL.NET 2.0.0-preview, MSAL.NET také podporuje použití **možnosti vloženého** webového zobrazení. Pro ADAL.NET je vložené webové zobrazení jedinou podporovanou možností.

Jako vývojář, který používá MSAL.NET cílení na Xamarin, můžete použít buď vložená webová zobrazení, nebo systémové prohlížeče. Toto je vaše volba v závislosti na uživatelském prostředí a bezpečnostních problémech, na které chcete cílit.

V současné době MSAL.NET ještě nepodporuje makléře Android a iOS. Proto pokud potřebujete poskytnout jednotné přihlašování (SSO), systémový prohlížeč může být stále lepší volbou. Podpora makléři s vloženým webovým prohlížečem je na MSAL.NET nevyřízených položek.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Rozdíly mezi vloženým webovým zobrazením a systémovým prohlížečem
Existují určité vizuální rozdíly mezi vloženým webovým zobrazením a systémovým prohlížečem v MSAL.NET.

**Interaktivní přihlášení s MSAL.NET pomocí vloženého webového view:**

![vložený](media/msal-net-web-browsers/embedded-webview.png)

**Interaktivní přihlášení s MSAL.NET pomocí systémového prohlížeče:**

![Systémový prohlížeč](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Možnosti pro vývojáře

Jako vývojář používající MSAL.NET máte několik možností pro zobrazení interaktivního dialogu ze služby STS:

- **Systémový prohlížeč.** Systémový prohlížeč je ve výchozím nastavení nastaven v knihovně. Pokud používáte Android, přečtěte si [systémové prohlížeče](msal-net-system-browser-android-considerations.md) pro konkrétní informace o tom, které prohlížeče jsou podporovány pro ověřování. Při použití systémového prohlížeče v systému Android doporučujeme, aby zařízení bylo mít prohlížeč, který podporuje vlastní karty Chrome.  V opačném případě může dojít k selhání ověřování.
- **Vložené webové zobrazení.** Chcete-li použít pouze vložené `AcquireTokenInteractively` webové zobrazení v `WithUseEmbeddedWebView()` MSAL.NET, tvůrce parametrů obsahuje metodu.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Výběr mezi vloženým webovým prohlížečem nebo systémem na Xamarin.iOS

V aplikaci pro iOS můžete `AppDelegate.cs` v `ParentWindow` `null`aplikaci na aplikaci to inicializovat . Nepoužívá se v iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Výběr mezi vloženým webovým prohlížečem nebo systémovým prohlížečem na Xamarin.Android

V aplikaci pro `MainActivity.cs` Android můžete v aplikaci pro Android nastavit nadřazenou aktivitu tak, aby se k ní vrátil výsledek ověřování:

```csharp
 App.ParentWindow = this;
```

Pak v `MainPage.xaml.cs`:

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Detekce přítomnosti vlastních záložek na Xamarin.Android

Pokud chcete použít systémový webový prohlížeč k povolení vlastního přihlašování s aplikacemi spuštěnými v prohlížeči, ale obáváte se uživatelského `IsSystemWebViewAvailable()` prostředí `IPublicClientApplication`pro zařízení se systémem Android, která nemají prohlížeč s podporou vlastní karty, máte možnost rozhodnout voláním metody v aplikaci . Tato metoda `true` vrátí, pokud PackageManager zjistí `false` vlastní karty a pokud nejsou zjištěny v zařízení.

Na základě hodnoty vrácené touto metodou a vašich požadavků můžete učinit rozhodnutí:

- Uživateli můžete vrátit vlastní chybovou zprávu. Například: "Nainstalujte chrome, abyste pokračovali v ověřování" -OR-
- Můžete se vrátit k možnosti vloženého webového zobrazení a spustit uživatelské uživatelské pole jako vložené webové zobrazení.

Níže uvedený kód ukazuje možnost vloženého webového zobrazení:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>Jádro .NET nepodporuje interaktivní ověřování pomocí vestavěného prohlížeče

Pro .NET Core je pořízení tokenů interaktivně k dispozici pouze prostřednictvím webového prohlížeče systému, nikoli s vloženými webovými zobrazeními. Ve skutečnosti .NET Core zatím neposkytuje uI.
Pokud chcete přizpůsobit procházení pomocí webového prohlížeče systému, můžete implementovat rozhraní [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) a dokonce poskytnout svůj vlastní prohlížeč.
