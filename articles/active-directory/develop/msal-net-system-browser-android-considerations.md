---
title: Xamarin Android systém prohlížeč úvahy (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Další informace o aspektech používání systémových prohlížečů v systému Xamarin Android s Knihovnou ověřování Microsoft pro rozhraní .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132612"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android systém prohlížeč úvahy pro použití MSAL.NET

Tento článek popisuje, co byste měli zvážit při použití systémového prohlížeče v systému Xamarin Android s Knihovnou ověřování microsoftu pro .NET (MSAL.NET).

Počínaje MSAL.NET náhledem 2.4.0 podporuje MSAL.NET jiné prohlížeče než Chrome. Již nevyžaduje, aby byl Chrome nainstalován v zařízení Android pro ověřování.

Doporučujeme používat prohlížeče, které podporují vlastní karty. Zde je několik příkladů těchto prohlížečů:

| Prohlížeče, které podporují vlastní karty | Název balíčku |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Statečný | com.brave.browser|

Kromě identifikace prohlížečů, které nabízejí podporu vlastních karet, naše testování naznačuje, že několik prohlížečů, které nepodporují vlastní karty, také pracují pro ověřování. Tyto prohlížeče zahrnují Opera, Opera Mini, InBrowser a Maxthon. 

## <a name="tested-devices-and-browsers"></a>Testovaná zařízení a prohlížeče
V následující tabulce jsou uvedena zařízení a prohlížeče, které byly testovány na kompatibilitu ověřování.

| Zařízení | Prohlížeč     |  Výsledek  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | Předat|
| Huawei/One+ | Edge\* | Předat|
| Huawei/One+ | Firefox\* | Předat|
| Huawei/One+ | Statečný\* | Předat|
| Jeden+ | Ecosia\* | Předat|
| Jeden+ | Kiwi\* | Předat|
| Huawei/One+ | Opera | Předat|
| Huawei | OperaMini | Předat|
| Huawei/One+ | Vprohlížeč | Předat|
| Jeden+ | Maxthon | Předat|
| Huawei/One+ | Kachní kachna | Ověření bylo zrušeno uživatelem.|
| Huawei/One+ | Prohlížeč UC | Ověření bylo zrušeno uživatelem.|
| Jeden+ | Delfín | Ověření bylo zrušeno uživatelem.|
| Jeden+ | Prohlížeč CM | Ověření bylo zrušeno uživatelem.|
| Huawei/One+ | Není nainstalována žádná | Výjimka AndroidActivityNotFound|

\*Podporuje vlastní karty

## <a name="known-issues"></a>Známé problémy

Pokud uživatel nemá v zařízení povolen žádný prohlížeč, MSAL.NET vyvolá výjimku. `AndroidActivityNotFound`  
  - **Zmírnění :** Požádejte uživatele o povolení prohlížeče na svém zařízení. Doporučte prohlížeč, který podporuje vlastní karty.

Pokud se ověření nezdaří (například pokud se ověřování `AuthenticationCanceled MsalClientException`spustí pomocí DuckDuckGo), vrátí se MSAL.NET . 
  - **Problém s kořenem:** Prohlížeč, který podporuje vlastní karty, nebyl v zařízení povolen. Ověřování bylo spuštěno s prohlížečem, který nemohl dokončit ověřování. 
  - **Zmírnění :** Požádejte uživatele o povolení prohlížeče na svém zařízení. Doporučte prohlížeč, který podporuje vlastní karty.

## <a name="next-steps"></a>Další kroky
Další informace a příklady kódu naleznete v [tématu Výběr mezi vloženým webovým prohlížečem a systémovým prohlížečem v uživatelském](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) uživatelském prostředí Xamarin Android a [Embedded versus system web .](msal-net-web-browsers.md#embedded-vs-system-web-ui)  