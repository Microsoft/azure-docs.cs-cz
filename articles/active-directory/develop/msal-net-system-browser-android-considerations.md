---
title: Požadavky na prohlížeč systému Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s důležitými informacemi o používání systémových prohlížečů v Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132612"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Předpoklady pro prohlížeč systému Xamarin Android pro použití MSAL.NET

Tento článek popisuje, co byste měli vzít v úvahu při použití prohlížeče systému v Xamarin Android s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).

Počínaje verzí MSAL.NET 2.4.0 Preview podporuje MSAL.NET prohlížeče jiné než Chrome. Už není potřeba na zařízení s Androidem instalovat Chrome pro ověřování.

Doporučujeme používat prohlížeče, které podporují vlastní karty. Tady je několik příkladů těchto prohlížečů:

| Prohlížeče, které podporují vlastní karty | Název balíčku |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

Kromě identifikace prohlížečů, které nabízí podporu vlastních karet, naše testování indikuje, že pro ověřování funguje i několik prohlížečů, které nepodporují vlastní karty. Mezi tyto prohlížeče patří Opera, Opera Mini, inBrowser a Maxthon. 

## <a name="tested-devices-and-browsers"></a>Testovaná zařízení a prohlížeče
V následující tabulce jsou uvedena zařízení a prohlížeče, které byly testovány pro kompatibilitu ověřování.

| Zařízení | Prohlížeč     |  Výsledek  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | \* pro Chrome | Úspěšný|
| Huawei/jeden + | \* Edge | Úspěšný|
| Huawei/jeden + | \* Firefox | Úspěšný|
| Huawei/jeden + | Brave\* | Úspěšný|
| Jedna + | Ecosia\* | Úspěšný|
| Jedna + | Kiwi\* | Úspěšný|
| Huawei/jeden + | Opera | Úspěšný|
| Huawei | OperaMini | Úspěšný|
| Huawei/jeden + | Prohlížeč | Úspěšný|
| Jedna + | Maxthon | Úspěšný|
| Huawei/jeden + | DuckDuckGo | Uživatel zrušil ověřování.|
| Huawei/jeden + | Prohlížeč UC | Uživatel zrušil ověřování.|
| Jedna + | Delfín | Uživatel zrušil ověřování.|
| Jedna + | Prohlížeč CM | Uživatel zrušil ověřování.|
| Huawei/jeden + | žádné nainstalované | Výjimka AndroidActivityNotFound|

\* podporuje vlastní karty

## <a name="known-issues"></a>Známé problémy

Pokud uživatel nemá na zařízení povolený prohlížeč, MSAL.NET vyvolá výjimku `AndroidActivityNotFound`.  
  - **Zmírnění rizika**: požádejte uživatele, aby na svém zařízení povolil prohlížeč. Doporučuje se použít prohlížeč, který podporuje vlastní karty.

Pokud se ověření nezdaří (například pokud se spustí ověřování pomocí DuckDuckGo), MSAL.NET vrátí `AuthenticationCanceled MsalClientException`. 
  - **Problém s kořenovým adresářem**: v zařízení není povolený prohlížeč, který podporuje vlastní karty. Ověřování bylo spuštěno s prohlížečem, který nemůže dokončit ověřování. 
  - **Zmírnění rizika**: požádejte uživatele, aby na svém zařízení povolil prohlížeč. Doporučuje se použít prohlížeč, který podporuje vlastní karty.

## <a name="next-steps"></a>Další kroky
Další informace a příklady kódu najdete v tématu [Výběr mezi vloženým webovým prohlížečem a systémovým prohlížečem v prostředí Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) a [Embedded versus systémové webové uživatelské rozhraní](msal-net-web-browsers.md#embedded-vs-system-web-ui).  