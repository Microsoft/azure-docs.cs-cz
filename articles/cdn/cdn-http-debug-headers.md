---
title: X-EC-Ladění http záhlaví pro azure cdn pravidla motoru | Dokumenty společnosti Microsoft
description: Hlavička požadavku mezipaměti ladění Ladění x-EC-ladění poskytuje další informace o zásadách mezipaměti, která je použita pro požadovaný prostředek. Tato záhlaví jsou specifická pro společnost Verizon.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: allensu
ms.openlocfilehash: 52aae3bdd2fe82eea6cbd500723192c88c293a1e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260492"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Hlavičky HTTP X-EC-Debug pro modul pravidel Azure CDN
Hlavička požadavku mezipaměti `X-EC-Debug`ladění , poskytuje další informace o zásadách mezipaměti, které jsou použity pro požadovaný prostředek. Tyto hlavičky jsou specifické pro **Azure CDN Premium od** produktů Verizon.

## <a name="usage"></a>Využití
Odpověď odeslaná uživateli ze serverů `X-EC-Debug` POP uživateli obsahuje záhlaví pouze v případě, že jsou splněny následující podmínky:

- [Funkce Hlavičky odpovědí ladicí mezipaměti](cdn-verizon-premium-rules-engine-reference-features.md#debug-cache-response-headers) byla povolena v modulu pravidel pro zadaný požadavek.
- Zadaný požadavek definuje sadu ladicích hlaviček odpovědí mezipaměti, které budou zahrnuty do odpovědi.

## <a name="requesting-debug-cache-information"></a>Vyžádání informací o ladicí mezipaměti
Pomocí následujících směrnic v zadaném požadavku definujte informace o mezipaměti ladění, které budou zahrnuty do odpovědi:

Hlavička požadavku | Popis |
---------------|-------------|
X-EC-Ladění: x-ec-cache | [Stavový kód mezipaměti](#cache-status-code-information)
X-EC-Ladění: x-ec-cache-remote | [Stavový kód mezipaměti](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Cacheable](#cacheable-response-header)
X-EC-Ladění: x-ec-cache-key | [Klíč mezipaměti](#cache-key-response-header)
X-EC-Ladění: x-ec-cache-state | [Stav mezipaměti](#cache-state-response-header)

### <a name="syntax"></a>Syntaxe

Ladění hlavičky odpovědi mezipaměti může být požadováno zahrnutím následující hlavičky a zadané direktivy v požadavku:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Ukázka záhlaví X-EC-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informace o stavovém kódu mezipaměti
Hlavička odpovědi X-EC-Debug může identifikovat server a způsob, jakým zpracoval odpověď prostřednictvím následujících směrnic:

Hlavička | Popis
-------|------------
X-EC-Ladění: x-ec-cache | Toto záhlaví je hlášeno vždy, když je obsah směrován přes CDN. Identifikuje server POP, který požadavek splnil.
X-EC-Ladění: x-ec-cache-remote | Tato hlavička je hlášena pouze v případě, že požadovaný obsah byl uložen do mezipaměti na serveru štítu původu nebo serveru brány ADN.

### <a name="response-header-format"></a>Formát záhlaví odpovědi

Hlavička X-EC-Debug hlásí informace o stavovém kódu mezipaměti v následujícím formátu:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Termíny použité ve výše uvedené syntaxi hlavičky odpovědi jsou definovány takto:
- StatusCode: Označuje, jak byl požadovaný obsah zpracován CDN, který je reprezentován prostřednictvím stavového kódu mezipaměti.
    
    Stavový kód TCP_DENIED může být hlášena namísto NONE při neoprávněném požadavku je odepřen z důvodu ověřování na základě tokenu. Stavový kód NONE se však bude nadále používat při zobrazení sestav stavu mezipaměti nebo nezpracovaných dat protokolu.

- Platforma: Označuje platformu, na které byl požadován obsah. Pro toto pole platí následující kódy:

    kód  | Platforma
    ------| --------
    ECAcc | HTTP velké
    Ecs   | HTTP malé
    Ecd   | Síť pro doručování aplikací (ADN)

- POP: Označuje [protokol POP,](cdn-pop-abbreviations.md) který požadavek zpracoval. 

### <a name="sample-response-headers"></a>Ukázkové hlavičky odpovědí

Následující ukázkové hlavičky poskytují informace o kódu stavu mezipaměti pro požadavek:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Hlavička odpovědi s mezipamětí
Hlavička `X-EC-Debug: x-ec-check-cacheable` odpovědi označuje, zda požadovaný obsah mohl být uložen do mezipaměti.

Tato hlavička odpovědi neznamená, zda došlo k ukládání do mezipaměti. Spíše uvádí, zda byla žádost způsobilá pro ukládání do mezipaměti.

### <a name="response-header-format"></a>Formát záhlaví odpovědi

Hlavička `X-EC-Debug` odpovědi, která hlásí, zda mohl být požadavek uložen do mezipaměti, je v následujícím formátu:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termín použitý ve výše uvedené syntaxi hlavičky odpovědi je definován takto:

Hodnota  | Popis
-------| --------
ANO    | Označuje, že požadovaný obsah byl způsobilý pro ukládání do mezipaměti.
NO     | Označuje, že požadovaný obsah nebyl způsobilý pro ukládání do mezipaměti. Tento stav může být způsoben jedním z následujících důvodů: <br /> - Konfigurace specifická pro zákazníka: Konfigurace specifická pro váš účet může zabránit popovým serverům v ukládání datového zdroje do mezipaměti. Modul pravidel může například zabránit ukládání datového prostředku do mezipaměti povolením funkce Obejít mezipaměť pro kvalifikační požadavky.<br /> - Hlavičky odpovědí mezipaměti: Hlavičky kontroly mezipaměti požadovaného datového zdroje a vypršení platnosti mohou zabránit tomu, aby je servery POP uvázly do mezipaměti.
Neznámý | Označuje, že servery nebyly schopny posoudit, zda byl požadovaný datový zdroj uložit do mezipaměti. K tomuto stavu obvykle dochází, když je požadavek odepřen z důvodu ověřování na základě tokenu.

### <a name="sample-response-header"></a>Ukázková hlavička odpovědi

Následující ukázková hlavička odpovědi označuje, zda požadovaný obsah mohl být uložen do mezipaměti:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Hlavička odpovědi cache-key
Hlavička `X-EC-Debug: x-ec-cache-key` odpovědi označuje fyzický klíč mezipaměti přidružený k požadovanému obsahu. Fyzický klíč mezipaměti se skládá z cesty, která identifikuje prostředek pro účely ukládání do mezipaměti. Jinými slovy, servery zkontrolují verzi datového zdroje uloženou v mezipaměti podle jeho cesty, jak je definována jeho klíčem mezipaměti.

Tento fyzický klíč mezipaměti začíná dvojitým lomítkem (//) následovaným protokolem používaným k vyžádání obsahu (HTTP nebo HTTPS). Za tímto protokolem následuje relativní cesta k požadovanému datovému zdroji, která začíná přístupovým bodem obsahu (například _/000001/_).

Ve výchozím nastavení jsou platformy HTTP konfigurovány tak, aby používaly *standardní mezipaměť*, což znamená, že řetězce dotazů jsou mechanismem ukládání do mezipaměti ignorovány. Tento typ konfigurace zabraňuje klíči mezipaměti zahrnout data řetězce dotazu.

Pokud je řetězec dotazu zaznamenán v klíči mezipaměti, je převeden na ekvivalent hodnoty hash a poté vložen mezi název&lt;požadovaného&gt;datového zdroje a jeho příponu souboru (například hodnota hash datového zdroje .html).

### <a name="response-header-format"></a>Formát záhlaví odpovědi

Hlavička `X-EC-Debug` odpovědi hlásí informace o fyzickém klíči mezipaměti v následujícím formátu:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Ukázková hlavička odpovědi

Následující ukázková hlavička odpovědi označuje fyzický klíč mezipaměti pro požadovaný obsah:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Hlavička odpovědi stavu mezipaměti
Hlavička `X-EC-Debug: x-ec-cache-state` odpovědi označuje stav mezipaměti požadovaného obsahu v době, kdy byl požadován.

### <a name="response-header-format"></a>Formát záhlaví odpovědi

Hlavička `X-EC-Debug` odpovědi hlásí informace o stavu mezipaměti v následujícím formátu:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Termíny použité ve výše uvedené syntaxi hlavičky odpovědi jsou definovány takto:

- MASeconds: Označuje maximální stáří (v sekundách), jak je definováno hlavičkami ovládacího prvku mezipaměti požadovaného obsahu.

- MATimePeriod: Převede maximální stáří hodnoty (to znamená MASeconds) na přibližný ekvivalent větší jednotky (například dny). 

- UnixTime: Označuje časové razítko cache požadovaného obsahu v unixovém čase (známé také jako ČAS POSIX nebo Unixová epocha). Časové razítko mezipaměti označuje počáteční datum a čas, ze kterého bude vypočítána hodnota TTL datového zdroje. 

    Pokud zdrojový server nevyužívá server http ukládání do mezipaměti jiného výrobce nebo pokud tento server nevrátí hlavičku odpovědi Stáří, bude časové razítko mezipaměti vždy datum a čas, kdy byl datový zdroj načten nebo znovu ověřen. V opačném případě budou servery POP používat pole Stáří k výpočtu ttl datového zdroje následujícím způsobem: Načítání/Opětovné ověřeníDateTime - Stáří.

- ddd, dd MMM yyyy HH:mm:ss GMT: Označuje časové razítko mezipaměti požadovaného obsahu. Další informace naleznete ve výše uvedeném termínu UnixTime.

- CASeconds: Označuje počet sekund, které uplynuly od časového razítka mezipaměti.

- RTSeconds: Označuje zbývající počet sekund, po které bude obsah uložený v mezipaměti považován za čerstvý. Tato hodnota se vypočítá takto: RTSeconds = maximální stáří - stáří mezipaměti.

- RTTimePeriod: Převede zbývající hodnotu TTL (tj. RTSeconds) na přibližný ekvivalent větší jednotky (například dny).

- ExpiresSeconds: Označuje počet sekund zbývajících před datem `Expires` a časem určeným v hlavičce odpovědi. Pokud `Expires` hlavička odpovědi nebyla zahrnuta do odpovědi, pak hodnota tohoto termínu není *žádná*.

### <a name="sample-response-header"></a>Ukázková hlavička odpovědi

Následující ukázková hlavička odpovědi označuje stav mezipaměti požadovaného obsahu v době, kdy byl požadován:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

