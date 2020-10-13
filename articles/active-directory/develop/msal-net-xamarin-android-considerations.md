---
title: Konfigurace a řešení potíží s kódem Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s informacemi o tom, jak používat Xamarin Android s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 320d48535c4792a4d610888c6a7030568ccf16bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89459840"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Požadavky na konfiguraci a tipy pro řešení potíží pro Xamarin Android s MSAL.NET

Při použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET) je potřeba provést několik změn konfigurace, které máte v kódu dělat. V následujících částech jsou popsány požadované úpravy, za kterými následuje část [věnované odstraňování potíží](#troubleshooting) , které vám pomůžou vyhnout se některým z nejběžnějších problémů.

## <a name="set-the-parent-activity"></a>Nastavení nadřazené aktivity

V Xamarin Androidu nastavte nadřazenou aktivitu tak, aby se token vrátil po interakci:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

V MSAL.NET 4,2 a novějších verzích můžete tuto funkci nastavit také na úrovni [PublicClientApplication][PublicClientApplication]. K tomu použijte zpětné volání:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Pokud používáte [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), váš [`PublicClientApplication`][PublicClientApplication] kód tvůrce by měl vypadat podobně jako tento fragment kódu:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Ujistěte se, že se ovládací prvek vrátí do MSAL

Po ukončení interaktivní části toku ověřování vraťte řízení k MSAL přepsáním [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] Metoda.

V přepsání zavolejte MSAL. NETTO `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` Metoda, která vrátí řízení na MSAL na konci interaktivní části toku ověřování.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Aktualizace manifestu pro Android

Soubor *AndroidManifest.xml* by měl obsahovat následující hodnoty:

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
  <activity
        android:name="microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
  </activity>
```

Pro tuto hodnotu nahraďte název balíčku, který jste zaregistrovali v Azure Portal `android:host=` . Nahraďte hodnotu hash klíče, kterou jste zaregistrovali v Azure Portal pro `android:path=` hodnotu. Hodnota hash *podpisu by neměla být kódovaná* v adrese URL. Zajistěte, aby `/` se na začátku hodnoty hash podpisu zobrazovalo lomítko ().

Případně můžete místo ruční úpravy *AndroidManifest.xml* [vytvořit aktivitu v kódu](/xamarin/android/platform/android-manifest#the-basics) . Chcete-li vytvořit aktivitu v kódu, nejprve vytvořte třídu, která obsahuje `Activity` atribut a `IntentFilter` atribut.

Zde je příklad třídy, která představuje hodnoty souboru XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Manifest Xamarin. Forms 4.3. x

Xamarin. Forms 4.3. x vygeneruje kód, který nastaví `package` atribut na `com.companyname.{appName}` v *AndroidManifest.xml*. Pokud používáte `DataScheme` jako `msal{client_id}` , pak můžete chtít změnit hodnotu tak, aby odpovídala hodnotě `MainActivity.cs` oboru názvů.

## <a name="use-the-embedded-web-view-optional"></a>Použít vložené webové zobrazení (volitelné)

Ve výchozím nastavení používá MSAL.NET webový prohlížeč systému. Tento prohlížeč vám umožní získat jednotné přihlašování (SSO) pomocí webových aplikací a dalších aplikací. V některých vzácných případech může být vhodné, aby systém používal vložené webové zobrazení.

Tento příklad kódu ukazuje, jak nastavit vložené webové zobrazení:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Další informace najdete v tématu věnovaném [použití webových prohlížečů pro MSAL.NET](msal-net-web-browsers.md) a [Xamarin Android System Browser](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="general-tips"></a>Obecné tipy

- Aktualizujte existující balíček NuGet MSAL.NET na [nejnovější verzi nástroje MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Ověřte, že je Xamarin. Forms na nejnovější verzi.
- Ověřte, jestli je Xamarin. Android. support. v4 v nejnovější verzi.
- Ujistěte se, že všechny balíčky Xamarin. Android. support cílí na nejnovější verzi.
- Vyčistěte nebo znovu sestavte aplikaci.
- V aplikaci Visual Studio zkuste nastavit maximální počet paralelních sestavení projektu na **1**. To uděláte tak, že vyberete **Možnosti**  >  **projekty a řešení**  >  **sestavíte a spustíte**  >  **maximální počet paralelně sestavovaných projektů**.
- Pokud sestavíte z příkazového řádku a použijete příkaz `/m` , zkuste tento prvek odebrat z příkazu.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Chyba: název AuthenticationContinuationHelper neexistuje v aktuálním kontextu.

Pokud chyba indikuje, že `AuthenticationContinuationHelper` v aktuálním kontextu neexistuje, může Visual Studio nesprávně aktualizovat soubor *Android. csproj \* * . V některých případech je cesta k souboru v `<HintPath>` elementu nesprávně obsažena `netstandard13` místo `monoandroid90` .

Tento příklad obsahuje správnou cestu k souboru:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v ukázce [mobilní aplikace Xamarin, která používá Microsoft Identity Platform](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Následující tabulka shrnuje relevantní informace v souboru READme.

| Ukázka | Platforma | Description |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Jednoduchá aplikace Xamarin. Forms, která ukazuje, jak používat MSAL k ověřování osobních účtů Microsoft a Azure AD prostřednictvím koncového bodu Azure AD 2,0. Aplikace také ukazuje, jak získat přístup k Microsoft Graph a zobrazuje výsledný token. <br>![Diagram toku ověřování](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
