---
title: Webové prohlížeče v Microsoft Authentication Library pro .NET | Azure
description: Další informace o konkrétní aspekty při používání Xamarin Android s Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 061b8a9f16396841c3f0d650ccc2f2c4a907aab3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111310"
---
# <a name="using-web-browsers-in-msalnet"></a>Pomocí webových prohlížečů v MSAL.NET
Webové prohlížeče jsou požadovány pro interaktivní ověřování. Ve výchozím nastavení podporuje MSAL.NET [systému webový prohlížeč](#system-web-browser-on-xamarinios-and-xamarinandroid) na Xamarin.iOS a [Xamarin.Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/system-browser). Ale [můžete povolit také integrovaného webového prohlížeče](#enable-embedded-webviews) v závislosti na vašich požadavcích (uživatelského prostředí, třeba pro jednotné přihlašování (SSO), zabezpečení) v [Xamarin.iOS](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) a [Xamarin.Android](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) aplikace. A můžete dokonce [zvolte dynamicky](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) používat webový prohlížeč na základě přítomnosti Chrome nebo prohlížeč podporuje vlastní karty Chrome v Androidu.

## <a name="web-browsers-in-msalnet"></a>Webové prohlížeče v MSAL.NET

Je důležité pochopit, že při získávání tokenu interaktivně, obsah dialogového okna není k dispozici v knihovně, ale službou tokenů zabezpečení (služby tokenů zabezpečení). Koncový bod ověřování odešle zpět některé HTML a JavaScriptu, která řídí interakce, které se vykreslí v webový prohlížeč nebo webový ovládací prvek. Povolení služby STS pro zpracování interakce HTML má mnoho výhod:

- Heslo (Pokud byl zadán jeden) nikdy neukládají aplikace ani knihovny pro ověřování.
- Umožňuje přesměrování s ostatními poskytovateli identity (například přihlášení ve službě pracovní nebo školní účet nebo osobní účet MSAL nebo s účtu na sociální síti s Azure AD B2C).
- Umožňuje služba tokenů zabezpečení řízení podmíněného přístupu, například tím, že se uživatel více vícefaktorového ověřování (MFA) během fáze ověřování (zadávání pin Windows Hello nebo volání na svůj telefon nebo na ověřování aplikace na telefonu). V případech, kdy se vyžaduje vícefaktorové ověřování není nastavený ji ještě uživatel může nastavit tak za běhu v dialogovém okně stejné.  Uživatel zadá své mobilní telefonní číslo a v průvodci k instalaci aplikace ověřování a kontroly značku QR, kterou chcete přidat svůj účet. Tento server řízené interakce je skvělé prostředí!
- Umožňuje uživateli změnit své heslo v tomto dialogovém okně stejné, pokud vypršela platnost hesla (poskytuje další pole pro staré heslo a potvrzení nového hesla).
- Umožňuje branding tenanta nebo aplikace (imagí) řídí správce tenanta Azure AD / vlastník aplikace.
- Umožňuje uživatelům udělit souhlas s umožní aplikaci přistupovat k prostředkům / obory v názvu bezprostředně po ověření.

## <a name="system-web-browser-on-xamarinios-and-xamarinandroid"></a>Systém webový prohlížeč v Xamarin.iOS a Xamarin.Android

Ve výchozím nastavení podporuje MSAL.NET systému webový prohlížeč na Xamarin.iOS a Xamarin.Android. Pro všechny platformy, které poskytují uživatelské rozhraní (to znamená ne .NET Core) je dialogové okno poskytovaných knihovnou vkládání ovládací prvek webového prohlížeče. MSAL.NET také používá vložený webové zobrazení pro .NET Desktop a WAB pro platformu UPW. Však využívá ve výchozím nastavení **systému webový prohlížeč** pro Xamarin iOS a aplikace Xamarin Android. V systémech iOS, dokonce i zvolí ve webovém zobrazení používat v závislosti na verzi operačního systému (iOS12 iOS11 a starší).

Pomocí prohlížeče systém má významné výhody jednotného přihlašování stavu sdílení s jinými aplikacemi a s webovými aplikacemi bez nutnosti zprostředkovatele (portál společnosti a Authenticator). Prohlížeč systému byl použit, ve výchozím nastavení v MSAL.NET pro Xamarin iOS a platformy Xamarin Android vzhledem k tomu, na těchto platformách webový prohlížeč systému zabírá celou obrazovku a je lepší uživatelské prostředí. Webové zobrazení systému není odlišitelné od dialogové okno. V systémech iOS ale uživatel pravděpodobně k udělení souhlasu doplňku pro prohlížeč pro aplikace, které vám můžou jít zpětné volání.

### <a name="uwp-does-not-use-the-system-webview"></a>UPW nepoužívá System Webview

Pro desktopové aplikace ale spouštění System Webview vede k subpar uživatelské prostředí, jak se uživateli zobrazí prohlížeč, ve kterém může už mají ostatní karty otevřít. A při ověřování se stalo, uživatelé obdrží stránky s výzvou, aby toto okno zavřít. Pokud uživatel není věnovat pozornost, že můžete zavřít celý proces (včetně ostatní karty, které spolu nesouvisí s ověřováním). Využití prohlížeč systému na ploše by také vyžadují místní porty a naslouchá, může to vyžadovat rozšířená oprávnění pro aplikaci. Jako vývojář, uživatel nebo správce, je možné, nebudete o tomto požadavku.

## <a name="enable-embedded-webviews"></a>Povolit vložená zobrazení Webview 
Můžete také povolit vložená zobrazení Webview v aplikacích pro Xamarin.iOS a Xamarin.Android. Počínaje MSAL.NET 2.0.0-preview, MSAL.NET také podporuje používání **vložený** možnost webview. Vložený webview pro ADAL.NET, je jediná možnost nepodporuje.

Jako vývojář pomocí MSAL.NET cílení na Xamarinu můžete použít buď vložená zobrazení Webview nebo systém prohlížeče. To závisí na vašem výběru, v závislosti na obavy uživatelů prostředí a zabezpečení, kterou chcete cílit.

V současné době MSAL.NET zatím nepodporuje zprostředkovatele Android a iOS. Proto pokud je potřeba poskytovat jednotné přihlašování (SSO), prohlížeč systému může být vhodnější. Podpora zprostředkovatelů pomocí integrovaného webového prohlížeče je v nevyřízených položkách MSAL.NET.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Rozdíly mezi vložený webview a prohlížeč systému 
Existují některé visual rozdíly mezi vložený webview a prohlížeč systému v MSAL.NET.

**Interaktivní přihlašování pomocí MSAL.NET pomocí vložených Webview:**

![Vložený](media/msal-net-web-browsers/embedded-webview.png)

**Interaktivní přihlašování pomocí MSAL.NET pomocí prohlížeče systému:**

![Prohlížeč systému](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Možnosti pro vývojáře

Jako vývojář pomocí MSAL.NET máte několik možností pro zobrazení dialogu interaktivní ze služby STS:

- **Prohlížeč systému.** Prohlížeč systému je ve výchozím nastavení v knihovně. Pokud používáte Android, přečtěte si [systému prohlížeče](msal-net-system-browser-android-considerations.md) konkrétní informace o tom, které jsou podporované prohlížeče pro ověřování. Pokud používáte prohlížeč systému v Androidu, doporučujeme, abyste že zařízení má prohlížeč, který podporuje vlastní karty Chrome.  V opačném případě může selhat ověřování.
- **Vložený webview.** Používat pouze webview součástí MSAL.NET, `AcquireTokenInteractively` obsahuje parametry Tvůrce `WithUseEmbeddedWebView()` metoda.

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

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Volba mezi vložený webovém prohlížeči nebo prohlížeč systému na Xamarin.iOS

V aplikaci pro iOS v `AppDelegate.cs` lze inicializovat `ParentWindow` k `null`. Není použit v Iosu

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Volba mezi vložený webovém prohlížeči nebo prohlížeč systému v Xamarin.Android

V aplikaci pro Android v `MainActivity.cs` Nadřazená aktivita, můžete nastavit tak, aby výsledky ověřování získá zpět do ní:

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

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Zjištění přítomnosti vlastních karet na Xamarin.Android

Pokud chcete použít webový prohlížeč systému k povolení přihlášení SSO s aplikacemi spuštění v prohlížeči, ale jsou obavy týkající se uživatelského prostředí pro zařízení s Androidem bez nutnosti prohlížeč s podporou vlastní karty, máte možnost se rozhodnout, voláním `IsSystemWebViewAvailable()` metoda v < c 2 > `IPublicClientApplication` . Tato metoda vrátí `true` pokud zjistí, PackageManager vlastní karty a `false` Pokud nejsou zjištěny na zařízení.

Podle hodnoty, vrátí tato metoda a vaše požadavky, můžete provést rozhodnutí:

- Vlastní chybová zpráva může vrátit uživateli. Příklad: "Nainstalujte prosím Chrome, abyste mohli pokračovat s ověřováním" - OR-
- Lze vrátit zpět k možnost vložený webview a uživatelské rozhraní služby jako vložený webview.

Následující kód ukazuje embedded webview možnost:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

## <a name="net-core-does-not-support-interactive-authentication-out-of-the-box"></a>.NET core nepodporuje interaktivní ověřování mimo pole

Pro .NET Core získávání tokenů interaktivně není k dispozici. Ve skutečnosti .NET Core neposkytuje uživatelského rozhraní ještě. Pokud chcete poskytnout interaktivní přihlašování pro aplikace .NET Core, můžete umožnit aplikaci k dispozici pro uživatele, kód a adresu URL budou přihlašovat interaktivně (viz [toku kódu zařízení](msal-authentication-flows.md#device-code)).

Případně můžete implementovat [IWithCustomUI](scenario-desktop-acquire-token.md#withcustomwebui) rozhraní a zadat vlastní prohlížeč