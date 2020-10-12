---
title: Používání webových prohlížečů (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních doporučeních pro použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: bf51f39a789b91a4cb0b88eb8bb1f2989bec7358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165817"
---
# <a name="using-web-browsers-msalnet"></a>Používání webových prohlížečů (MSAL.NET)

Pro interaktivní ověřování se vyžadují webové prohlížeče. Ve výchozím nastavení podporuje MSAL.NET [webový prohlížeč systému](#system-web-browser-on-xamarinios-xamarinandroid) na Xamarin. iOS a Xamarin. Android. [Můžete ale také povolit vložený webový prohlížeč](#enable-embedded-webviews-on-ios-and-android) v závislosti na vašich požadavcích (uživatelské rozhraní, nutnosti jednotného přihlašování (SSO), zabezpečení) v aplikacích [Xamarin. iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) a [Xamarin. Android](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) . A můžete dokonce [zvolit](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) , který webový prohlížeč se má použít, na základě přítomnosti Chrome nebo prohlížeče, který podporuje vlastní karty Chrome v Androidu. MSAL.NET podporuje pouze prohlížeč systému v aplikacích klasické pracovní plochy .NET Core.

## <a name="web-browsers-in-msalnet"></a>Webové prohlížeče v MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interakce se stane ve webovém prohlížeči.

Je důležité si uvědomit, že při interaktivním získání tokenu se obsah dialogového okna neposkytuje v knihovně, ale službou STS (služba tokenů zabezpečení). Koncový bod ověřování pošle zpátky kód HTML a JavaScript, který řídí interakci, která je vykreslena ve webovém prohlížeči nebo ovládacím prvku Web. Povolení služby STS pro zpracování interakce HTML má mnoho výhod:

- Heslo (pokud bylo typu) nikdy neukládá aplikace ani knihovna ověřování.
- Povoluje přesměrování na jiné zprostředkovatele identity (Pokud se přihlásíte k přihlašování pomocí pracovního školního účtu nebo osobního účtu s MSAL nebo s účtem sociální sítě s Azure AD B2C).
- Umožňuje řídit podmíněný přístup řízení přístupu (STS), například tím, že uživatel provede [vícefaktorové ověřování (MFA)](../authentication/concept-mfa-howitworks.md) během fáze ověřování (zadání kódu PIN Windows Hello nebo jeho volání na telefonu nebo na ověřovací aplikaci na telefonu). V případech, kdy požadované vícefaktorové ověřování ještě není nastavené, si ho uživatel může v jednom dialogu nastavit přesně včas.  Uživatel zadá své mobilní telefonní číslo a provede instalaci ověřovací aplikace a naskenováním značky QR k přidání svého účtu. Tato interakce řízená serverem je skvělým prostředím.
- Umožňuje uživateli změnit heslo v tomto dialogovém okně v případě vypršení platnosti hesla (zadáním dalších polí pro původní heslo a nové heslo).
- Umožňuje branding klienta nebo aplikaci (Image) řízenou správcem tenanta nebo vlastníkem aplikace služby Azure AD.
- Umožňuje uživatelům udělit souhlas s tím, že aplikace přistupuje k prostředkům nebo oborům v jejich názvu hned po ověření.

### <a name="embedded-vs-system-web-ui"></a>Webové uživatelské rozhraní Embedded vs System

MSAL.NET je knihovna s více architekturami a má kód specifický pro rozhraní, který je hostitelem prohlížeče v ovládacím prvku uživatelského rozhraní (například v klasickém rozhraní .NET používá WinForms, v Xamarin používá nativní mobilní ovládací prvky atd.). Tento ovládací prvek se nazývá `embedded` webové uživatelské rozhraní. Alternativně je MSAL.NET také možné aktivovat prohlížeč operačního systému.

Obecně se doporučuje použít výchozí platformu a obvykle se jedná o systémový prohlížeč. Prohlížeč systému je lepší při zapamatování uživatelů, kteří se předtím přihlásili. Pokud potřebujete toto chování změnit, použijte `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>Na první pohled

| Rozhraní .NET Framework        | Vložené | Systém | Výchozí |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Yes | Ano ^ | Vložené |
| .NET Core     | No | Ano ^ | Systém |
| .NET Standard | No | Ano ^ | Systém |
| UWP | Yes | No | Vložené |
| Xamarin.Android | Yes | Yes  | Systém |
| Xamarin.iOS | Yes | Yes  | Systém |
| Xamarin.Mac| Yes | No | Vložené |

^ Vyžaduje " http://localhost " identifikátor URI přesměrování

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systémový webový prohlížeč v Xamarin. iOS, Xamarin. Android

Ve výchozím nastavení podporuje MSAL.NET webový prohlížeč systému na Xamarin. iOS, Xamarin. Android a .NET Core. Pro všechny platformy, které poskytují uživatelské rozhraní (to znamená ne .NET Core), je dialogové okno k dispozici v knihovně vložení ovládacího prvku webového prohlížeče. MSAL.NET také používá vložené webové zobrazení pro Desktop a WAB rozhraní .NET pro platformu UWP. Využívá je ale ve výchozím nastavení **webový prohlížeč systému** pro aplikace Xamarin iOS a Xamarin Android. V systému iOS si dokonce vyberou webové zobrazení, které se má použít, v závislosti na verzi operačního systému (iOS12, iOS11 a starší).

Použití prohlížeče systému má významnou výhodu sdílení stavu jednotného přihlašování s jinými aplikacemi a webovými aplikacemi bez nutnosti zprostředkovatele (portál společnosti/ověřovatel). Ve výchozím nastavení se v systému MSAL.NET pro platformy Xamarin iOS a Xamarin Android používal webový prohlížeč, protože na těchto platformách zabírá systém webový prohlížeč celou obrazovku a činnost koncového uživatele je lepší. Webové zobrazení systému nelze odlišit od dialogu. V systému iOS může uživatel například udělit souhlas, že by prohlížeč mohl volat aplikaci zpátky, což může být nepříjemné.

## <a name="system-browser-experience-on-net-core"></a>Prostředí prohlížeče systému v .NET Core

V .NET Core MSAL.NET spustí prohlížeč systému jako samostatný proces. MSAL.NET nemá kontrolu nad tímto prohlížečem, ale jakmile uživatel dokončí ověřování, Webová stránka se přesměruje takovým způsobem, že MSAL.NET může zachytit identifikátor URI.

Můžete taky nakonfigurovat aplikace napsané pro .NET Classic tak, aby používaly tento prohlížeč, a to zadáním

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET nemůže zjistit, jestli uživatel přejde pryč nebo jednoduše zavře prohlížeč. Pro aplikace, které používají tuto techniku, doporučujeme definovat časový limit (prostřednictvím `CancellationToken` ). Doporučujeme časový limit aspoň pár minut, aby se zohlednily případy, kdy se uživateli zobrazí výzva ke změně hesla nebo provedení vícefaktorového ověřování.

### <a name="how-to-use-the-default-os-browser"></a>Použití výchozího prohlížeče operačního systému

MSAL.NET musí naslouchat `http://localhost:port` a zachytit kód, který AAD posílá, když se uživatel dokončí ověřováním (podrobnosti najdete v [autorizačním kódu](v2-oauth2-auth-code-flow.md) ).

Postup povolení prohlížeče systému:

1. Při registraci aplikace nakonfigurujte `http://localhost` jako identifikátor URI přesměrování (aktuálně se nepodporuje v B2C).
2. Při vytváření PublicClientApplication zadejte tento identifikátor URI přesměrování:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Pokud nakonfigurujete `http://localhost` , interně MSAL.NET najde náhodný otevřený port a použije ho.

### <a name="linux-and-mac"></a>Linux a MAC

V systému Linux se v MSAL.NET otevře výchozí prohlížeč operačního systému pomocí nástroje xdg-Open. Chcete-li vyřešit potíže, spusťte nástroj z terminálu, například `xdg-open "https://www.bing.com"` . V systému Mac je prohlížeč otevřený vyvoláním `open <url>` .

### <a name="customizing-the-experience"></a>Přizpůsobení prostředí

> [!NOTE]
> Přizpůsobení je k dispozici v MSAL.NET 4.1.0 nebo novějším.

MSAL.NET je schopný reagovat zprávou HTTP při přijetí tokenu nebo v případě chyby. Můžete zobrazit zprávu HTML nebo přesměrovat na adresu URL dle vašeho výběru:

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

### <a name="opening-a-specific-browser-experimental"></a>Otevření konkrétního prohlížeče (experimentální)

Je možné přizpůsobit způsob, jakým MSAL.NET otevírá prohlížeč. Například namísto použití libovolného prohlížeče je výchozí, můžete vynutit otevření konkrétního prohlížeče:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP nepoužívá systémové WebView.

U aplikací klasické pracovní plochy ale spuštění webového WebView vede k subpar uživatelskému prostředí, protože uživatel uvidí prohlížeč, kde už můžou mít otevřené jiné karty. A když k ověřování dojde, uživatelé obdrží stránku s žádostí, aby zavřeli toto okno. Pokud uživatel nevěnuje pozornost pozornost, může celý proces zavřít (včetně dalších karet, které se nevztahují k ověřování). Využití webového prohlížeče na ploše by vyžadovalo také otevření místních portů a jejich naslouchání, což by mohlo vyžadovat Pokročilá oprávnění pro aplikaci. Tento požadavek může být zdráhají jako vývojář, uživatel nebo správce.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Povolit vložená webzobrazení v iOS a Androidu

Můžete také povolit vložená webzobrazení v aplikacích Xamarin. iOS a Xamarin. Android. Od verze MSAL.NET 2.0.0-Preview MSAL.NET podporuje také použití **vloženého** možnosti WebView. Pro ADAL.NET je jedinou podporovanou možností vložené WebView.

Jako vývojář, který používá MSAL.NET cílené na Xamarin, se můžete rozhodnout použít buď vložená webzobrazení, nebo systémové prohlížeče. Podle vašeho výběru záleží na uživatelském prostředí a na zabezpečení, které chcete cílit.

V současné době MSAL.NET ještě nepodporuje zprostředkovatele pro Android a iOS. Pokud tedy potřebujete zadat jednotné přihlašování (SSO), je možné, že je prohlížeč systému stále lepší volbou. Podpora zprostředkovatelů s integrovaným webovým prohlížečem je na MSAL.NET nevyřízených položek.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Rozdíly mezi vloženým webovým zobrazením a systémovým prohlížečem
Mezi vloženým webovým zobrazením a systémovým prohlížečem v MSAL.NET jsou rozdíly v různých vizuálních rozdílech.

**Interaktivní přihlašování pomocí MSAL.NET pomocí vloženého webviewu:**

![vložený](media/msal-net-web-browsers/embedded-webview.png)

**Interaktivní přihlašování pomocí MSAL.NET pomocí systémového prohlížeče:**

![Prohlížeč systému](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Možnosti pro vývojáře

Jako vývojář používající MSAL.NET máte k dispozici několik možností zobrazení interaktivního dialogového okna ze služby STS:

- **Prohlížeč systému.** Prohlížeč systému je ve výchozím nastavení nastaven v knihovně. Při použití Androidu si přečtěte [systémové prohlížeče](msal-net-system-browser-android-considerations.md) , kde najdete konkrétní informace o podporovaných prohlížečích pro ověřování. Při používání prohlížeče systému v Androidu doporučujeme, aby zařízení mělo prohlížeč, který podporuje vlastní karty Chrome.  V opačném případě může ověřování selhat.
- **Vložené WebView.** Chcete-li použít pouze vložené WebView v MSAL.NET, `AcquireTokenInteractively` Tvůrce parametrů obsahuje `WithUseEmbeddedWebView()` metodu.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Výběr mezi integrovaným webovým prohlížečem nebo systémovým prohlížečem v Xamarin. iOS

V aplikaci pro iOS můžete v nástroji `AppDelegate.cs` inicializovat na `ParentWindow` `null` . Nepoužívá se v iOS.

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Výběr mezi integrovaným webovým prohlížečem nebo systémovým prohlížečem v Xamarin. Android

V aplikaci pro Android můžete v aplikaci `MainActivity.cs` nastavit nadřazenou aktivitu tak, aby se na ni vrátil výsledek ověřování:

```csharp
 App.ParentWindow = this;
```

Pak v `MainPage.xaml.cs` :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Zjištění přítomnosti vlastních karet na Xamarin. Android

Pokud chcete použít webový prohlížeč systému k povolení jednotného přihlašování s aplikacemi spuštěnými v prohlížeči, ale máte obavy o činnost koncového uživatele pro zařízení s Androidem, která nemají prohlížeč s podporou vlastní karty, máte možnost se rozhodnout, že zavoláte `IsSystemWebViewAvailable()` metodu v `IPublicClientApplication` . Tato metoda vrátí, `true` Pokud PackageManager detekuje vlastní karty, a `false` Pokud nejsou v zařízení zjištěny.

Na základě hodnoty vracené touto metodou a vašich požadavků můžete učinit rozhodnutí:

- Uživateli můžete vrátit vlastní chybovou zprávu. Například: "nainstalujte si Chrome, abyste mohli pokračovat v ověřování"-nebo-
- Můžete se vrátit k vložené možnosti WebView a spustit uživatelské rozhraní jako vložené WebView.

Následující kód ukazuje možnost vloženého WebView:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core nepodporuje interaktivní ověřování pomocí vloženého prohlížeče.

Pro .NET Core je získání tokenů interaktivně dostupné jenom přes webový prohlížeč systému, nikoli s vloženými webovými zobrazeními. Rozhraní .NET Core je vlastně v uživatelském rozhraní ještě neposkytuje.
Pokud chcete přizpůsobit možnosti procházení webovým prohlížečem systému, můžete implementovat rozhraní [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) a dokonce zadat vlastní prohlížeč.
