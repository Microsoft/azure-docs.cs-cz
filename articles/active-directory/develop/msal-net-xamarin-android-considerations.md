---
title: Předpoklady pro Xamarin Android (Microsoft Authentication Library pro .NET) | Azure
description: Přečtěte si o konkrétních doporučeních pro použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff55853c859008690548b161451a24941a597d3a
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277900"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Doporučení pro Xamarin Android týkající se MSAL.NET
Tento článek popisuje konkrétní informace týkající se použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Nastavení nadřazené aktivity

V Xamarin. Android je nutné nastavit nadřazenou aktivitu tak, aby se token znovu vrátil, jakmile k interakci dojde.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```
Můžete ji také nastavit na úrovni PublicClientApplication (v MSAL 4.2 +) prostřednictvím zpětného volání.

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Doporučením je [použít CurrentActivityPlugin.](https://github.com/jamesmontemagno/CurrentActivityPlugin)  Váš kód tvůrce PublicClientApplication by pak vypadal takto:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```


## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Zajistěte, aby se řízení vrátilo zpátky na MSAL, jakmile skončí interaktivní část toku ověřování.
V systému Android je nutné přepsat `OnActivityResult` metodu `Activity` a voláním metody SetAuthenticationContinuationEventArgs třídy MSAL AuthenticationContinuationHelper.

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
Tento řádek zajistí, že se ovládací prvek vrátí zpět na MSAL, jakmile se dokončí interaktivní část toku ověřování.

## <a name="update-the-android-manifest"></a>Aktualizace manifestu pro Android
`AndroidManifest.xml` Měl by obsahovat následující hodnoty:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Použít vložené webové zobrazení (volitelné)

Ve výchozím nastavení MSAL.NET používá webový prohlížeč systému, který umožňuje získat jednotné přihlašování s webovými aplikacemi a dalšími aplikacemi. V některých vzácných případech může být vhodné určit, že chcete použít vložené webové zobrazení. Další informace najdete v tématu [MSAL.NET používá webový prohlížeč](msal-net-web-browsers.md) a [prohlížeč systému Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Řešení potíží
Pokud vytvoříte novou aplikaci Xamarin. Forms a přidáte do ní odkaz na balíček NuGet MSAL.Net, bude to jenom fungovat.
Nicméně pokud chcete upgradovat existující aplikaci Xamarin. Forms na MSAL.NET Preview 1.1.2 nebo novější, může dojít k problémům se sestavením.

Chcete-li tyto problémy vyřešit, měli byste:
- Aktualizace existujícího balíčku NuGet MSAL.NET na verzi MSAL.NET verze 1.1.2 nebo novější
- Ověřte, že se Xamarin. Forms automaticky aktualizovala na verzi 2.5.0.122203 (Pokud ne, aktualizujte na tuto verzi).
- Ověřte, jestli se Xamarin. Android. support. v4 automaticky aktualizoval na verzi 25.4.0.2 (Pokud ne, aktualizujte na tuto verzi).
- Všechny balíčky Xamarin. Android. support by měly být cíleny na verzi 25.4.0.2
- Vyčistit/znovu sestavit
- Zkuste nastavit maximální paralelní projekt sestavení na 1 v aplikaci Visual Studio (Možnosti-> projekty a řešení – > sestavování a spouštění-> maximální počet paralelně sestavovaných projektů)
- Případně, pokud vytváříte z příkazového řádku, zkuste z příkazu odebrat/m, pokud ho používáte.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Chyba: Název ' AuthenticationContinuationHelper ' v aktuálním kontextu neexistuje.

To je pravděpodobně proto, že Visual Studio neaktualizovalo správně soubor Android. csproj *. **V\<některých případech cestu >** FilePath nesprávně obsahuje netstandard13 namísto **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Další postup

Další podrobnosti a ukázky najdete v článku o [specifických informacích pro Android](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) v následujícím souboru Readme.MD ukázky:

| Ukázka | Platforma | Popis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Jednoduchá aplikace Xamarin Forms předvádí, jak pomocí MSAL ověřovat MSA a Azure AD prostřednictvím koncového bodu AADD v 2.0 a přistupovat k Microsoft Graph s výsledným tokenem. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |