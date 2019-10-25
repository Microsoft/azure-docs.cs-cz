---
title: Předpoklady pro Xamarin Android (Microsoft Authentication Library pro .NET)
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: 8f5caad4b136c9ef2686cc4befc70e6720e27855
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802751"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Doporučení pro Xamarin Android týkající se MSAL.NET
Tento článek popisuje konkrétní informace týkající se použití prohlížeče systému v Xamarin Android s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).

Od verze MSAL.NET 2.4.0-Preview MSAL.NET podporuje prohlížeče jiné než Chrome a už nevyžadují, aby se na zařízení s Androidem nainstalovala aplikace Chrome pro ověřování.

Doporučujeme používat prohlížeče, které podporují vlastní karty, například tyto:

| Prohlížeče s podporou vlastní karty | Název balíčku |
|------| ------- |
|Chrome | com. Android. Chrome|
|Microsoft Edge | com. Microsoft. emmx|
|Firefox | org. mozilla. Firefox|
|Ecosia | com. ecosia. Android|
|Kiwi | com. kiwibrowser. browser|
|Brave | com. Brave. browser|

Kromě prohlížečů s podporou vlastních karet na základě našeho testování bude pro ověřování fungovat i několik prohlížečů, které nepodporují vlastní karty: Opera, Opera Mini, inBrowser a Maxthon. Další informace najdete v [tabulce pro výsledky testů](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Známé problémy

- Pokud uživatel nemá na zařízení povolený prohlížeč, MSAL.NET vyvolá výjimku `AndroidActivityNotFound`. 
  - **Omezení rizik**: Informujte uživatele, že by měli povolit prohlížeč (nejlépe s podporou vlastní karty) na svém zařízení.

- Pokud ověřování neproběhne úspěšně (např. Spustí se ověřování s DuckDuckGo), MSAL.NET vrátí `AuthenticationCanceled MsalClientException`. 
  - **Problém s kořenovým adresářem**: v zařízení není povolený prohlížeč s podporou vlastních karet. Ověřování se spustilo v alternativním prohlížeči, které nedokázalo dokončit ověřování. 
  - **Omezení rizik**: Informujte uživatele o tom, že by měli nainstalovat prohlížeč (nejlépe s podporou vlastní karty) na svém zařízení.

## <a name="devices-and-browsers-tested"></a>Testovaných zařízení a prohlížečů
V následující tabulce jsou uvedena zařízení a prohlížeče, které byly testovány.

| | &ast; prohlížeče     |  Výsledek  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | &ast; pro Chrome | Dána|
| Huawei/jeden + | &ast; Edge | Dána|
| Huawei/jeden + | &ast; Firefox | Dána|
| Huawei/jeden + | Brave&ast; | Dána|
| Jedna + | Ecosia&ast; | Dána|
| Jedna + | Kiwi&ast; | Dána|
| Huawei/jeden + | Opera | Dána|
| Huawei | OperaMini | Dána|
| Huawei/jeden + | Prohlížeč | Dána|
| Jedna + | Maxthon | Dána|
| Huawei/jeden + | DuckDuckGo | Uživatel zrušil ověření.|
| Huawei/jeden + | Prohlížeč UC | Uživatel zrušil ověření.|
| Jedna + | Delfín | Uživatel zrušil ověření.|
| Jedna + | Prohlížeč CM | Uživatel zrušil ověření.|
| Huawei/jeden + | žádné nainstalované | AndroidActivityNotFound ex|

&ast; podporuje vlastní karty

## <a name="next-steps"></a>Další kroky
Pro fragmenty kódu a další informace o používání prohlížeče systému s Xamarin Androidem si přečtěte tento [Průvodce](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  