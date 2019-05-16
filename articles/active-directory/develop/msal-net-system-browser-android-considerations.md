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
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544427"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Důležité informace specifické pro Xamarin Android pomocí MSAL.NET
Tento článek popisuje konkrétní aspekty při používání prohlížeče systému v Xamarin pro Android s Microsoft Authentication Library pro .NET (MSAL.NET).

Počínaje MSAL.NET 2.4.0-preview, MSAL.NET podporuje prohlížečích než Chrome a už nevyžaduje Chrome nainstalovat na zařízení s Androidem pro ověřování.

Doporučujeme, abyste že pomocí prohlížeče, které podporují vlastní karty, jako je například tyto:

| Prohlížeče s podporou vlastní karty | Název balíčku |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Kulatý háček | com.brave.browser|

Kromě prohlížeče s podporou vlastní karty, na základě našich testování několika prohlížečích, které nepodporují vlastní karty budou fungovat i pro ověřování: Opera, Opera Mini InBrowser a Maxthon. Další informace najdete v článku [tabulky pro výsledky testů](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Známé problémy

- Pokud uživatel nemá žádný prohlížeč na zařízení povolená, vyvolá výjimku MSAL.NET `AndroidActivityNotFound` výjimky. 
  - **Zmírnění dopadů**: Informujte uživatele, že by měl umožňují prohlížeče (nejlépe s podporou vlastní karty) na svém zařízení.

- Pokud se ověření nezdaří (např. ověřování spustí s DuckDuckGo), vrátí MSAL.NET `AuthenticationCanceled MsalClientException`. 
  - **Hlavní problém**: Prohlížeč s podporou vlastní karty nebyl povolen v zařízení. Ověřování se spustila s alternativní prohlížeč, který nebylo možné dokončit ověřování. 
  - **Zmírnění dopadů**: Informujte uživatele, měli by tito instalovat prohlížeče (nejlépe s podporou vlastní kartu) na svém zařízení.

## <a name="devices-and-browsers-tested"></a>Zařízení a testovat prohlížeče
V následující tabulce jsou uvedeny, zařízení a prohlížeče, které byly testovány.

| | Prohlížeč&ast;     |  Výsledek  | 
| ------------- |:-------------:|:-----:|
| Huawei jeden + / | Chrome&ast; | Úspěch|
| Huawei jeden + / | Edge&ast; | Úspěch|
| Huawei jeden + / | Firefox&ast; | Úspěch|
| Huawei jeden + / | Kulatý háček&ast; | Úspěch|
| Jeden + | Ecosia&ast; | Úspěch|
| Jeden + | Kiwi&ast; | Úspěch|
| Huawei jeden + / | Opera | Úspěch|
| Huawei | OperaMini | Úspěch|
| Huawei jeden + / | InBrowser | Úspěch|
| Jeden + | Maxthon | Úspěch|
| Huawei jeden + / | DuckDuckGo | Uživatel zrušil ověřování|
| Huawei jeden + / | UC prohlížeče | Uživatel zrušil ověřování|
| Jeden + | Delfínů | Uživatel zrušil ověřování|
| Jeden + | Prohlížeč CM | Uživatel zrušil ověřování|
| Huawei jeden + / | žádná nainstalovaná | AndroidActivityNotFound ex|

&ast; Podporuje vlastní karty

## <a name="next-steps"></a>Další postup
Kód fragmenty kódu a další informace o používání prohlížeč systému s Xamarin Androidem, najdete v tomto [průvodce](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  