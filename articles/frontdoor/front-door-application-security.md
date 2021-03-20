---
title: Přední dvířka Azure – zabezpečení aplikační vrstvy | Microsoft Docs
description: Tento článek vám pomůže pochopit, jak můžou služby Azure front-endu chránit a zabezpečovat back-endy vaší aplikace.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: 4ee50b4c7da27df3630c1b4d263f076da44189bc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89399935"
---
# <a name="application-layer-security-with-front-door"></a>Zabezpečení aplikační vrstvy s předními dvířky
Přední dveře Azure poskytují možnost ochrany webových aplikací, která chrání webové aplikace před útoky ze sítě a běžnými webovými ohroženími zabezpečení, jako je vkládání SQL nebo skriptování mezi weby (XSS). Povoleno pro front-endy http (s), zabezpečení vrstvy aplikace front-endu je globálně distribuované a pořád se zastavuje, aby se na hraniční síti Azure od back-endu zastavily škodlivé útoky. Díky zvýšení zabezpečení a optimalizace výkonu poskytují přední dveře rychlé a zabezpečené webové prostředí koncovým uživatelům.

## <a name="application-protection"></a>Ochrana aplikací
Ochrana aplikací na předních dveřích je nakonfigurovaná na všech hraničních prostředích po celém světě, v souladu s aplikacemi a automaticky blokuje provoz jiného typu než HTTP (s), aby se dosáhlo svých webových aplikací. Naše vícevrstvá distribuovaná architektura umožňuje škálování globální ochrany bez obětování výkonu. Pro úlohy http (s), služba ochrany webových aplikací předních dveří poskytuje bohatý modul pravidel pro vlastní pravidla, předem nakonfigurovaná RuleSet proti běžným útokům a podrobné protokolování všech požadavků, které odpovídají pravidlu. Podporují se flexibilní akce, včetně povolených, blokovaných nebo log.

## <a name="custom-access-control-rules"></a>Vlastní pravidla řízení přístupu
- Seznam **povolených a blokovaných IP adres:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě seznamu IP adres klientů. Podporují se protokoly IP v4 i IP v6.
- **Geografické řízení přístupu:** Můžete nakonfigurovat vlastní pravidla pro řízení přístupu k webovým aplikacím na základě kódu země, ze kterého je IP adresa klienta.
- **Filtrování parametrů http:** Můžete nakonfigurovat vlastní pravidla přístupu na základě vyhovujících parametrů požadavků HTTP (s), včetně hlaviček, adres URL a řetězců dotazů.

## <a name="azure-managed-rules"></a>Pravidla spravovaná službou Azure
- Ve výchozím nastavení je povolená předem nakonfigurovaná sada pravidel proti běžným chybám zabezpečení OWASP. Ve verzi Preview sada pravidel zahrnuje kontrolu požadavků SQLi a XSS. Budou přidána další pravidla. Můžete se rozhodnout, že začnete pracovat jenom s protokolem, abyste ověřili, jestli pro vaše aplikace funguje podle očekávání předkonfigurovaná pravidla. 

## <a name="rate-limiting"></a>Omezování rychlosti
- Pravidlem řízení sazeb je omezit neobvykle vysoký provoz z jakékoli IP adresy klienta.  Můžete nastavit prahovou hodnotu počtu webových požadavků povolených IP adresou klienta během jedné minuty.

## <a name="centralized-protection-policy"></a>Zásady centralizované ochrany
- Je možné definovat několik pravidel ochrany a přidat je do zásad v pořadí podle priority. Vlastní pravidla mají vyšší prioritu než spravovaná RuleSet, aby bylo možné výjimky. K vaší webové aplikaci je přidružená jedna zásada.  Stejné zásady ochrany webových aplikací se replikují na všechny hraniční servery ve všech umístěních. Zajistěte konzistentní zásady zabezpečení ve všech oblastech.

## <a name="configuration"></a>Konfigurace
- Během období Preview můžete pomocí rozhraní REST API, PowerShellu nebo rozhraní příkazového řádku vytvářet a nasazovat pravidla a zásady ochrany aplikací na předních dveřích. Přístup k portálu bude podporován předtím, než je služba všeobecně dostupná. 


## <a name="monitoring"></a>Monitorování
Přední dvířka umožňují monitorovat webové aplikace proti útokům pomocí metrik v reálném čase, které jsou integrovány s Azure Monitor ke sledování výstrah a ke snadnému monitorování trendů.

## <a name="pricing"></a>Ceny
Zabezpečení aplikační vrstvy na předních dveřích je ve verzi Preview zdarma.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
