---
title: Požadavky na prohlížeč systému Xamarin Android (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o konkrétních požadavcích při použití systémových prohlížečů v Xamarin Androidu s knihovnou Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3ea2554fac8654b052e3e38633af23e7c778b3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915469"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Doporučení pro prohlížeč systému Xamarin Android s MSAL.NET

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

- Pokud uživatel nemá na zařízení povolený prohlížeč, MSAL.NET vyvolá výjimku `AndroidActivityNotFound`. 
  - **Omezení rizik**: Informujte uživatele, že by měli povolit prohlížeč (nejlépe s podporou vlastní karty) na svém zařízení.

- Pokud ověřování neproběhne úspěšně (např. Spustí se ověřování s DuckDuckGo), MSAL.NET vrátí `AuthenticationCanceled MsalClientException`. 
  - **Problém s kořenovým adresářem**: v zařízení není povolený prohlížeč s podporou vlastních karet. Ověřování se spustilo v alternativním prohlížeči, které nedokázalo dokončit ověřování. 
  - **Omezení rizik**: Informujte uživatele o tom, že by měli nainstalovat prohlížeč (nejlépe s podporou vlastní karty) na svém zařízení.

## <a name="devices-and-browsers-tested"></a>Testovaných zařízení a prohlížečů
V následující tabulce jsou uvedena zařízení a prohlížeče, které byly testovány.

| | &ast; prohlížeče     |  Výsledek  | 
| ------------- |:-------------:|:-----:|
| Huawei/jeden + | &ast; pro Chrome | Průchod|
| Huawei/jeden + | Edge&ast; | Průchod|
| Huawei/jeden + | Firefox&ast; | Průchod|
| Huawei/jeden + | Brave&ast; | Průchod|
| Jedna + | Ecosia&ast; | Průchod|
| Jedna + | Kiwi&ast; | Průchod|
| Huawei/jeden + | Opera | Průchod|
| Huawei | OperaMini | Průchod|
| Huawei/jeden + | Prohlížeč | Průchod|
| Jedna + | Maxthon | Průchod|
| Huawei/jeden + | DuckDuckGo | Uživatel zrušil ověření.|
| Huawei/jeden + | Prohlížeč UC | Uživatel zrušil ověření.|
| Jedna + | Delfín | Uživatel zrušil ověření.|
| Jedna + | Prohlížeč CM | Uživatel zrušil ověření.|
| Huawei/jeden + | žádné nainstalované | AndroidActivityNotFound ex|

&ast; podporuje vlastní karty

## <a name="next-steps"></a>Další kroky
Pro fragmenty kódu a další informace o používání prohlížeče systému s Xamarin Androidem si přečtěte tento [Průvodce](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  