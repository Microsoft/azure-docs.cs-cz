---
title: Hlavičky HTTP X-ES-Debug pro stroj pravidel Azure CDN | Microsoft Docs
description: Hlavička X-ES-Debug ladění mezipaměti požadavku poskytuje další informace o mezipaměti zásad, které se použijí pro požadovaný prostředek. Jsou tyto hlavičky specifické pro Verizon.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: v-deasim
ms.openlocfilehash: 3a99e322d81748c54585e7dd0eb06959bfeb9569
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31517433"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Hlavičky HTTP X-ES-Debug pro stroj pravidel Azure CDN
Hlavička požadavku mezipaměti ladění, `X-EC-Debug`, poskytuje další informace o mezipaměti zásad, které se použijí pro požadovaný prostředek. Jsou tyto hlavičky specifické pro **Azure CDN Premium od společnosti Verizon** produkty.

## <a name="usage"></a>Využití
Odpověď odeslaná ze serverů POP uživateli obsahuje `X-EC-Debug` záhlaví jenom v případě, že jsou splněny následující podmínky:

- [Funkce ladění hlavičky odpovědi mezipaměti](cdn-rules-engine-reference-features.md#debug-cache-response-headers) byla zapnuta stroj pravidel pro zadaný požadavek.
- Zadaný požadavek definuje sadu hlavičky odpovědi mezipaměti ladění, které budou zahrnuty v odpovědi.

## <a name="requesting-debug-cache-information"></a>Vyžadování informací o mezipaměti ladění
Použijte následující direktivy v zadaný požadavek definovat informace o mezipaměti ladění, který bude zahrnut v odpovědi:

Hlavička požadavku | Popis |
---------------|-------------|
X-ES Debug: x ES mezipaměti | [Mezipaměť stavový kód](#cache-status-code-information)
X-ES Debug: x ES mezipaměti vzdálené | [Mezipaměť stavový kód](#cache-status-code-information)
X-ES Debug: x ES kontrola-lze uložit do mezipaměti | [Lze uložit do mezipaměti](#cacheable-response-header)
X-ES Debug: x ES mezipaměti key | [Klíč mezipaměti](#cache-key-response-header)
X-ES Debug: x ES mezipaměti – stav | [Stav mezipaměti](#cache-state-response-header)

### <a name="syntax"></a>Syntaxe

Ladění odpověď mezipaměti, které mohou být vyžádány hlavičky zahrnutím následující hlavičku a direktivy zadaný v požadavku:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Ukázka X-ES-Debug záhlaví

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informace o kódu stavu mezipaměti
Hlavička odpovědi X-ES-Debug můžete identifikovat server a jak ji zpracovat odpověď prostřednictvím následující direktivy:

Záhlaví | Popis
-------|------------
X-ES Debug: x ES mezipaměti | Tuto hlavičku údajně vždy, když obsah je směrován přes CDN. Identifikuje server POP, která požadavek splnila.
X-ES Debug: x ES mezipaměti vzdálené | Tuto hlavičku se hlásí jenom v případě, že požadovaný obsah se ukládá do mezipaměti na serveru štítu původ nebo server brány ADN.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

Hlavička X-ES-Debug hlásí informace o stavu kódu v mezipaměti v následujícím formátu:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Termínů používaných v syntaxi výše uvedené hlavičky odpovědi jsou definovány takto:
- StatusCode: Určuje, jak je požadovaný obsah zajišťoval CDN, která je reprezentována pomocí mezipaměti stavový kód.
    
    Stavový kód TCP_DENIED může místo žádné hlášena, když bude neoprávněného požadavku byl odepřen v důsledku ověřování na základě tokenu. Stavový kód NONE však bude i nadále používat při zobrazení zprávy o stavu mezipaměti nebo nezpracovaná data protokolu.

- Platforma: Označuje platformy, na kterém byl požadovaný obsah. Následující kódy jsou platné pro pole:

    Kód  | Platforma
    ------| --------
    Banky ECAcc | Velké HTTP
    ECS   | Malá HTTP
    ECD   | Application Delivery Network (ADN)

- POP: Označuje [POP](cdn-pop-abbreviations.md) který zpracovává žádosti. 

### <a name="sample-response-headers"></a>Ukázka hlavičky odpovědi

Následující ukázka hlavičky poskytují informace o kódu stavu mezipaměti pro žádost:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Hlavička odpovědi lze uložit do mezipaměti
`X-EC-Debug: x-ec-check-cacheable` Hlavičku odpovědi označuje, zda je požadovaný obsah může mít byl uložený do mezipaměti.

Tuto hlavičku odpovědi neindikuje, jestli ukládání do mezipaměti byla provedena. Místo toho se určuje, zda byla žádost vhodné pro ukládání do mezipaměti.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug` Hlavičku odpovědi reporting, zda žádost může mít byl uložený do mezipaměti je v následujícím formátu:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termín použitý v syntaxi výše uvedené hlavičky odpovědi je definován následujícím způsobem:

Hodnota  | Popis
-------| --------
ANO    | Znamená, že požadovaný obsah byla vhodné pro ukládání do mezipaměti.
NE     | Znamená, že byla požadovaný obsah není vhodná pro ukládání do mezipaměti. Tento stav může být způsobeno jedním z následujících důvodů: <br /> -Konfigurace zákaznické: konfigurace specifické pro váš účet můžete pop servery zabránit v ukládání do mezipaměti prostředek. Stroj pravidel můžete například zabránit prostředek ukládat do mezipaměti povolíte funkci mezipaměti jednorázové přihlášení pro určení požadavků.<br /> -Hlavičky odpovědi mezipaměti: Hlavičky Cache-Control a Expires požadovaný prostředek můžete zabránit servery POP z mezipaměti.
NEZNÁMÝ | Označuje, nemohli servery k vyhodnocení, zda požadovaný prostředek byl lze uložit do mezipaměti. Tento stav obvykle dochází, když požadavek byl odepřen v důsledku ověřování na základě tokenu.

### <a name="sample-response-header"></a>Ukázka hlavička odpovědi

Následující ukázka hlavičku odpovědi označuje, zda je požadovaný obsah může mít byl uložený do mezipaměti:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Hlavička odpovědi klíče mezipaměti
`X-EC-Debug: x-ec-cache-key` Hlavičku odpovědi označuje fyzické mezipaměti klíč přidružený požadovaný obsah. Fyzické klíče mezipaměti se skládá z cestu, která identifikuje prostředek pro účely ukládání do mezipaměti. Jinými slovy servery zkontroluje uložené v mezipaměti verzi prostředek podle cesty podle definice jeho klíče mezipaměti.

Tento fyzický klíče mezipaměti začíná dvojité lomítkem (/ /) následuje protokol použitý k požadavek na obsah (HTTP nebo HTTPS). Tento protokol je pro požadovaný prostředek, který začíná bodem přístup k obsahu a relativní cesty (například _/000001/_).

Ve výchozím nastavení, HTTP platformy nakonfigurovány pro používání *standard mezipaměti*, což znamená, že řetězce dotazu se ignorují mechanismem pro ukládání do mezipaměti. Tento typ konfigurace zabrání klíče mezipaměti včetně data řetězce dotazu.

Pokud řetězec dotazu se zaznamená do klíče mezipaměti, má být převeden na odpovídající hodnotu hash a potom vložen mezi název požadovaný prostředek a jeho příponu souboru (například asset&lt;hodnoty hash&gt;.html).

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug` Hlavičku odpovědi sestavy fyzické klíče mezipaměti informace v následujícím formátu:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Ukázka hlavička odpovědi

Následující ukázka hlavičku odpovědi označuje fyzické klíč mezipaměti pro požadovaný obsah:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Hlavička odpovědi stavu mezipaměti
`X-EC-Debug: x-ec-cache-state` Hlavičku odpovědi označuje stav mezipaměti požadovaného obsahu v době byla vyžádána.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug` Hlavičku odpovědi sestavy mezipaměti informace o stavu v následujícím formátu:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Termínů používaných v syntaxi výše uvedené hlavičky odpovědi jsou definovány takto:

- MASeconds: Určuje maximální stáří (v sekundách) podle definice požadovaný obsah hlavičky Cache-Control.

- MATimePeriod: Převede hodnotu maximální stáří (MASeconds) přibližný ekvivalent větší jednotky (například, dnů). 

- UnixTime: Určuje časové razítko mezipaměti požadovaného obsahu v čase systému Unix (také známa jako POSIX čas nebo Unix epoch). Časové razítko mezipaměti určuje počáteční datum a čas ze kterého se bude počítat TTL prostředek služby. 

    Pokud je zdrojový server nevyužívá třetích stran HTTP, ukládání do mezipaměti serveru nebo pokud tento server nevrací hlavičku odpovědi stáří, pak časové razítko mezipaměti bude vždy datum a čas, kdy byl asset načíst ani znovu ověřeny. Jinak hodnota servery POP pole stáří používá k výpočtu assetu TTL následujícím způsobem: načítání/RevalidateDateTime - stáří.

- ddd, dd MMMM rrrr hh: mm: GMT: Určuje časové razítko mezipaměti požadovaného obsahu. Další informace najdete v tématu výše UnixTime termín.

- CASeconds: Určuje počet sekund, které uplynuly od časové razítko mezipaměti.

- RTSeconds: Určuje počet sekund, po zbývající, pro který obsahu v mezipaměti bude považovat za novou. Tato hodnota se vypočítá následujícím způsobem: RTSeconds = maximální stáří - mezipaměti stáří.

- RTTimePeriod: Zbývající hodnota TTL (RTSeconds) převede na přibližný ekvivalent větší jednotky (například, dnů).

- ExpiresSeconds: Určuje, kolik sekund se datum a čas zadané v `Expires` hlavičky odpovědi. Pokud `Expires` nebyl hlavička odpovědi zahrnuty v odpovědi a pak je hodnota tento termín *žádné*.

### <a name="sample-response-header"></a>Ukázka hlavička odpovědi

Následující ukázka hlavičku odpovědi označuje stav mezipaměti požadovaného obsahu v čase, který byl vyžádán:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

