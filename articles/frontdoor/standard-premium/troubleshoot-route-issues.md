---
title: Řešení problémů s konfigurací služby Azure front-end Standard/Premium
description: V tomto kurzu se dozvíte, jak řešit některé běžné problémy, které se můžou stát pro instanci standard/Premium služby Azure front.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099255"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Řešení běžných potíží se směrováním pomocí Azure front-in standard/Premium

Tento článek popisuje, jak řešit běžné problémy se směrováním, na které se můžete setkat při konfiguraci front-výstupů Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 reakce z front Azure na přední dveře po několika sekundách

### <a name="symptom"></a>Příznak

* Pravidelné požadavky odeslané do back-endu bez přechodu přes přední dveře Azure jsou úspěšné. Při přechodu přes službu Azure front-restarts dojde k chybovým odpovědím 503.
* Selhání z front Azure na předních dveřích se většinou zobrazuje přibližně 30 sekund.

### <a name="cause"></a>Příčina

Příčinou tohoto problému může být jedna z následujících dvou věcí:
 
* Váš počátek trvá déle, než je nakonfigurovaný časový limit (výchozí hodnota je 30 sekund), aby se žádost dostala z front-dveří Azure.
* Doba potřebná k odeslání odpovědi na žádost z front Azure na začátku trvá déle než hodnota časového limitu. 

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

* Odešlete žádost přímo do back-endu (bez přechodu přes přední vrátka Azure). Podívejte se, jak dlouho vaše back-end obvykle trvá reagovat.
* Odešlete žádost přes dvířka Azure a podívejte se, jestli se vám zobrazují odpovědi 503. V takovém případě se nemusí jednat o problém s vypršením časového limitu. Obraťte se na podporu.
* Pokud projdete předními dvířky Azure, dojde k chybě v kódu chyby 503, nakonfigurujte `sendReceiveTimeout` pole pro přední dveře Azure. Výchozí časový limit můžete zvětšit na 4 minuty (240 sekund). Nastavení je v rámci `Endpoint Setting` a voláno `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Požadavky odeslané do vlastní domény vrátí stavový kód 400.

### <a name="symptom"></a>Příznak

* Vytvořili jste instanci front-endu Azure, ale požadavek na hostitele domény nebo front-end vrací kód stavu HTTP 400.
* Vytvořili jste mapování DNS pro vlastní doménu na hostitele front-endu, který jste nakonfigurovali. Odeslání požadavku na název hostitele vlastní domény ale vrátí stavový kód HTTP 400. Zdá se, že není směrován do back-endu, který jste nakonfigurovali.

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud jste nenakonfigurovali pravidlo směrování pro vlastní doménu, která byla přidána jako hostitel s front-endu. Pravidlo směrování se musí explicitně přidat pro tohoto hostitele front-endu. To platí i v případě, že pravidlo směrování již bylo konfigurováno pro hostitele front-end v rámci subdomény služby Azure front-endu (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Přidejte pravidlo směrování pro vlastní doménu pro směrování provozu do vybrané skupiny zdrojů.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Přední dvířka Azure nepřesměrují protokol HTTP na HTTPS

### <a name="symptom"></a>Příznak

Přední dveře Azure mají pravidlo směrování, které přesměruje HTTP na HTTPS, ale přístup k doméně stále udržuje protokol HTTP jako protokol.

### <a name="cause"></a>Příčina

K tomuto chování může dojít, pokud jste pravidla směrování nenakonfigurovali správně pro přední dveře Azure. V podstatě vaše aktuální konfigurace není specifická a může mít konfliktní pravidla.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Požadavek na název hostitele front-end vrátí stavový kód 411.

### <a name="symptom"></a>Příznak

Vytvořili jste instanci služby Azure front-endu Standard/Premium a nakonfigurovali hostitele s front-end, skupinu původu s alespoň jedním zdrojem v ní a pravidlo směrování, které připojí hostitele front-end ke skupině původu. Váš obsah nebude pravděpodobně k dispozici, když požadavek přejde do nakonfigurovaného hostitele front-endu, protože se vrátí stavový kód HTTP 411.

Odpovědi na tyto požadavky mohou také obsahovat chybovou stránku HTML v těle odpovědi, která obsahuje vysvětlující příkaz. Příklad: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Příčina

U tohoto příznaku existuje několik možných příčin. Celkovým důvodem je to, že váš požadavek HTTP není plně kompatibilní se specifikací RFC. 

Příkladem nedodržení předpisů je `POST` požadavek odeslaný bez `Content-Length` `Transfer-Encoding` hlavičky (například pomocí `curl -X POST https://example-front-door.domain.com` ). Tato žádost nesplňuje požadavky uvedené v [dokumentu RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Přední dvířka Azure ji zablokují pomocí odpovědi HTTP 411.

Toto chování je nezávislé na funkci firewallu webových aplikací (WAF) služby Azure front-dveří. V současné době neexistuje žádný způsob, jak toto chování zakázat. Všechny požadavky HTTP musí splňovat požadavky, a to i v případě, že se funkce WAF nepoužívá.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

- Ověřte, že požadavky jsou v souladu s požadavky uvedenými v nezbytných dokumentech RFC.

- Poznamenejte si text zprávy HTML, který se vrátí v reakci na vaši žádost. Tělo zprávy často přesně vysvětluje, *jak* vaše žádost nedodržuje předpisy.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [vytvořit frontu Standard/Premium](create-front-door-portal.md).
