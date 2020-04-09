---
title: Poradce při potížích s TLS/SSL (MSAL iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, co dělat s různými problémy s použitím certifikátů TLS/SSL s msal. Knihovna Objective-C.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881073"
---
# <a name="how-to-troubleshoot-msal-for-ios-and-macos-tlsssl-issues"></a>Postup: Řešení potíží s msal pro problémy s iOS a macOS TLS/SSL

Tento článek obsahuje informace, které vám pomohou vyřešit problémy, se kterými se můžete sejít při používání [Knihovny ověřování MicrosoftU (MSAL) pro iOS a macOS](reference-v2-libraries.md)

## <a name="network-issues"></a>Problémy se sítí

**Chyba -1200**: "Došlo k chybě SSL a nelze provést zabezpečené připojení k serveru."

Tato chyba znamená, že připojení není zabezpečené. K tomu dochází, když je certifikát neplatný. Další informace, včetně toho, který server se nedaří kontrola TLS, naleznete `NSURLErrorFailingURLErrorKey` ve `userInfo` slovníku objektu chyby.

Tato chyba je z síťové knihovny společnosti Apple. Úplný seznam kódů chyb NSURL je v souboru NSURLError.h v sadách MacOS a iOS SDK. Další podrobnosti o této chybě naleznete v [tématu URL Loading System Error Codes](https://developer.apple.com/documentation/foundation/1508628-url_loading_system_error_codes?language=objc).

## <a name="certificate-issues"></a>Certifikáty

Pokud se adresa URL poskytující neplatný certifikát připojí k serveru, který chcete použít jako součást toku ověřování, je dobrým začátkem diagnostiky problému otestovat adresu URL pomocí ověřovací služby SSL, jako je [například test serveru SSL](https://www.ssllabs.com/ssltest/analyze.html). Testuje server proti široké škále scénářů a prohlížečů a kontroluje mnoho známých chyb zabezpečení.

Ve výchozím nastavení nová funkce [zabezpečení přenosu aplikací (ATS)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) společnosti Apple používá přísnější zásady zabezpečení pro aplikace, které používají certifikáty TLS/SSL. Některé operační systémy a webové prohlížeče začaly ve výchozím nastavení vynucovat některé z těchto zásad. Z bezpečnostních důvodů doporučujeme ats zakázat.

Certifikáty používající hashy SHA-1 mají známé chyby zabezpečení. Většina moderních webových prohlížečů nepovoluje certifikáty s hashe sha-1.

## <a name="captive-portals"></a>Kaptivní portály

Přijatý portál představuje uživateli webovou stránku, když poprvé přistupuje k síti Wi-Fi a dosud mu nebyl udělen přístup k této síti. Zachytí jejich internetový provoz, dokud uživatel nesplní požadavky portálu. Chyby sítě, protože uživatel se nemůže připojit k síťovým prostředkům, se očekávají, dokud se uživatel nepřipojí prostřednictvím portálu.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [kaptivních portálech](https://en.wikipedia.org/wiki/Captive_portal) a nové funkci [Ats (App Transport Security)](https://developer.apple.com/library/archive/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW35) společnosti Apple.
