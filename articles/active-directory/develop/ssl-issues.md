---
title: Řešení potíží s protokolem TLS/SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, co dělat s různými problémy pomocí certifikátů TLS/SSL s MSAL. Cíl – knihovna C.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.custom: aaddev
ms.openlocfilehash: 1507231c3ab395319d5ce95ec06dbb592c324aa6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "80881073"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Postupy: řešení potíží s MSAL pro iOS a macOS s protokolem TLS/SSL

Tento článek poskytuje informace, které vám pomůžou při řešení problémů, ke kterým může docházet při používání [knihovny Microsoft Authentication Library (MSAL) pro iOS a MacOS](reference-v2-libraries.md) .

## <a name="network-issues"></a>Problémy se sítí

**Chyba-1200**: došlo k chybě SSL a zabezpečené připojení k serveru nelze vytvořit.

Tato chyba znamená, že připojení není zabezpečené. K tomu dojde, pokud je certifikát neplatný. Další informace, včetně toho, který server selhává při kontrole TLS, najdete `NSURLErrorFailingURLErrorKey` ve `userInfo` slovníku objektu Error.

Tato chyba pochází ze síťové knihovny společnosti Apple. Úplný seznam kódů chyb NSURL je v NSURLError. h v sadách SDK pro macOS a iOS. Další podrobnosti o této chybě najdete v tématu [adresy URL při načítání kódů chyb systému](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Problémy s certifikátem

Pokud se adresa URL, která poskytuje neplatný certifikát, připojí k serveru, který chcete použít jako součást toku ověřování, dobrým účelem diagnostiky problému je otestování adresy URL pomocí ověřovací služby SSL, jako je například [Test serveru SSL](https://www.ssllabs.com/ssltest/analyze.html). Testuje server na nejrůznějších scénářích a prohlížečích a kontroluje mnoho známých chyb zabezpečení.

Ve výchozím nastavení se nová funkce [Security Transport Security (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) společnosti Apple aplikuje přísnější zásady zabezpečení pro aplikace, které používají certifikáty TLS/SSL. Některé operační systémy a webové prohlížeče začali ve výchozím nastavení vynucovat některé z těchto zásad. Z bezpečnostních důvodů Nedoporučujeme zakázat ATS.

Certifikáty využívající hodnoty hash SHA-1 mají známé chyby zabezpečení. Většina moderních webových prohlížečů nepovoluje certifikáty s hodnotami hash SHA-1.

## <a name="captive-portals"></a>Nedobrovolný portál

Bezdiskový portál prezentuje uživateli webovou stránku při prvním přístupu k síti Wi-Fi a ještě mu nebyl udělen přístup k této síti. Zachycuje svůj internetový provoz, dokud uživatel nesplní požadavky portálu. Chyby sítě, protože se uživatel nemůže připojit k síťovým prostředkům, dokud se uživatel nepřipojí přes portál.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [nedobrovolných portálech](https://en.wikipedia.org/wiki/Captive_portal) a funkci nového [zabezpečení přenosu aplikací (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) společnosti Apple.
