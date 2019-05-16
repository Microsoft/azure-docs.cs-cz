---
title: Důležité informace o Xamarin Android (knihovna Microsoft Authentication Library pro .NET) | Azure
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
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0cfb06e95cadbb549f669e5d59bdb0d795c896
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545860"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Důležité informace specifické pro Xamarin Android pomocí MSAL.NET
Tento článek popisuje konkrétní aspekty při používání Xamarin Android s Microsoft Authentication Library pro .NET (MSAL.NET).

Tento článek je určený pro MSAL.NET 3.x. Pokud vás zajímají MSAL.NET 2.x, naleznete v tématu [specifika Xamarin Android v MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>Nastavte Nadřazená aktivita

V Xamarin.Android je nutné nastavit Nadřazená aktivita tak, že token, který získá zpět po interakce se stalo.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Zajištění ovládací prvek se vrátí do MSAL jednou interaktivní spotřebovanou část končí tok ověřování
V systému Android, je nutné přepsat `OnActivityResult` metodu `Activity` a volání metody SetAuthenticationContinuationEventArgs AuthenticationContinuationHelper MSAL třídy.

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
Tento řádek se zajistí, že ovládací prvek se vrátí do MSAL po interaktivní spotřebovanou část tok ověřování skončila.

## <a name="update-the-android-manifest"></a>Aktualizace manifestu Androidu
`AndroidManifest.xml` By měl obsahovat následující hodnoty:
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

## <a name="use-the-embedded-web-view-optional"></a>Pomocí integrovaného webového zobrazení (volitelné)

Ve výchozím nastavení používá MSAL.NET webový prohlížeč systému, který umožňuje získat jednotné přihlašování u webových aplikací a jinými aplikacemi. V některých výjimečných případech můžete chtít určit, že chcete použít vložený webové zobrazení. Další informace najdete v tématu [MSAL.NET používá webový prohlížeč](msal-net-web-browsers.md) a [prohlížeč systému Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Řešení potíží
Pokud vytvoříte novou aplikaci pro Xamarin.Forms a přidejte odkaz na balíček MSAL.Net NuGet, to budou prostě fungovat.
Pokud chcete provést upgrade existující aplikaci Xamarin.Forms MSAL.NET 1.1.2 ve verzi preview nebo novější můžete setkat s problémy se sestavením.

Při řešení těchto problémů, měli byste provést tyto akce:
- Aktualizovat existující balíček MSAL.NET NuGet MSAL.NET ve verzi Preview 1.1.2 nebo novější
- Zkontrolujte, že Xamarin.Forms automaticky aktualizuje na verzi 2.5.0.122203 (Pokud ne, aktualizujte tuto verzi)
- Zkontrolujte, že Xamarin.Android.Support.v4 automaticky aktualizuje na verzi 25.4.0.2 (Pokud ne, aktualizujte tuto verzi)
- Všechny balíčky Xamarin.Android.Support by měl být cílení na verzi 25.4.0.2
- Vyčistit/opětovné sestavení
- Zkuste nastavení maximální počet paralelních projektu sestavení na hodnotu 1 v sadě Visual Studio (Možnosti -> projekty a řešení -> sestavení a spuštění -> maximální počet paralelních projekty sestavení)
- Případně Pokud sestavujete z příkazového řádku, zkuste /m ze svých rukou pokud ho používáte.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Chyba: Název 'AuthenticationContinuationHelper' v aktuálním kontextu neexistuje.

To je pravděpodobně protože Visual Studio neaktualizovala správně soubor Android.csproj*. Někdy **<HintPath>** filepath obsahuje nesprávně netstandard13 místo **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Další postup

Další podrobnosti a ukázky jsou k dispozici v [Android konkrétní aspekty](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) odstavec souboru readme.md v následující ukázce:

| Ukázka | Platforma | Popis |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP | Jednoduchá aplikace Xamarin Forms která ukazuje použití MSAL k ověřování MSA a Azure AD prostřednictvím koncového bodu v2.0 AADD a přístup k Microsoft Graphu pomocí výsledný token. <br>![Topologie](media/msal-net-xamarin-android-considerations/topology.png) |