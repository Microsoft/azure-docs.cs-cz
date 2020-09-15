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
ms.date: 09/22/2018
ms.author: duau
ms.openlocfilehash: babe24d0c934cffac00a5100d1da7ee252d147da
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399051"
---
# <a name="troubleshooting-common-routing-issues"></a>Řešení běžných potíží se směrováním

Tento článek popisuje, jak řešit některé běžné problémy s směrováním, které můžete mít pro konfiguraci front-výstupů Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 reakce z předních dveří po několika sekundách

### <a name="symptom"></a>Příznak

- Pravidelné požadavky odeslané do back-endu bez průchodu před předními dveřmi jsou úspěšné, ale přes přední dveře mají za následek 503 chybových odpovědí.

- Selhání z front-dveří se zobrazí po několika sekundách (obvykle přibližně po 30 sekundách).

### <a name="cause"></a>Příčina

K tomuto problému dochází, když back-end trvá déle než konfigurace časového limitu (výchozí hodnota je 30 sekund), aby se žádost dostala z front-endu, nebo pokud tato hodnota časového limitu překročí k odeslání odpovědi na požadavek z předních dveří. 

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

- Odešlete žádost přímo do back-endu (bez přechodu přes přední vrátka) a podívejte se na to, jaká je obvyklá doba, jakou má váš back-end reagovat.
- Poslat požadavek přes dvířka a zjistit, jestli se vám zobrazují odpovědi 503. Pokud ne, může se jednat o problém s vypršením časového limitu. Obraťte se prosím na podporu.
- Pokud provedete přes přední dvířka, výsledkem bude 503 kód chyby odezvy a potom nakonfigurovat pole sendReceiveTimeout pro vaše přední dveře, aby se výchozí časový limit rozšířil na 4 minuty (240 sekund). Nastavení je `backendPoolSettings` zavolána v a `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Požadavky odeslané do vlastní domény vrátí stavový kód 400.

### <a name="symptom"></a>Příznak

- Vytvořili jste frontu, ale požadavek na hostitele domény nebo front-end vrací kód stavu HTTP 400.

- Vytvořili jste mapování DNS z vlastní domény na hostitele front-endu, kterého jste nakonfigurovali. Odesláním žádosti o název hostitele vlastní domény ale vrátí stavový kód HTTP 400 a nezobrazuje se směrování na back-endové servery, které jste nakonfigurovali.

### <a name="cause"></a>Příčina

K tomuto problému může dojít, pokud jste nenakonfigurovali pravidlo směrování pro vlastní doménu, kterou jste přidali jako hostitele s front-endu. Pravidlo směrování se musí explicitně přidat pro tohoto hostitele front-endu, a to i v případě, že už je nakonfigurovaný pro hostitele front-end v rámci subdomény front-endu (*. azurefd.net), na kterou má vaše vlastní doména mapování DNS.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Přidejte pravidlo směrování z vlastní domény do požadovaného back-end fondu.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Přední dvířka přesměrují HTTP na HTTPS.

### <a name="symptom"></a>Příznak

Vaše přední dveře obsahují pravidlo směrování, které říká přesměrování HTTP na HTTPS, ale přístup k doméně stále udržuje protokol HTTP jako protokol.

### <a name="cause"></a>Příčina

K tomuto chování může dojít, pokud jste nenakonfigurovali správně pravidla směrování pro vaše přední dveře. V podstatě vaše aktuální konfigurace není specifická a může mít konfliktní pravidla.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Požadavek na název hostitele front-endu vrátí stavový kód 404.

### <a name="symptom"></a>Příznak

- Vytvořili jste přední dvířka a nakonfigurovali hostitele front-endu, back-end fond s alespoň jedním back-end serverem a pravidlo směrování, které připojuje hostitele front-end k back-endovému fondu. Váš obsah nemusí být k dispozici při odesílání žádosti do nakonfigurovaného hostitele front-endu, protože je vrácen stavový kód HTTP 404.

### <a name="cause"></a>Příčina

U tohoto příznaku existuje několik možných příčin:

- Back-end není veřejným přístupem k back-endu a není viditelný pro přední dveře.
- Back-end je nesprávně nakonfigurovaný, což způsobuje, že přední dveře pošle nesprávný požadavek (to znamená, že back-end přijímá jenom protokol HTTP, ale nejste u něj nekontrolované, aby se při pokusu o přeposílání požadavků HTTPS předaly i přední dveře.
- Back-end odmítá hlavičku hostitele předanou žádostí do back-endu.
- Konfigurace pro back-end ještě není plně nasazená.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

1. Čas nasazení
   - Před nasazením konfigurace se ujistěte, že jste čekali přibližně 10 minut.

2. Kontrolovat nastavení back-endu
    - Přejděte do fondu back-end, do kterého se má žádost směrovat (závisí na tom, jak máte nakonfigurované pravidlo směrování), a ověřte, že _typ hostitele back-endu_ a název hostitele back-endu jsou správné. Pokud je back-end vlastním hostitelem, ujistěte se, že je správně napsaný. 

    - Ověřte porty HTTP a HTTPS. Ve většině případů jsou 80 a 443 (v uvedeném pořadí) správné a nevyžadují se žádné změny. Existuje však možnost, že back-end není nakonfigurován tímto způsobem a naslouchá na jiném portu.

        - Ověřte _hlavičku hostitele back-end_ nakonfigurovanou pro back-endy, do kterých se má hostitel front-endu směrovat. Ve většině případů by tato hlavička měla být stejná jako _název hostitele back-endu_. Nesprávná hodnota ale může způsobit různé stavové kódy 4xx HTTP, pokud back-end očekává něco jiného. Pokud zadáte IP adresu vašeho back-endu, možná budete muset nastavit _hlavičku hostitele back-end_ na název hostitele back-endu.


3. Ověřte nastavení pravidla směrování.
    - Přejděte do pravidla směrování, které by mělo směrovat z daného názvu hostitele front-endu do back-endového fondu. Zajistěte, aby byly přijaté protokoly správně nakonfigurované, nebo pokud ne, zajistěte, aby se při předávání žádosti správně nakonfigurovala Tato dvířka protokolu. Pole _přijaté protokoly_ určuje, které požadavky musí přední dveře přijmout, a _protokol předávání_ určuje, jaké přední dveře protokolu mají použít pro předání žádosti do back-endu.
         - Příklad: Pokud back-end akceptuje pouze požadavky HTTP, budou platné následující konfigurace:
            - _Přijaté protokoly_ jsou HTTP a HTTPS. _Protokol předávání_ je http. Požadavek shody nebude fungovat, protože HTTPS je povolený protokol, a pokud požadavek přišel jako HTTPS, přední dveře se pokusí o přeposlání pomocí protokolu HTTPS.

            - _Přijaté protokoly_ jsou http. _Protokol předávání_ buď odpovídá požadavku, nebo http.

    - _Přepsání adresy URL_ je ve výchozím nastavení zakázáno a toto pole byste měli použít pouze v případě, že chcete zúžit rozsah prostředků hostovaných hostovaným serverem, které mají být k dispozici. Pokud je tato akce zakázána, předá přední dveře stejnou cestu požadavku, kterou obdrží. Je možné, že toto pole je špatně nakonfigurované a přední dveře vyžadují prostředek z back-endu, který není k dispozici, a vrátí stavový kód HTTP 404.

