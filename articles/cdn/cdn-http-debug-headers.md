---
title: Hlavičky protokolu HTTP X-ES-ladicí stroj pravidel Azure CDN | Dokumentace Microsoftu
description: Záhlaví X-ES-Debug ladění mezipaměti požadavek obsahuje další informace o zásady ukládání do mezipaměti, který se použije pro požadovaný prostředek. Tyto hlavičky jsou specifické pro Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: 4ba42850ee28e2e212d9bc2b7b64be103218757c
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49094220"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Hlavičky HTTP X-ES-ladění pro Azure CDN stroje pravidel
Hlavičky žádosti ladění mezipaměti `X-EC-Debug`, poskytuje další informace o zásady ukládání do mezipaměti, který se použije pro požadovaný prostředek. Tyto hlavičky jsou specifické pro **Azure CDN Premium od Verizonu** produktů.

## <a name="usage"></a>Využití
Zahrnuje odpověď odesílanou ze serverů POP pro uživatele `X-EC-Debug` záhlaví jenom v případě, že jsou splněny následující podmínky:

- [Funkce ladění hlavičky odpovědi mezipaměti](cdn-rules-engine-reference-features.md#debug-cache-response-headers) byla zapnuta stroj pravidel pro zadaný požadavek.
- Zadaný požadavek definuje sadu hlaviček odpovědí mezipaměti ladění, které budou zahrnuty v odpovědi.

## <a name="requesting-debug-cache-information"></a>Žádost o informace o ladění mezipaměti
Použijte následující direktivy v zadané žádosti k definování mezipaměti informace o ladění, který bude zahrnut v odpovědi:

Hlavička požadavku | Popis |
---------------|-------------|
X-ES ladění: x ES cache | [Kód stavu mezipaměti](#cache-status-code-information)
X-ES ladění: x ES cache-remote | [Kód stavu mezipaměti](#cache-status-code-information)
X-ES ladění: x ES kontrola-možné ukládat do mezipaměti | [Možné ukládat do mezipaměti](#cacheable-response-header)
X-ES ladění: x ES mezipaměti – klíč | [Klíč mezipaměti](#cache-key-response-header)
X-ES ladění: x ES cache stav | [Stav mezipaměti](#cache-state-response-header)

### <a name="syntax"></a>Syntaxe

Ladění odpověď mezipaměti, které mohou být vyžádány záhlaví zahrnutím následující záhlaví a direktivy zadaný v požadavku:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Ukázka X-ES-Debug záhlaví

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Mezipaměť informací o kódu stavu
Hlavička X-ES-Debug odpovědi můžete identifikovat server a způsob zpracování odpovědi prostřednictvím následující direktivy:

Hlavička | Popis
-------|------------
X-ES ladění: x ES cache | Tato hlavička se použije v hlášení pokaždé, když se obsah je směrován přes síť CDN. Identifikuje server POP, která požadavek splnila.
X-ES ladění: x ES cache-remote | Tato hlavička se hlásí pouze v případě, že se požadovaný obsah ukládá do mezipaměti na zdrojový server shield nebo server brány ADN.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

Hlavička X-ES-Debug sestavy informací o kódu stavu mezipaměti v následujícím formátu:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Termíny používané v syntaxi výše uvedené hlavičky odpovědi jsou definovány takto:
- StatusCode: Určuje, jak se požadovaný obsah zpracovat CDN, která je reprezentována pomocí mezipaměti stavový kód.
    
    Stavový kód TCP_DENIED může být nahlášeno místo žádný při neoprávněného požadavku byl odepřen v důsledku ověřování založené na tokenech. Stavový kód NONE bude však nadále se dá použít při zobrazení zprávy o stavu mezipaměti nebo nezpracovaná data protokolu.

- Platforma: Určuje platformu, na kterém byl vyžádán obsah. Následující kódy jsou platné pro toto pole:

    Kód  | Platforma
    ------| --------
    Banky ECAcc | Velké HTTP
    ECS   | HTTP (krátkodobé používání)
    ECD   | Síť pro doručování aplikací (ADN)

- POP: Označuje, [POP](cdn-pop-abbreviations.md) , který zpracovává požadavek. 

### <a name="sample-response-headers"></a>Ukázka hlavičky odpovědi

Tato ukázka záhlaví poskytují informace o kódu stavu mezipaměti pro žádost:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Hlavička odpovědi možné ukládat do mezipaměti
`X-EC-Debug: x-ec-check-cacheable` Hlavička odpovědi označuje, zda požadovaný obsah by mohl mít byl uložený do mezipaměti.

Tato hlavička odpovědi neuvádí, jestli ukládání do mezipaměti konal úplně. Místo toho znamená to, zda byla žádost nárok na ukládání do mezipaměti.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug` Hlavička odpovědi vytváření sestav, jestli žádost by mohla mít byl uložený do mezipaměti je v následujícím formátu:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termín, který se v syntaxi výše uvedené hlavičky odpovědi je definovaná následujícím způsobem:

Hodnota  | Popis
-------| --------
ANO    | Označuje, že požadovaný obsah byl způsobilý pro ukládání do mezipaměti.
NE     | Označuje, že se požadovaný obsah nemá nárok na ukládání do mezipaměti. Tento stav může být způsobené jedním z následujících důvodů: <br /> – Konfigurace zákaznické: konfigurace specifické pro váš účet dokážou zabránit zahlcení serverů pop z ukládání do mezipaměti prostředek. Stroj pravidel může například zabránit prostředek ukládat do mezipaměti povolíte funkci mezipaměti jednorázové přihlášení pro oprávněné požadavky.<br /> -Hlavičky odpovědi cache: Požadovaný prostředek hlavičky Cache-Control a Expires zabránit POP servery ukládání do mezipaměti.
NEZNÁMÝ | Označuje, že servery nám k vyhodnocení, zda požadovaný prostředek je možné ukládat do mezipaměti. Tento stav obvykle dochází, když požadavek byl odepřen v důsledku ověřování založené na tokenech.

### <a name="sample-response-header"></a>Hlavička odpovědi vzorku

Následující ukázka hlavička odpovědi označuje, zda požadovaný obsah by mohl mít byl uložený do mezipaměti:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Hlavička odpovědi klíč mezipaměti
`X-EC-Debug: x-ec-cache-key` Hlavička odpovědi určuje fyzické mezipaměti – klíč přidružený k požadovaný obsah. Fyzické klíč mezipaměti se skládá z cestu, která identifikuje prostředek pro účely ukládání do mezipaměti. Jinými slovy servery zkontroluje verzi v mezipaměti prostředek podle jeho cesty podle jeho klíče mezipaměti.

Tento fyzický mezipaměti – klíč začíná dvojité lomítka (/ /) následuje protokol použitý pro vyžádání obsahu (HTTP nebo HTTPS). Tento protokol následuje relativní cesta k požadované asset, který začíná znakem obsahu přístupový bod (například _/000001/_).

Ve výchozím nastavení, HTTP platformy umožňují použít *standard mezipaměti*, což znamená, že řetězce dotazu jsou ignorovány pomocí mechanismu ukládání do mezipaměti. Tento typ konfigurace zabraňuje klíč mezipaměti včetně data řetězce dotazu.

Pokud řetězec dotazu je zaznamenán v klíč mezipaměti, má být převeden na její ekvivalentní hodnotu hash a potom vložen mezi název požadovaného assetu a jeho příponu souboru (například majetku&lt;hodnoty hash&gt;.html).

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug` Hlavička odpovědi sestavy fyzické klíč mezipaměti informace v následujícím formátu:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Hlavička odpovědi vzorku

Následující ukázkové odpovědi záhlaví označuje fyzické klíč mezipaměti pro požadovaný obsah:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Hlavička odpovědi stavu mezipaměti
`X-EC-Debug: x-ec-cache-state` Hlavička odpovědi označuje stav mezipaměti požadovaného obsahu v době byla vyžádána.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug` Hlavička odpovědi hlásí informace o stavu mezipaměti v následujícím formátu:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Termíny používané v syntaxi výše uvedené hlavičky odpovědi jsou definovány takto:

- MASeconds: Označuje max-age (v sekundách), podle definovaných hlavičkami Cache-Control požadovaný obsah.

- MATimePeriod: Převede hodnotu max-age (MASeconds) na přibližně ekvivalentem větší jednotky (například ve dnech). 

- UnixTime: Určuje časové razítko mezipaměti požadovaného obsahu v Unixový čas (označovaný také jako POSIX čas nebo Unix epocha). Časové razítko mezipaměti označuje počáteční datum/čas ze kterého se vypočte hodnota TTL prostředků. 

    Pokud zdrojový server nevyužívá třetích stran HTTP, ukládání do mezipaměti serveru nebo pokud tento server nevrací hlavička odpovědi věk, pak časové razítko mezipaměti bude mít vždy datum a čas, kdy byl asset načíst nebo ověřit. V opačném případě servery POP použije pole stáří k výpočtu hodnoty TTL prostředku následujícím způsobem: načítání/RevalidateDateTime - věku.

- ddd, dd MMM yyyy HH: mm: GMT: Určuje časové razítko mezipaměti požadovaného obsahu. Další informace najdete v tématu UnixTime termín výše.

- CASeconds: Určuje počet sekund, které uplynuly od časové razítko mezipaměti.

- RTSeconds: Určuje počet sekund pro kterou obsah uložený v mezipaměti bude považovat za čerstvý. Tato hodnota se vypočítá následovně: RTSeconds = max-age – mezipaměť: age.

- RTTimePeriod: Zbývající hodnota TTL (RTSeconds) převede na přibližně ekvivalentem větší jednotky (například ve dnech).

- ExpiresSeconds: Určuje počet sekund, než budou data a času podle `Expires` hlavičky odpovědi. Pokud `Expires` v odpovědi nebyla zahrnuta hlavička odpovědi a pak je hodnota tento termín *žádný*.

### <a name="sample-response-header"></a>Hlavička odpovědi vzorku

Následující ukázkové odpovědi záhlaví označuje stav mezipaměti požadovaného obsahu v době, který byl vyžádán:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

