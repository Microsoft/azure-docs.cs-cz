---
title: Řešení potíží s konfigurací front-dveří Azure
description: V tomto kurzu se naučíte, jak sami řešit některé běžné problémy, které se dají pro vaše přední dveře vytvářet.
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
ms.openlocfilehash: 8e810a31fab4457e47329e37f54b16e6f488c9da
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337623"
---
# <a name="troubleshooting-common-routing-issues"></a>Řešení běžných potíží se směrováním

Tento článek popisuje, jak řešit některé běžné problémy s směrováním, které můžete mít pro konfiguraci front-výstupů Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 reakce z předních dveří po několika sekundách

### <a name="symptom"></a>Příznak

* Pravidelné požadavky odeslané do back-endu bez průchodu před předními dveřmi jsou úspěšné, ale přes přední dveře mají za následek 503 chybových odpovědí.
* Selhání z front-dveří se zobrazí po několika sekundách (obvykle přibližně po 30 sekundách).

### <a name="cause"></a>Příčina

Příčinou tohoto problému může být jedna z následujících dvou věcí:
 
* Vaše back-end trvá déle, než je nakonfigurovaný časový limit (výchozí hodnota je 30 sekund), aby se žádost dostala z přední dveře.
* Doba potřebná k odeslání odpovědi na požadavek z front-dveří trvá déle než hodnota časového limitu. 

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

