---
title: Předpoklady pro Xamarin Android (Microsoft Authentication Library pro .NET) | Azure
description: Přečtěte si o konkrétních doporučeních pro použití Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532577"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Doporučení pro Xamarin Android týkající se MSAL.NET
Tento článek popisuje konkrétní informace týkající se použití prohlížeče systému v Xamarin Android s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).

Od verze MSAL.NET 2.4.0-Preview MSAL.NET podporuje prohlížeče jiné než Chrome a už nevyžadují, aby se na zařízení s Androidem nainstalovala aplikace Chrome pro ověřování.

Doporučujeme používat prohlížeče, které podporují vlastní karty, například tyto:

| Prohlížeče s podporou vlastní karty | Název balíčku |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

Kromě prohlížečů s podporou vlastních karet na základě našeho testování bude pro ověřování fungovat i několik prohlížečů, které nepodporují vlastní karty: Opera, Opera Mini, inBrowser a Maxthon. Další informace najdete v [tabulce pro výsledky testů](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Známé problémy

- Pokud uživatel nemá na zařízení povolený prohlížeč, MSAL.NET vyvolá `AndroidActivityNotFound` výjimku. 
  - **Omezení rizik**: Informujte uživatele, že by měli povolit prohlížeč (nejlépe s podporou vlastní karty) na svém zařízení.

- Pokud ověřování neproběhne úspěšně (např. Spustí se ověřování s DuckDuckGo), MSAL.NET vrátí `AuthenticationCanceled MsalClientException`. 
  - **Problém**s kořenovým adresářem: V zařízení není povolený prohlížeč s podporou vlastní karty. Ověřování se spustilo v alternativním prohlížeči, které nedokázalo dokončit ověřování. 
  - **Omezení rizik**: Informujte uživatele, že by měli nainstalovat prohlížeč (nejlépe s podporou vlastní karty) na svém zařízení.

## <a name="devices-and-browsers-tested"></a>Testovaných zařízení a prohlížečů
V následující tabulce jsou uvedena zařízení a prohlížeče, které byly testovány.

| | Prohlížeee&ast;     |  Výsledek  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | Chrome&ast; | Úspěch|
| Huawei/jeden + | Edge&ast; | Úspěch|
| Huawei/jeden + | Firefox&ast; | Úspěch|
| Huawei/jeden + | Brave&ast; | Úspěch|
| Jedna + | Ecosia&ast; | Úspěch|
| Jedna + | Kiwi&ast; | Úspěch|
| Huawei/jeden + | Opera | Úspěch|
| Huawei | OperaMini | Úspěch|
| Huawei/jeden + | Prohlížeč | Úspěch|
| Jedna + | Maxthon | Úspěch|
| Huawei/jeden + | DuckDuckGo | Uživatel zrušil ověření.|
| Huawei/jeden + | Prohlížeč UC | Uživatel zrušil ověření.|
| Jedna + | Delfín | Uživatel zrušil ověření.|
| Jedna + | Prohlížeč CM | Uživatel zrušil ověření.|
| Huawei/jeden + | žádné nainstalované | AndroidActivityNotFound ex|

&ast;Podporuje vlastní karty

## <a name="next-steps"></a>Další kroky
Pro fragmenty kódu a další informace o používání prohlížeče systému s Xamarin Androidem si přečtěte tento [Průvodce](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  