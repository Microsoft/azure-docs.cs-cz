---
title: Předpoklady pro Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s informacemi o tom, jak používat Xamarin Android s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: bb5950360734bc46923ef18424e3ad1ce275ad7a
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652677"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Předpoklady pro používání Xamarin Androidu s MSAL.NET
Tento článek popisuje, co byste měli vzít v úvahu při použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Nastavení nadřazené aktivity

V Xamarin Android nastavte nadřazenou aktivitu tak, že se token vrátí po interakci. Zde je příklad kódu:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

V MSAL 4,2 a novějších verzích `PublicClientApplication`můžete tuto funkci nastavit také na úrovni. K tomu použijte zpětné volání:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Pokud používáte [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin), pak váš `PublicClientApplication` Tvůrce kódu vypadá jako v následujícím příkladu.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Ujistěte se, že se ovládací prvek vrátí do MSAL 
Až bude interaktivní část toku ověřování ukončena, ujistěte se, že se ovládací prvek vrátí zpět do MSAL. V Androidu přepište `OnActivityResult` metodu `Activity`. Pak zavolejte `SetAuthenticationContinuationEventArgs` metodu třídy `AuthenticationContinuationHelper` MSAL. 

Tady je příklad:

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```

Tato čára zajistí, že se ovládací prvek vrátí na MSAL na konci interaktivní části toku ověřování.

## <a name="update-the-android-manifest"></a>Aktualizace manifestu pro Android
Soubor *souboru AndroidManifest. XML* by měl obsahovat následující hodnoty:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
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

Pro tuto `android:host=` hodnotu nahraďte název balíčku, který jste zaregistrovali v Azure Portal. Nahraďte `android:path=` hodnotu hash klíče, kterou jste zaregistrovali v Azure Portal pro hodnotu. Hodnota hash *podpisu by neměla být kódovaná* v adrese URL. Zajistěte, aby se na`/`začátku hodnoty hash podpisu zobrazovalo lomítko ().

Alternativně [Vytvořte aktivitu v kódu](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) místo ruční úpravy *souboru AndroidManifest. XML*. Chcete-li vytvořit aktivitu v kódu, nejprve vytvořte třídu, která obsahuje `Activity` atribut a `IntentFilter` atribut. 

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

### <a name="xamarinforms-43x-manifest"></a>Manifest Xamarin. Forms 4.3. X

Xamarin. Forms 4.3. x vygeneruje kód, který `package` nastaví atribut `com.companyname.{appName}` na *souboru AndroidManifest. XML*. Pokud používáte `DataScheme` jako `msal{client_id}`, pak můžete chtít změnit hodnotu tak, aby odpovídala hodnotě `MainActivity.cs` oboru názvů.

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


## <a name="troubleshoot"></a>Řešení potíží
Můžete vytvořit novou aplikaci Xamarin. Forms a přidat odkaz na balíček NuGet MSAL.NET.
Ale můžete mít problémy sestavení, pokud upgradujete existující aplikaci Xamarin. Forms na MSAL.NET Preview 1.1.2 nebo novější.

Řešení potíží s sestavením:

- Aktualizujte existující balíček NuGet MSAL.NET na verzi MSAL.NET verze 1.1.2 nebo novější.
- Ověřte, že se Xamarin. Forms automaticky aktualizovala na verzi 2.5.0.122203. V případě potřeby aktualizujte Xamarin. Forms na tuto verzi.
- Ověřte, že se Xamarin. Android. support. v4 automaticky aktualizuje na verzi 25.4.0.2. V případě potřeby aktualizujte na verzi 25.4.0.2.
- Zajistěte, aby všechny balíčky Xamarin. Android. support byly cílovou verzí 25.4.0.2.
- Vyčistěte nebo znovu sestavte aplikaci.
- V aplikaci Visual Studio zkuste nastavit maximální počet paralelních sestavení projektu na 1. To uděláte tak, že vyberete **Možnosti** > **projekty a řešení** > **sestavíte a spustíte** > **maximální počet paralelně sestavovaných projektů**.
- Pokud sestavíte z příkazového řádku a použijete `/m`příkaz, zkuste tento prvek odebrat z příkazu.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Chyba: název AuthenticationContinuationHelper neexistuje v aktuálním kontextu.

Pokud chyba indikuje, že `AuthenticationContinuationHelper` v aktuálním kontextu neexistuje, může Visual Studio nesprávně aktualizovat soubor Android. csproj *. Někdy * \<cestu>* cesta k souboru nesprávně obsahuje *netstandard13* namísto *monoandroid90*.

Tento příklad obsahuje správnou cestu k souboru:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Další kroky

Další informace najdete v ukázce [mobilní aplikace Xamarin, která používá Microsoft Identity Platform](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Následující tabulka shrnuje relevantní informace v souboru READme.

| Ukázka | Platforma | Popis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Jednoduchá aplikace Xamarin. Forms, která ukazuje, jak používat MSAL k ověřování osobních účtů Microsoft a Azure AD prostřednictvím koncového bodu Azure AD 2,0. Aplikace také ukazuje, jak získat přístup k Microsoft Graph a zobrazuje výsledný token. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |
