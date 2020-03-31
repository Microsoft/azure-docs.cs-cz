---
title: Azure Front Door – zabezpečení aplikační vrstvy | Dokumenty společnosti Microsoft
description: Tento článek vám pomůže pochopit, jak Azure Front Door umožňuje chránit a zabezpečit back-endy aplikací
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e458926930c1b95d48886559551878fc6c9d0673
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471791"
---
# <a name="application-layer-security-with-front-door"></a>Zabezpečení aplikační vrstvy pomocí předních dveří
Azure Front Door poskytuje možnost ochrany webových aplikací k ochraně webových aplikací před síťovými útoky a běžnými webovými chybami zabezpečení, jako je SQL Injection nebo Cross Site Scripting (XSS). Zabezpečení aplikační vrstvy Front Door, které je povoleno pro front-endy http(s), je globálně distribuované a stále zapnuté, což zastavuje škodlivé útoky na okraji sítě Azure, daleko od back-endů. Díky přidané optimalizaci zabezpečení a výkonu poskytuje front door svým koncovým uživatelům rychlé a bezpečné webové prostředí.

## <a name="application-protection"></a>Ochrana aplikací
Ochrana aplikací předních dveří je konfigurována na každém hraničním prostředí po celém světě v souladu s aplikacemi a automaticky blokuje provoz bez http(s) od dosažení vašich webových aplikací. Naše víceklientská distribuovaná architektura umožňuje globální ochranu ve velkém měřítku bez obětování výkonu. Pro úlohy http(s) služba ochrany webových aplikací Front Door poskytuje bohatý modul pravidel pro vlastní pravidla, předkonfigurovanou sadu pravidel proti běžným útokům a podrobné protokolování pro všechny požadavky, které odpovídají pravidlu. Flexibilní akce, včetně povolit, blokovat nebo protokolovat pouze jsou podporovány.

## <a name="custom-access-control-rules"></a>Vlastní pravidla řízení přístupu
- **Seznam povolených ip a blokovaných položek:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě seznamu adres IP klientů. Ip v4 i IP v6 jsou podporovány
- **Geografické řízení přístupu:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě kódu země, ze které je ip adresa klienta
- **Filtrování parametrů HTTP:** Vlastní pravidla přístupu můžete konfigurovat na základě odpovídajících parametrů požadavků http(s), včetně záhlaví, adresy URL a řetězců dotazů.

## <a name="azure-managed-rules"></a>Pravidla spravovaná Azure
- Ve výchozím nastavení je povolena předkonfigurovaná sada pravidel proti běžným nejvyšším chybám zabezpečení OWASP. Ve verzi Preview sada pravidel zahrnuje sqli a xss požadavky kontroly. Budou přidána další pravidla. Můžete začít s protokolem pouze akce k ověření předkonfigurovaných pravidel fungovat podle očekávání pro vaše aplikace 

## <a name="rate-limiting"></a>Omezování rychlosti
- Pravidlo řízení rychlosti je omezit abnormální vysoký provoz z libovolné IP adresy klienta.  Můžete nastavit prahovou hodnotu pro počet webových požadavků povolených ip adresou klienta během jedné minuty trvání.

## <a name="centralized-protection-policy"></a>Centralizovaná ochrana
- Můžete definovat několik pravidel ochrany a přidat je do zásady v pořadí podle priority. Vlastní pravidla mají vyšší prioritu než spravovaná sada pravidel, která umožňuje výjimky. K webové aplikaci je přidružena jedna zásada.  Stejné zásady ochrany webových aplikací jsou replikovány na všechny hraniční servery ve všech umístěních, zajišťují konzistentní zásady zabezpečení ve všech oblastech.

## <a name="configuration"></a>Konfigurace
- Během náhledu můžete použít REST API, PowerShell nebo CLI k vytvoření a nasazení pravidel ochrany aplikací a zásad front door. Přístup k portálu bude podporován před tím, než bude služba obecně dostupná. 


## <a name="monitoring"></a>Monitorování
Front Door poskytuje možnost sledovat webové aplikace proti útokům pomocí metrik v reálném čase, které jsou integrované s Azure Monitor sledovat výstrahy a snadno sledovat trendy.

## <a name="pricing"></a>Ceny
Zabezpečení aplikační vrstvy předních dveří je během náhledu zdarma.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
