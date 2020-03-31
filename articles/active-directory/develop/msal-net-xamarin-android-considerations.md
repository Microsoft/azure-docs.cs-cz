---
title: Xamarin Android úvahy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Další informace o aspektech používání Xamarin Android s Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132500"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Důležité informace o používání Xamarin Android s MSAL.NET
Tento článek popisuje, co byste měli zvážit při použití Xamarin Android s Microsoft Authentication Library pro .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Nastavení nadřazené aktivity

Na Xamarin Android nastavte nadřazenou aktivitu tak, aby se token vrátil po interakci. Zde je příklad kódu:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

V MSAL 4.2 a novější, můžete také nastavit `PublicClientApplication`tuto funkci na úrovni . Chcete-li tak učinit, použijte zpětné volání:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Pokud používáte [CurrentActivityPlugin](https://github.com/jamesmontemagno/CurrentActivityPlugin) `PublicClientApplication` , pak váš kód tvůrce vypadá jako v následujícím příkladu.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Ujistěte se, že se ovládací prvek vrátí do msal 
Když interaktivní část toku ověřování končí, ujistěte se, že ovládací prvek přejde zpět do MSAL. V systému Android `OnActivityResult` přepište metodu `Activity`. Potom volání `SetAuthenticationContinuationEventArgs` metody `AuthenticationContinuationHelper` MSAL třídy. 

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

Tento řádek zajišťuje, že ovládací prvek vrátí msal na konci interaktivní části toku ověřování.

## <a name="update-the-android-manifest"></a>Aktualizace manifestu Android
Soubor *AndroidManifest.xml* by měl obsahovat následující hodnoty:

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

`android:host=` Nahraďte hodnotu názvem balíčku, který jste zaregistrovali na portálu Azure. Nahraďte hodnotu hodnotou hodnotu hodnotou hodnotu hodnotou hodnotu hodnotou hodnotu hash klíče, který jste zaregistrovali na portálu `android:path=` Azure. Algoritmus hash podpisu by *neměl* být kódován adresou URL. Ujistěte se, že`/`se na začátku hash podpisu zobrazí úvodní lomítko ( ).

Případně [můžete aktivitu vytvořit v kódu,](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) nikoli ručně upravovat *soubor AndroidManifest.xml*. Chcete-li vytvořit aktivitu v kódu, `Activity` nejprve `IntentFilter` vytvořte třídu, která obsahuje atribut a atribut. 

Tady je příklad třídy, která představuje hodnoty souboru XML:

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

### <a name="xamarinforms-43x-manifest"></a>Manifest Xamarin.Forms 4.3.X

Xamarin.Forms 4.3.x generuje kód, `package` který `com.companyname.{appName}` nastaví atribut v *AndroidManifest.xml*. Pokud `DataScheme` použijete `msal{client_id}`jako , můžete změnit hodnotu tak, `MainActivity.cs` aby odpovídala hodnotě oboru názvů.

## <a name="use-the-embedded-web-view-optional"></a>Použití vloženého webového zobrazení (volitelné)

Ve výchozím nastavení používá MSAL.NET systémový webový prohlížeč. Tento prohlížeč umožňuje získat jednotné přihlašování (SSO) pomocí webových aplikací a dalších aplikací. V některých výjimečných případech můžete chtít, aby systém používal vložené webové zobrazení. 

Tento příklad kódu ukazuje, jak nastavit vložené webové zobrazení:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Další informace naleznete [v tématu Použití webových prohlížečů pro MSAL.NET](msal-net-web-browsers.md) a [Xamarin Android aspekty prohlížeče android](msal-net-system-browser-android-considerations.md).


## <a name="troubleshoot"></a>Řešení potíží
Můžete vytvořit novou aplikaci Xamarin.Forms a přidat odkaz na balíček MSAL.NET NuGet.
Ale může mít problémy se sestavením, pokud upgradujete existující aplikaci Xamarin.Forms na MSAL.NET náhled 1.1.2 nebo novější.

Postup řešení problémů se sestavením:

- Aktualizujte existující balíček MSAL.NET NuGet, abyste MSAL.NET náhled 1.1.2 nebo novější.
- Zkontrolujte, zda se Xamarin.Forms automaticky aktualizuje na verzi 2.5.0.122203. V případě potřeby aktualizujte Xamarin.Forms na tuto verzi.
- Zkontrolujte, zda se Xamarin.Android.Support.v4 automaticky aktualizuje na verzi 25.4.0.2. V případě potřeby aktualizujte na verzi 25.4.0.2.
- Ujistěte se, že všechny balíčky Xamarin.Android.Support cílová verze 25.4.0.2.
- Vyčistěte nebo znovu vytvořte aplikaci.
- V sadě Visual Studio zkuste nastavit maximální počet sestavení paralelních projektů na 1. Chcete-li to provést, vyberte **možnosti** > **projekty a řešení** > **sestavení a spuštění** > **maximální počet paralelních projektů sestavení**.
- Pokud vytváříte z příkazového řádku a `/m`váš příkaz používá , zkuste odebrat tento prvek z příkazu.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Chyba: Název AuthenticationContinuationHelper neexistuje v aktuálním kontextu.

Pokud chyba označuje, že `AuthenticationContinuationHelper` neexistuje v aktuálním kontextu, Visual Studio pravděpodobně nesprávně aktualizovali soubor Android.csproj*. Někdy * \<Cesta k*>souboru HintPath nesprávně obsahuje *netstandard13* namísto *monoandroid90*.

Tento příklad obsahuje správnou cestu k souboru:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Další kroky

Další informace naleznete v ukázkové matné [mobilní aplikaci Xamarin, která používá platformu identit microsoftu](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). Následující tabulka shrnuje příslušné informace v souboru README.

| Ukázka | Platforma | Popis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UPW | Jednoduchá aplikace Xamarin.Forms, která ukazuje, jak používat MSAL k ověření osobních účtů Microsoftu a Azure AD prostřednictvím koncového bodu Azure AD 2.0. Aplikace také ukazuje, jak získat přístup k Microsoft Graphu a zobrazuje výsledný token. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |
