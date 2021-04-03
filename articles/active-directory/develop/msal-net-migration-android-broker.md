---
title: Migrace aplikací pro Xamarin Android pomocí zprostředkovatelů do MSAL.NET
titleSuffix: Microsoft identity platform
description: Naučte se migrovat aplikace Xamarin Android, které používají Microsoft Authenticator nebo Portál společnosti Intune z ADAL.NET do MSAL.NET.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 01af0e620ecb100839f7b1101e5ff9fcfc874eea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206665"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>Migrace aplikací pro Android, které používají zprostředkovatele z ADAL.NET na MSAL.NET

Pokud máte aplikaci pro Xamarin Android, která aktuálně používá knihovnu Azure Active Directory Authentication Library pro .NET (ADAL.NET) a [zprostředkovatele ověřování](msal-android-single-sign-on.md), je čas migrovat do [knihovny Microsoft Authentication Library pro .NET ](msal-overview.md) (MSAL.NET).

## <a name="prerequisites"></a>Požadavky

* Aplikace Xamarin Android je už integrovaná s zprostředkovatelem ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) nebo [Portál společnosti Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) a ADAL.NET, které potřebujete migrovat na MSAL.NET.

## <a name="step-1-enable-the-broker"></a>Krok 1: povolení zprostředkovatele

<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
V ADAL.NET je podpora zprostředkovatele povolená na základě kontextu ověřování.

Chcete-li volat zprostředkovatele, měli byste nastavit `useBroker` na *hodnotu true* v `PlatformParameters` konstruktoru:

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

V kódu pro vykreslování stránky specifického pro platformu Android nastavte `useBroker` příznak na hodnotu true:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Pak zahrňte parametry v volání metody získání tokenu:

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
V MSAL.NET je podpora zprostředkovatelů povolená na základě PublicClientApplication.

Použít `WithBroker()` parametr (který je ve výchozím nastavení nastaven na hodnotu true) k volání Broker:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

Pak ve volání AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>Krok 2: nastavení aktivity

V ADAL.NET jste předali v rámci aktivity (obvykle MainActivity) jako součást PlatformParameters, jak je znázorněno v části [Krok 1: povolení zprostředkovatele](#step-1-enable-the-broker).

MSAL.NET také používá aktivitu, ale nepožaduje se v běžném použití Androidu bez zprostředkovatele. Chcete-li použít zprostředkovatele, nastavte aktivitu pro odesílání a příjem odpovědí od zprostředkovatele.

<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
Aktivita se předává do PlatformParameters v platformě specifické pro Android.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

V MSAL.NET proveďte dvě věci pro nastavení aktivity pro Android:

1. V aplikaci nastavte na, aby se `MainActivity.cs` `App.RootViewController` `MainActivity` zajistila aktivita se voláním zprostředkovatele.

    Pokud není správně nastavená, může se zobrazit tato chyba: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. Na volání AcquireTokenInteractive použijte `.WithParentActivityOrWindow(App.RootViewController)` a předejte odkaz na aktivitu, kterou budete používat. Tento příklad použije MainActivity.

**Například:**

V *App. cs*:

```CSharp
   public static object RootViewController { get; set; }
```

V *MainActivity. cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

Ve volání AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Další kroky

Další informace o otázkách specifických pro Android při použití MSAL.NET s Xamarin najdete v tématu [požadavky na konfiguraci a tipy pro řešení potíží pro Xamarin Android s MSAL.NET](msal-net-xamarin-android-considerations.md).