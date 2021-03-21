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
ms.openlocfilehash: 11642480ac817b50d102e396b8ab5e200948a615
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199568"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Požadavky na konfiguraci a tipy pro řešení potíží pro Xamarin Android s MSAL.NET

Při použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET) existuje několik změn konfigurace, které je třeba provést ve svém kódu. V následujících částech jsou popsány požadované úpravy, za kterými následuje část [věnované odstraňování potíží](#troubleshooting) , které vám pomůžou vyhnout se některým z nejběžnějších problémů.

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

## <a name="update-the-android-manifest-for-system-webview-support"></a>Aktualizace manifestu Androidu pro podporu systému WebView 

Aby bylo možné podporovat systémové rozhraní WebView, *AndroidManifest.xml* soubor by měl obsahovat následující hodnoty:

```xml
<activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="msal{Client Id}" android:host="auth" />
  </intent-filter>
</activity>
```

`android:scheme`Hodnota se vytvoří z identifikátoru URI přesměrování, který je nakonfigurovaný na portálu aplikací. Například pokud je identifikátor URI `msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842://auth` pro přesměrování, `android:scheme` položka v manifestu by vypadala jako v tomto příkladu:

```xml
<data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
```

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

### <a name="use-system-webview-in-brokered-authentication"></a>Použití systémového WebView v zprostředkovaných ověřováních

Pokud chcete použít systémové WebView jako zálohu pro interaktivní ověřování, když jste aplikaci nakonfigurovali tak, aby používala zprostředkované ověřování a zařízení nemá nainstalovaného zprostředkovatele, povolte MSAL k zachycení odpovědi ověřování pomocí identifikátoru URI přesměrování zprostředkovatele. MSAL se pokusí ověřit pomocí výchozího systémového webzobrazení v zařízení, když zjistí, že zprostředkovatel není k dispozici. Použijete-li tuto výchozí hodnotu, nezdaří se, protože identifikátor URI přesměrování je nakonfigurován pro použití zprostředkovatele a systémové ovládacímu zobrazení není známo, jak jej použít pro návrat do MSAL. Chcete-li tento problém vyřešit, vytvořte _Filtr záměrů_ pomocí identifikátoru URI přesměrování zprostředkovatele, který jste nakonfigurovali dříve. Přidejte filtr záměr úpravou manifestu aplikace jako v tomto příkladu:

```xml
<!--Intent filter to capture System WebView or Authenticator calling back to our app after sign-in-->
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

### <a name="xamarinforms-43x-manifest"></a>Manifest Xamarin. Forms 4.3. x

Xamarin. Forms 4.3. x vygeneruje kód, který nastaví `package` atribut na `com.companyname.{appName}` v *AndroidManifest.xml*. Pokud používáte `DataScheme` jako `msal{client_id}` , pak můžete chtít změnit hodnotu tak, aby odpovídala hodnotě `MainActivity.cs` oboru názvů.

## <a name="android-11-support"></a>Podpora pro Android 11

Chcete-li používat systémový prohlížeč a zprostředkované ověřování v Androidu 11, je nutné nejprve deklarovat tyto balíčky, aby byly viditelné pro aplikaci. Aplikace, které cílí na Android 10 (rozhraní API 29) a starší, se můžou dotazovat na operační systém a získat seznam balíčků, které jsou v zařízení dostupné v daném okamžiku. Pro podporu ochrany osobních údajů a zabezpečení systém Android 11 omezuje viditelnost balíčku na výchozí seznam balíčků OS a balíčků, které jsou uvedené v souboru *AndroidManifest.xml* aplikace. 

Pokud chcete aplikaci povolit ověřování pomocí prohlížeče systému i zprostředkovatele, přidejte do *AndroidManifest.xml* následující část:

```xml
<!-- Required for API Level 30 to make sure the app can detect browsers and other apps where communication is needed.-->
<!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
<queries>
  <package android:name="com.azure.authenticator" />
  <package android:name="{Package Name}" />
  <package android:name="com.microsoft.windowsintune.companyportal" />
  <!-- Required for API Level 30 to make sure the app detect browsers
      (that don't support custom tabs) -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.BROWSABLE" />
    <data android:scheme="https" />
  </intent>
  <!-- Required for API Level 30 to make sure the app can detect browsers that support custom tabs -->
  <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
  <intent>
    <action android:name="android.support.customtabs.action.CustomTabsService" />
  </intent>
</queries>
``` 

Nahraďte `{Package Name}` názvem balíčku aplikace. 

Aktualizovaný manifest, který teď obsahuje podporu pro systémový prohlížeč a zprostředkované ověřování, by měl vypadat podobně jako v tomto příkladu:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android" android:versionCode="1" android:versionName="1.0" package="com.companyname.XamarinDev">
    <uses-sdk android:minSdkVersion="21" android:targetSdkVersion="30" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <application android:theme="@android:style/Theme.NoTitleBar">
        <activity android:name="microsoft.identity.client.BrowserTabActivity" android:configChanges="orientation|screenSize">
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msal4a1aa1d5-c567-49d0-ad0b-cd957a47f842" android:host="auth" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />
                <data android:scheme="msauth" android:host="com.companyname.XamarinDev" android:path="/Fc4l/5I4mMvLnF+l+XopDuQ2gEM=" />
            </intent-filter>
        </activity>
    </application>
    <!-- Required for API Level 30 to make sure we can detect browsers and other apps we want to
     be able to talk to.-->
    <!--https://developer.android.com/training/basics/intents/package-visibility-use-cases-->
    <queries>
        <package android:name="com.azure.authenticator" />
        <package android:name="com.companyname.xamarindev" />
        <package android:name="com.microsoft.windowsintune.companyportal" />
        <!-- Required for API Level 30 to make sure we can detect browsers
        (that don't support custom tabs) -->
        <intent>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="https" />
        </intent>
        <!-- Required for API Level 30 to make sure we can detect browsers that support custom tabs -->
        <!-- https://developers.google.com/web/updates/2020/07/custom-tabs-android-11#detecting_browsers_that_support_custom_tabs -->
        <intent>
            <action android:name="android.support.customtabs.action.CustomTabsService" />
        </intent>
    </queries>
</manifest>
```

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

## <a name="troubleshooting"></a>Poradce při potížích

### <a name="general-tips"></a>Obecné tipy

- Aktualizujte existující balíček NuGet MSAL.NET na [nejnovější verzi nástroje MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Ověřte, že je Xamarin. Forms na nejnovější verzi.
- Ověřte, jestli je Xamarin. Android. support. v4 v nejnovější verzi.
- Ujistěte se, že všechny balíčky Xamarin. Android. support cílí na nejnovější verzi.
- Vyčistěte nebo znovu sestavte aplikaci.
- V aplikaci Visual Studio zkuste nastavit maximální počet paralelních sestavení projektu na **1**. To uděláte tak, že vyberete **Možnosti**  >  **projekty a řešení**  >  **sestavíte a spustíte**  >  **maximální počet paralelně sestavovaných projektů**.
- Pokud sestavíte z příkazového řádku a použijete příkaz `/m` , zkuste tento prvek odebrat z příkazu.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Chyba: název AuthenticationContinuationHelper neexistuje v aktuálním kontextu.

Pokud chyba indikuje, že `AuthenticationContinuationHelper` v aktuálním kontextu neexistuje, může Visual Studio nesprávně aktualizovat soubor *Android. csproj \** . V některých případech je cesta k souboru v `<HintPath>` elementu nesprávně obsažena `netstandard13` místo `monoandroid90` .

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