* Odešlete žádost přímo do back-endu (bez přechodu přes přední vrátka) a podívejte se na to, jaká je obvyklá doba, jakou má váš back-end reagovat.
* Poslat požadavek přes dvířka a zjistit, jestli se vám nedaří získat odpovědi 503. V takovém případě se nemusí jednat o problém s časovým limitem. Obraťte se na podporu.
* Pokud projdete předními dvířky, dojde k chybě 503 kódu odpovědi na chybu a pak nakonfigurujte `sendReceiveTimeout` pole pro vaše přední dveře. Výchozí časový limit můžete zvětšit na 4 minuty (240 sekund). Nastavení je `backendPoolSettings` zavolána v a `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Požadavky odeslané do vlastní domény vrátí stavový kód 400.

### <a name="symptom"></a>Příznak

* Vytvořili jste frontu, ale požadavek na hostitele domény nebo front-end vrací kód stavu HTTP 400.
* Vytvořili jste mapování DNS pro vlastní doménu na hostitele front-endu, který jste nakonfigurovali. Odeslání požadavku na název hostitele vlastní domény ale vrátí stavový kód HTTP 400. Nezobrazuje se směrování na back-end, který jste nakonfigurovali.

### <a name="cause"></a>Příčina

K tomuto problému dochází, pokud jste nenakonfigurovali pravidlo směrování pro vlastní doménu, která byla přidána jako hostitel s front-endu. Pravidlo směrování se musí explicitně přidat pro tohoto hostitele front-endu. I v případě, že již byl nakonfigurován pro hostitele front-end v rámci subdomény front-endu (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Přidejte pravidlo směrování pro vlastní doménu pro směrování provozu do vybraného back-end fondu.

## <a name="front-door-doesnt-redirect-http-to-https"></a>Přední dvířka nepřesměrují protokol HTTP na HTTPS.

### <a name="symptom"></a>Příznak

Vaše přední dveře obsahují pravidlo směrování, které říká přesměrování HTTP na HTTPS, ale přístup k doméně stále udržuje protokol HTTP jako protokol.

### <a name="cause"></a>Příčina

K tomuto chování může dojít, pokud jste pravidla směrování nenakonfigurovali správně pro vaše přední dveře. V podstatě vaše aktuální konfigurace není specifická a může mít konfliktní pravidla.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Požadavek na název hostitele front-endu vrátí stavový kód 404.

### <a name="symptom"></a>Příznak

 Vytvořili jste přední dveře konfigurací hostitele front-endu, back-end fondu s alespoň jedním back-endu v něm a pravidlem směrování, které připojí hostitele front-end k back-endovému fondu. Váš obsah není k dispozici, když vytvoříte požadavek na nakonfigurovaného hostitele front-endu v důsledku vrácení kódu stavu HTTP 404.

### <a name="cause"></a>Příčina

U tohoto příznaku existuje několik možných příčin:

* Back-end není veřejným přístupem k back-endu a není viditelný pro přední dveře.
* Back-end je nesprávně nakonfigurovaný, protože přední dvířka posílá špatný požadavek. Jinými slovy, váš back-end přijímá pouze HTTP a nejste nezkontrolovali povolení HTTPS. Takže se přední dvířka pokoušejí přeslat požadavky HTTPS.
* Back-end odmítá hlavičku hostitele předanou žádostí do back-endu.
* Konfigurace pro back-end ještě není plně nasazená.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

1. Čas nasazení
   * Ujistěte se, že jste čekali na nasazení konfigurace za 10 minut.

2. Kontrolovat nastavení back-endu
    * Přejděte do fondu back-end, do kterého se má žádost směrovat (záleží na tom, jak máte pravidlo směrování nakonfigurované). Ověřte, že *typ hostitele back-endu* a název hostitele back-endu jsou správné. Pokud je back-end vlastním hostitelem, ujistěte se, že je správně napsaný. 

    * Ověřte porty HTTP a HTTPS. Ve většině případů jsou 80 a 443 (v uvedeném pořadí) správné a nevyžadují se žádné změny. Existuje však možnost, že back-end není nakonfigurován tímto způsobem a naslouchá na jiném portu.

        * Ověřte _hlavičku hostitele back-end_ nakonfigurovanou pro back-endy, do kterých se má hostitel front-endu směrovat. Ve většině případů by tato hlavička měla být stejná jako *název hostitele back-endu*. Nesprávná hodnota ale může způsobit různé stavové kódy 4xx HTTP, pokud back-end očekává něco jiného. Pokud zadáte IP adresu vašeho back-endu, možná budete muset nastavit *hlavičku hostitele back-end* na název hostitele back-endu.

3. Ověřte nastavení pravidla směrování:
    * Přejděte do pravidla směrování, které by mělo směrovat z daného názvu hostitele front-endu do back-endového fondu. Zajistěte, aby byly přijaté protokoly správně nakonfigurované při předávání žádosti. Pole *přijaté protokoly* určuje, které požadavky musí přední dveře přijmout. *Protokol předávání* určuje, jaké přední dveře protokolu mají použít pro předání požadavku do back-endu.
         * Příklad: Pokud back-end akceptuje pouze požadavky HTTP, budou platné následující konfigurace:
            * *Přijaté protokoly* jsou HTTP a HTTPS. *Protokol předávání* je http. Požadavek shody nebude fungovat, protože HTTPS je povolený protokol, a pokud požadavek nacházel jako HTTPS, přední dveře se pokusí o přeposlání pomocí protokolu HTTPS.

            * *Přijaté protokoly* jsou http. *Protokol předávání* buď odpovídá požadavku, nebo http.
    - *Přepsání adresy URL* je ve výchozím nastavení zakázáno. Toto pole se používá pouze v případě, že chcete zúžit rozsah prostředků hostovaných v hostovaném prostředí, které mají být k dispozici. Pokud je tato akce zakázána, předá přední dveře stejnou cestu požadavku, kterou obdrží. Toto pole je možné nakonfigurovat nesprávně. Takže pokud přední dveře požaduje prostředek z back-endu, který není dostupný, vrátí stavový kód HTTP 404.

## <a name="request-to-frontend-host-name-returns-411-status-code"></a>Požadavek na front-end název hostitele vrátí stavový kód 411.

### <a name="symptom"></a>Příznak

Vytvořili jste přední dvířka a nakonfigurovali hostitele front-endu, back-end fond s alespoň jedním back-end serverem a pravidlo směrování, které připojuje hostitele front-end k back-endovému fondu. Váš obsah nemusí být k dispozici při odesílání žádosti do nakonfigurovaného hostitele front-endu, protože je vrácen stavový kód HTTP 411.

Odpovědi na tyto požadavky mohou také obsahovat chybovou stránku HTML v těle odpovědi, která obsahuje vysvětlující příkaz. Příklad: `HTTP Error 411. The request must be chunked or have a content length`

### <a name="cause"></a>Příčina

U tohoto příznaku existuje několik možných příčin; Celkový důvod je však, že váš požadavek HTTP není plně kompatibilní se specifikací RFC. 

Příkladem nedodržení předpisů je `POST` požadavek odeslaný bez `Content-Length` `Transfer-Encoding` hlavičky (například pomocí `curl -X POST https://example-front-door.domain.com` ). Tato žádost nesplňuje požadavky uvedené v [dokumentu RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2) a vaše přední dvířka zablokovala odpověď HTTP 411.

Toto chování je oddělené od WAF funkcí front-dveří. V současné době neexistuje žádný způsob, jak toto chování zakázat. Všechny požadavky HTTP musí splňovat požadavky, a to i v případě, že se funkce WAF nepoužívá.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

- Ověřte, že požadavky jsou v dodržování požadavků stanovených v nezbytných dokumentech RFC.

- Poznamenejte si text zprávy HTML, který se vrátí v reakci na vaši žádost, protože často vysvětluje přesně to, *jak* vaše žádost nedodržuje předpisy.
