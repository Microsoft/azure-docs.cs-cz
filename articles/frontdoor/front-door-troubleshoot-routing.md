---
title: Řešení problémů s konfigurací front-dveří Azure
description: V tomto kurzu se naučíte, jak sami řešit některé běžné problémy, které se můžou stát pro instanci front-výstupů Azure.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629984"
---
# <a name="troubleshooting-common-routing-problems"></a>Řešení běžných potíží se směrováním

Tento článek popisuje, jak řešit běžné problémy se směrováním, na které se můžete setkat při konfiguraci front-výstupů Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 reakce z front Azure na přední dveře po několika sekundách

### <a name="symptom"></a>Příznak

* Pravidelné požadavky odeslané do back-endu bez přechodu přes přední dveře Azure jsou úspěšné. Při přechodu přes službu Azure front-restarts dojde k chybovým odpovědím 503.
* Selhání z front Azure na předních dveřích se většinou zobrazuje přibližně 30 sekund.

### <a name="cause"></a>Příčina

Příčinou tohoto problému může být jedna z následujících dvou věcí:
 
* Vaše back-end trvá déle, než je nakonfigurovaný časový limit (výchozí hodnota je 30 sekund), aby se žádost dostala z front-endu Azure.
* Doba potřebná k odeslání odpovědi na žádost z front Azure na začátku trvá déle než hodnota časového limitu. 

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

