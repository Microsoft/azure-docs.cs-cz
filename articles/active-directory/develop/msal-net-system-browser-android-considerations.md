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
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8f755b42249a88a2d37117003d561f79aea6b170
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88165987"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Předpoklady pro prohlížeč systému Xamarin Android pro použití MSAL.NET

Tento článek popisuje, co byste měli vzít v úvahu při použití prohlížeče systému v Xamarin Android s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).

Počínaje verzí MSAL.NET 2.4.0 Preview podporuje MSAL.NET prohlížeče jiné než Chrome. Už není potřeba na zařízení s Androidem instalovat Chrome pro ověřování.

Doporučujeme používat prohlížeče, které podporují vlastní karty. Tady je několik příkladů těchto prohlížečů:

| Prohlížeče, které podporují vlastní karty | Název balíčku |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. browser|
|Brave | com. Brave. browser|

Kromě identifikace prohlížečů, které nabízí podporu vlastních karet, naše testování indikuje, že pro ověřování funguje i několik prohlížečů, které nepodporují vlastní karty. Mezi tyto prohlížeče patří Opera, Opera Mini, inBrowser a Maxthon. 

## <a name="tested-devices-and-browsers"></a>Testovaná zařízení a prohlížeče
V následující tabulce jsou uvedena zařízení a prohlížeče, které byly testovány pro kompatibilitu ověřování.

| Zařízení | Prohlížeč     |  Výsledek  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | Chrome\* | Dána|
| Huawei/jeden + | Edge\* | Dána|
| Huawei/jeden + | Firefox\* | Dána|
| Huawei/jeden + | Brave\* | Dána|
| Jedna + | Ecosia\* | Dána|
| Jedna + | Kiwi\* | Dána|
| Huawei/jeden + | Opera | Dána|
| Huawei | OperaMini | Dána|
| Huawei/jeden + | Prohlížeč | Dána|
| Jedna + | Maxthon | Dána|
| Huawei/jeden + | DuckDuckGo | Uživatel zrušil ověřování.|
| Huawei/jeden + | Prohlížeč UC | Uživatel zrušil ověřování.|
| Jedna + | Delfín | Uživatel zrušil ověřování.|
| Jedna + | Prohlížeč CM | Uživatel zrušil ověřování.|
| Huawei/jeden + | Žádné nainstalované | Výjimka AndroidActivityNotFound|

\* Podporuje vlastní karty

## <a name="known-issues"></a>Známé problémy

Pokud uživatel nemá na zařízení povolený prohlížeč, MSAL.NET vyvolá `AndroidActivityNotFound` výjimku.  
  - **Zmírnění rizika**: požádejte uživatele, aby na svém zařízení povolil prohlížeč. Doporučuje se použít prohlížeč, který podporuje vlastní karty.

Pokud se ověření nezdaří (například pokud se ověřování spustí s DuckDuckGo), MSAL.NET vrátí `AuthenticationCanceled MsalClientException` . 
  - **Problém s kořenovým adresářem**: v zařízení není povolený prohlížeč, který podporuje vlastní karty. Ověřování bylo spuštěno s prohlížečem, který nemůže dokončit ověřování. 
  - **Zmírnění rizika**: požádejte uživatele, aby na svém zařízení povolil prohlížeč. Doporučuje se použít prohlížeč, který podporuje vlastní karty.

## <a name="next-steps"></a>Další kroky
Další informace a příklady kódu najdete v tématu [Výběr mezi vloženým webovým prohlížečem a systémovým prohlížečem v prostředí Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) a [Embedded versus systémové webové uživatelské rozhraní](msal-net-web-browsers.md#embedded-vs-system-web-ui).  