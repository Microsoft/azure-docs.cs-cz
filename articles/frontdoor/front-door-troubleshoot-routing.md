---
title: Řešení problémů s konfigurací Azure Front Door
description: V tomto kurzu se dozvíte, jak samostatně řešit některé běžné problémy, kterým můžete čelit pro vaše přední dveře.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: 962c884eb8adc05e5d50b6b254d5c3f0b18af556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471502"
---
# <a name="troubleshooting-common-routing-issues"></a>Poradce při potížích s běžným směrováním

Tento článek popisuje, jak řešit některé běžné problémy směrování, kterým můžete čelit pro konfiguraci Azure Front Door.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 odezva od předních dveří po několika sekundách

### <a name="symptom"></a>Příznak

- Pravidelné požadavky odeslané do backendu, aniž by procházely předními dveřmi, jsou úspěšné, ale procházení frontových dveří vede k 503 chybovým odpovědím.

- Porucha z předních dveří se zobrazí po několika sekundách (obvykle po 30 sekundách)

### <a name="cause"></a>Příčina

Tento příznak se stane, když buď back-end trvá nad časový limit konfigurace (výchozí je 30 sekund) přijímat požadavek z předních dveří nebo pokud trvá déle než tuto hodnotu časového limitu odeslat odpověď na požadavek z front door. 

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

- Pošlete žádost přímo do back-endu (bez průchodu předními dveřmi) a zjistěte, jaký je obvyklý čas, který trvá, než váš backend odpoví.
- Pošlete žádost přes přední dveře a uvidíme, jestli vidíte nějaké 503 odpovědí. Pokud ne, pak to nemusí být problém s časovým časem. Obraťte se na podporu.
- Pokud procházíte front door, výsledkem je kód odpovědi na chybu 503, nakonfigurujte pole sendReceiveTimeout pro přední dveře, abyste prodloužili výchozí časový limit až na 4 minuty (240 sekund). Nastavení je pod `backendPoolSettings` a `sendRecvTimeoutSeconds`je volána . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Požadavky odeslané do vlastní domény vrátí 400 Stavový kód

### <a name="symptom"></a>Příznak

- Vytvořili jste front door, ale požadavek na doménu nebo hostitele front-endu vrací stavový kód HTTP 400.

- Vytvořili jste mapování DNS z vlastní domény na front-endového hostitele, který jste nakonfigurovali. Odeslání požadavku na vlastní název_hostitele domény však vrátí stavový kód HTTP 400 a nezobrazí se směrovat do back-endu, které jste nakonfigurovali.

### <a name="cause"></a>Příčina

K tomuto příznaku může dojít, pokud jste nenakonfigurovali pravidlo směrování pro vlastní doménu, kterou jste přidali jako front-endového hostitele. Pravidlo směrování musí být explicitně přidáno pro tohoto hostitele front-endu, i když již bylo nakonfigurováno pro front-endového hostitele pod subdoménou Front Door (*.azurefd.net), na kterou má vaše vlastní doména mapování DNS.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

Přidejte pravidlo směrování z vlastní domény do požadovaného back-endového fondu.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Přední dveře nepřesměrovává HTTP na HTTPS

### <a name="symptom"></a>Příznak

Přední dveře mají pravidlo směrování, které říká přesměrování HTTP na protokol HTTPS, ale přístup k doméně stále udržuje protokol HTTP jako protokol.

### <a name="cause"></a>Příčina

K tomuto chování může dojít, pokud jste správně nenakonfigurovali pravidla směrování pro přední dveře. V podstatě vaše aktuální konfigurace není specifická a může mít konfliktní pravidla.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Požadavek na název hostitele front-endvrátí stavový kód 404.

### <a name="symptom"></a>Příznak

- Vytvořili jste frontdoor a nakonfigurovali front-endového hostitele, back-endový fond s alespoň jedním back-endem a pravidlo směrování, které spojuje front-endového hostitele s back-endovým fondem. Zdá se, že váš obsah není k dispozici při odesílání požadavku na konfigurovanému hostiteli front-endu, protože je vrácen stavový kód HTTP 404.

### <a name="cause"></a>Příčina

Existuje několik možných příčin tohoto příznaku:

- Back-end není veřejný back-end a není viditelný pro přední dveře.
- Back-end je nesprávně nakonfigurován, což způsobuje, že přední dveře odešlou nesprávný požadavek (to znamená, že back-end přijímá pouze protokol HTTP, ale nezrušili jste povolení protokolu HTTPS, takže front door se pokouší předat požadavky HTTPS).
- Back-end odmítá hlavičku hostitele, která byla předána s požadavkem back-endu.
- Konfigurace pro back-end ještě nebyla plně nasazena.

### <a name="troubleshooting-steps"></a>Postup při řešení potíží

1. Čas nasazení
   - Ujistěte se, že jste čekali ~10 minut na nasazení konfigurace.

2. Kontrola nastavení back-endu
    - Přejděte do back-endového fondu, do kterého by měl být požadavek směrován (závisí na tom, jak máte nakonfigurované pravidlo směrování) a ověřte, zda je _typ back-endového a_ back-endového hostitele správný. Pokud back-end je vlastní hostitel, ujistěte se, že jste jej napsali správně. 

    - Zkontrolujte porty HTTP a HTTPS. Ve většině případů 80 a 443 (v uvedeném pořadí) jsou správné a žádné změny budou vyžadovány. Existuje však možnost, že back-end není nakonfigurován tímto způsobem a naslouchá na jiném portu.

        - Zkontrolujte _hlavičku hostitele Back-end_ nakonfigurované pro back-endy, do kterých by měl být směrován hostitel front-end. Ve většině případů by toto záhlaví mělo být stejné jako _název hostitele Back-endu_. Nesprávná hodnota však může způsobit různé kódy stavu HTTP 4xx, pokud back-end očekává něco jiného. Pokud zadáte IP adresu back-endu, možná budete muset nastavit _hlavičku hostitele Back-end_ na název hostitele back-endu.


3. Kontrola nastavení pravidla směrování
    - Přejděte na pravidlo směrování, které by mělo směrovat z názvu hostitele front-endu do fondu back-endu. Ujistěte se, že přijaté protokoly jsou správně nakonfigurovány, nebo pokud ne, ujistěte se, že protokol Front Door bude používat při předávání požadavku je správně nakonfigurován. Pole _přijaté protokoly_ určuje, které požadavky by měly přijmout front door, a _protokol předávání_ určuje, jaký protokol by měl front door použít k předání požadavku back-endu.
         - Například pokud back-end přijímá pouze požadavky HTTP, budou platné následující konfigurace:
            - _Přijaté protokoly_ jsou HTTP a HTTPS. _Protokol předávání_ je http. Požadavek na shodu nebude fungovat, protože protokol HTTPS je povolený protokol a pokud je požadavek přišel jako HTTPS, front door by se ho pokusil předat dál pomocí protokolu HTTPS.

            - _Přijaté protokoly_ jsou HTTP. _Protokol předávání_ je buď požadavek na shodu, nebo protokol HTTPS.

    - _Přepis adresy URL_ je ve výchozím nastavení zakázán a toto pole byste měli použít pouze v případě, že chcete zúžit rozsah prostředků hostovaných v back-endu, které chcete zpřístupnit. Pokud je zakázáno, front door předá stejnou cestu požadavku, kterou obdrží. Je možné, že toto pole je nesprávně nakonfigurována a front door požaduje prostředek z back-endu, který není k dispozici, a tedy vrácení stavový kód HTTP 404.