* Odešlete žádost přímo do back-endu (bez přechodu přes přední vrátka Azure). Podívejte se, jak dlouho vaše back-end obvykle trvá reagovat.
* Odešlete žádost přes dvířka Azure a podívejte se, jestli se vám zobrazují odpovědi 503. V takovém případě se nemusí jednat o problém s vypršením časového limitu. Obraťte se na podporu.
* Pokud projdete předními dvířky Azure, dojde k chybě v kódu chyby 503, nakonfigurujte `sendReceiveTimeout` pole pro přední dveře Azure. Výchozí časový limit můžete zvětšit na 4 minuty (240 sekund). Nastavení je v rámci `backendPoolSettings` a voláno `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Požadavky odeslané do vlastní domény vrátí stavový kód 400.

### <a name="symptom"></a>Příznak

* Vytvořili jste instanci front-endu Azure, ale požadavek na hostitele domény nebo front-end vrací kód stavu HTTP 400.
* Vytvořili jste mapování DNS pro vlastní doménu na hostitele front-endu, který jste nakonfigurovali. Odeslání požadavku na název hostitele vlastní domény ale vrátí stavový kód HTTP 400. Zdá se, že není směrován do back-endu, který jste nakonfigurovali.

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud jste nenakonfigurovali pravidlo směrování pro vlastní doménu, která byla přidána jako hostitel s front-endu. Pravidlo směrování se musí explicitně přidat pro tohoto hostitele front-endu. To platí i v případě, že pravidlo směrování již bylo konfigurováno pro hostitele front-end v rámci subdomény služby Azure front-endu (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Přidejte pravidlo směrování pro vlastní doménu pro směrování provozu do vybraného back-end fondu.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Přední dvířka Azure nepřesměrují protokol HTTP na HTTPS

### <a name="symptom"></a>Příznak

Přední dveře Azure mají pravidlo směrování, které přesměruje HTTP na HTTPS, ale přístup k doméně stále udržuje protokol HTTP jako protokol.

### <a name="cause"></a>Příčina

K tomuto chování může dojít, pokud jste pravidla směrování nenakonfigurovali správně pro přední dveře Azure. V podstatě vaše aktuální konfigurace není specifická a může mít konfliktní pravidla.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Požadavek na název hostitele front-end vrátí stavový kód 404.

### <a name="symptom"></a>Příznak

Vytvořili jste instanci front-endu Azure nakonfigurovaného hostitele s front-end, back-end fondem s alespoň jedním back-endu a směrovací pravidlo, které připojí hostitele front-end k back-endu. Váš obsah není k dispozici, když vytvoříte požadavek na nakonfigurovaného hostitele front-endu. V důsledku toho požadavek vrátí stavový kód HTTP 404.

### <a name="cause"></a>Příčina

U tohoto příznaku existuje několik možných příčin:

* Back-end není veřejným back-endu a není viditelný pro služby Azure front-endu.
* Back-end je nesprávně nakonfigurovaný a způsobuje, že přední dvířka Azure odesílají špatný požadavek. Jinými slovy, váš back-end přijímá jenom HTTP a nepovolili jste HTTPS. Takže se přední dveře Azure pokoušejí přeslat požadavky HTTPS.
* Back-end odmítá hlavičku hostitele předanou žádostí do back-endu.
* Konfigurace pro back-end ještě není plně nasazená.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

* Ověřte dobu nasazení:
   * Ujistěte se, že jste před nasazením konfigurace čekali alespoň na 10 minut.

* Podívejte se na nastavení back-endu:
    * Přejít do fondu back-end, do kterého se má žádost směrovat (Záleží na tom, jak máte pravidlo směrování nakonfigurované.) Ověřte, že typ hostitele back-endu a název hostitele back-endu jsou správné. Pokud je back-end vlastním hostitelem, ujistěte se, že je správně napsaný. 

    * Ověřte porty HTTP a HTTPS. Ve většině případů jsou 80 a 443 (v uvedeném pořadí) správné a nevyžadují se žádné změny. Existuje však možnost, že back-end není nakonfigurován tímto způsobem a naslouchá na jiném portu.

    * Ověřte hlavičku hostitele back-end nakonfigurovanou pro back-endy, do kterých se má hostitel front-endu směrovat. Ve většině případů by tato hlavička měla být stejná jako název hostitele back-endu. Nesprávná hodnota ale může způsobit různé stavové kódy 4xx HTTP, pokud back-end očekává něco jiného. Pokud zadáte IP adresu vašeho back-endu, možná budete muset nastavit hlavičku hostitele back-end na název hostitele back-endu.

* Ověřte nastavení pravidla směrování:
    * Přejít na pravidlo směrování, které by se mělo směrovat z daného hostitele s front-end serverem do back-endového fondu. Ujistěte se, že přijaté protokoly jsou správně nakonfigurovány, když je požadavek předán. V poli **přijaté protokoly** se určí, které požadavky musí přední dveře Azure přijmout. Protokol předávání určuje, který protokol Azure na frontě by se měl použít pro předání požadavku do back-endu.
      
      Pokud například back-end přijímá pouze požadavky HTTP, následující konfigurace budou platné:
            
      * Přijaté protokoly jsou HTTP a HTTPS. Protokol předávání je HTTP. Požadavek shody nebude fungovat, protože HTTPS je povolený protokol. Pokud se žádost nacházela jako HTTPS, přední dvířka Azure se pokusí o přeposlání pomocí protokolu HTTPS.

      * Přijatý protokol je HTTP. Předávací protokol je buď žádost o shodu, nebo HTTP.
    - **Přepsání adresy URL** je ve výchozím nastavení zakázáno. Toto pole se používá pouze v případě, že chcete zúžit rozsah prostředků hostovaných v hostovaném prostředí, které mají být k dispozici. Když je toto pole zakázané, bude přední dvířka Azure přesměrovat stejnou cestu požadavku, kterou obdrží. Toto pole je možné nakonfigurovat nesprávně. Takže pokud přední vrátka Azure vyžaduje prostředek z back-endu, který není dostupný, vrátí stavový kód HTTP 404.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Požadavek na název hostitele front-end vrátí stavový kód 411.

### <a name="symptom"></a>Příznak

Vytvořili jste instanci front-endu Azure a nakonfigurovali hostitele front-end, back-end fond s alespoň jedním back-endu v něm a pravidlo směrování, které připojí hostitele front-end k back-endovému fondu. Váš obsah nebude pravděpodobně k dispozici, když požadavek přejde do nakonfigurovaného hostitele front-endu, protože je vrácen stavový kód HTTP 411.

Odpovědi na tyto požadavky mohou také obsahovat chybovou stránku HTML v těle odpovědi, která obsahuje vysvětlující příkaz. Příklad: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Příčina

U tohoto příznaku existuje několik možných příčin. Celkovým důvodem je to, že požadavek HTTP není plně kompatibilní se specifikací RFC. 

Příkladem nedodržení předpisů je `POST` požadavek odeslaný bez `Content-Length` `Transfer-Encoding` hlavičky (například pomocí `curl -X POST https://example-front-door.domain.com` ). Tato žádost nesplňuje požadavky uvedené v [dokumentu RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Přední dvířka Azure ji zablokují pomocí odpovědi HTTP 411.

Toto chování je nezávislé na funkci firewallu webových aplikací (WAF) služby Azure front-dveří. V současné době neexistuje žádný způsob, jak toto chování zakázat. Všechny požadavky HTTP musí splňovat požadavky, a to i v případě, že se funkce WAF nepoužívá.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

- Ověřte, že požadavky jsou v dodržování požadavků stanovených v nezbytných dokumentech RFC.

- Poznamenejte si text zprávy HTML, který se vrátí v reakci na vaši žádost. Tělo zprávy často přesně vysvětluje, *jak* vaše žádost nedodržuje předpisy.
