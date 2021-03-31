---
title: X-EC – ladění hlaviček protokolu HTTP pro modul pravidel Azure CDN | Microsoft Docs
description: V hlavičce žádosti o mezipaměť pro ladění X-EC jsou uvedeny další informace o zásadách mezipaměti, které se aplikují na požadovaný prostředek. Tato záhlaví jsou specifická pro Verizon.
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
ms.openlocfilehash: 4154c6a1e739f935022271e7a101f39d3ee5c500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "84343016"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>X-EC – ladění hlaviček protokolu HTTP pro modul pravidel Azure CDN
Hlavička žádosti mezipaměti ladění `X-EC-Debug` poskytuje další informace o zásadách mezipaměti, které se vztahují k požadovanému prostředku. Tato záhlaví jsou specifická pro **Azure CDN Premium z produktů Verizon** .

## <a name="usage"></a>Využití
Odpověď odesílaná ze serverů POP uživateli zahrnuje `X-EC-Debug` hlavičku, pouze pokud jsou splněny následující podmínky:

- V modulu pravidel pro zadaný požadavek byla povolena [funkce hlaviček odpovědí mezipaměti ladění](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) .
- Zadaný požadavek definuje sadu hlaviček odpovědí mezipaměti pro ladění, které budou zahrnuty do odpovědi.

## <a name="requesting-debug-cache-information"></a>Vyžadování informací o mezipaměti ladění
Pomocí následujících direktiv v zadaném požadavku definujte informace o mezipaměti ladění, které budou zahrnuty do odpovědi:

Hlavička požadavku | Popis |
---------------|-------------|
X-EC-ladění: x-EC – mezipaměť | [Stavový kód mezipaměti](#cache-status-code-information)
X-EC-ladění: x-EC-cache-Remote | [Stavový kód mezipaměti](#cache-status-code-information)
X-EC-ladění: x-EC-check-Cached | [Uložitelný](#cacheable-response-header)
X-EC-ladění: x-EC-cache-Key | [Klíč mezipaměti](#cache-key-response-header)
X-EC-ladění: x-EC-mezipaměť-State | [Stav mezipaměti](#cache-state-response-header)

### <a name="syntax"></a>Syntax

Hlavičky odpovědí mezipaměti ladění mohou být požadovány vložením následující hlavičky a zadaných direktiv v žádosti:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Ukázka záhlaví X-EC-ladění

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informace o stavovém kódu mezipaměti
Hlavička odpovědi X-EC-Debug může určit server a způsob, jakým zpracovává odpověď, prostřednictvím následujících direktiv:

Hlavička | Popis
-------|------------
X-EC-ladění: x-EC – mezipaměť | Tato hlavička se oznamuje při každém směrování obsahu přes CDN. Identifikuje server POP, který požadavek splnil.
X-EC-ladění: x-EC-cache-Remote | Tato hlavička se oznamuje jenom v případě, že je požadovaný obsah uložený v mezipaměti na zdrojovém serveru ochrany nebo na serveru brány a.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

Hlavička X-EC-Debug hlásí informace o stavovém kódu mezipaměti v následujícím formátu:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

Výrazy použité ve výše uvedené syntaxi hlaviček odpovědí jsou definovány takto:
- StatusCode: udává, jak byl požadovaný obsah zpracován přes CDN, který je reprezentován kódem stavu mezipaměti.
    
    Stavový kód TCP_DENIED může být hlášen jako žádný, pokud je odmítnut neautorizovaný požadavek z důvodu Token-Basedho ověřování. Stavový kód NONE se ale bude dál používat při prohlížení zpráv o stavu mezipaměti nebo nezpracovaná data protokolu.

- Platforma: Určuje platformu, na které byl obsah požadován. Pro toto pole jsou platné následující kódy:

    Kód  | Platforma
    ------| --------
    ECAcc | Velký HTTP
    ECS   | HTTP – malý
    ECD   | Application Delivery Network (a)

- POP: označuje [bod POP](cdn-pop-abbreviations.md) , který zpracoval požadavek. 

### <a name="sample-response-headers"></a>Ukázkové hlavičky odpovědí

Následující ukázkové hlavičky obsahují informace o stavovém kódu mezipaměti pro požadavek:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Hlavička odpovědi v mezipaměti
`X-EC-Debug: x-ec-check-cacheable`Hlavička odpovědi indikuje, zda byl požadovaný obsah uložen do mezipaměti.

Tato hlavička odpovědi neoznačuje, zda došlo k ukládání do mezipaměti. Místo toho indikuje, zda byl požadavek způsobilý pro ukládání do mezipaměti.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug`Hlavička odpovědi, která oznamuje, zda může být požadavek uložen v mezipaměti, je v následujícím formátu:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Termín použitý ve výše uvedené syntaxi hlavičky odpovědi je definován následujícím způsobem:

Hodnota  | Popis
-------| --------
ANO    | Indikuje, že požadovaný obsah byl způsobilý pro ukládání do mezipaměti.
NO     | Indikuje, že požadovaný obsah nebyl pro ukládání do mezipaměti způsobilý. K tomuto stavu může dojít z některého z následujících důvodů: <br /> -Customer-Specific konfiguraci: konfigurace specifická pro váš účet může zabránit serverům pop v ukládání assetu do mezipaměti. Například modul pravidel může zabránit ukládání prostředku do mezipaměti tím, že povolí funkci vynechat mezipaměť pro opravňující požadavky.<br /> – Hlavičky odpovědi mezipaměti: Cache-Control a hlavičky vypršení platnosti vyžádaného assetu můžou zabránit ukládání do mezipaměti serverem POP.
Neznámý | Indikuje, že servery nedokázaly vyhodnotit, jestli je požadovaný prostředek v mezipaměti. K tomuto stavu obvykle dochází v případě, že je žádost zamítnuta z důvodu ověřování založeného na tokenech.

### <a name="sample-response-header"></a>Hlavička ukázkové odpovědi

Následující hlavička odpovědi obsahuje informaci o tom, zda byl požadovaný obsah uložen do mezipaměti:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Hlavička odpovědi Cache-Key
`X-EC-Debug: x-ec-cache-key`Hlavička odpovědi indikuje fyzický meziklíčovou mezipaměť přidruženou k požadovanému obsahu. Klíč fyzické mezipaměti se skládá z cesty, která identifikuje prostředek pro účely ukládání do mezipaměti. Jinými slovy, servery budou kontrolovat verzi assetu v mezipaměti podle jeho cesty, jak je definováno jeho meziklíčovou mezipamětí.

Tento klíč fyzické mezipaměti začíná dvojitým lomítkem (//) následovaný protokolem, který se používá k vyžádání obsahu (HTTP nebo HTTPS). Tento protokol je následován relativní cestou k požadovanému prostředku, který začíná přístupovým bodem obsahu (například _/000001/_).

Ve výchozím nastavení jsou platformy HTTP nakonfigurovány tak, aby používaly *standardní mezipaměť*, což znamená, že řetězce dotazů jsou ignorovány mechanismem ukládání do mezipaměti. Tento typ konfigurace brání tomu, aby klíč mezipaměti mohl zahrnovat data řetězce dotazu.

Pokud je řetězec dotazu zaznamenán v mezipaměti, je převeden na jeho ekvivalent hodnoty hash a poté vložen mezi název požadovaného prostředku a jeho příponu souboru (například &lt; hodnota hash Asset Value &gt; . html).

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug`Hlavička odpovědi hlásí informace o fyzické mezipaměti-klíče v následujícím formátu:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Hlavička ukázkové odpovědi

V následujícím ukázkovém záhlaví odpovědi se označuje klíč fyzické mezipaměti pro požadovaný obsah:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Hlavička odpovědi na stav mezipaměti
`X-EC-Debug: x-ec-cache-state`Hlavička odpovědi indikuje stav mezipaměti požadovaného obsahu v době, kdy se požadoval.

### <a name="response-header-format"></a>Formát hlavičky odpovědi

`X-EC-Debug`Hlavička odpovědi hlásí informace o stavu mezipaměti v následujícím formátu:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

Výrazy použité ve výše uvedené syntaxi hlaviček odpovědí jsou definovány takto:

- MASeconds: Určuje maximální stáří (v sekundách) definované hlavičkou Cache-Control požadovaného obsahu.

- MATimePeriod: Převede hodnotu maximálního stáří (tj. MASeconds) na přibližný ekvivalent větší jednotky (například dny). 

- UnixTime: označuje časové razítko mezipaměti požadovaného obsahu v systému UNIX (označuje se také jako čas POSIX nebo UNIX epocha). Časové razítko mezipaměti indikuje počáteční datum a čas, ze kterého se bude počítat hodnota TTL prostředku. 

    Pokud zdrojový Server nevyužívá server mezipaměti protokolu HTTP třetí strany, nebo pokud tento server nevrací hlavičku stáří, pak bude časové razítko mezipaměti vždycky datum a čas, kdy se Asset získal nebo znovu ověřil. V opačném případě budou servery POP pomocí pole věk vypočítat hodnotu TTL prostředku následujícím způsobem: načítání/RevalidateDateTime-Age.

- DDD, dd MMM RRRR HH: mm: ss GMT: označuje časové razítko mezipaměti požadovaného obsahu. Další informace najdete výše v UnixTime období.

- CASeconds: označuje počet sekund, které uplynuly od časového razítka mezipaměti.

- RTSeconds: označuje počet zbývajících sekund, po které bude obsah uložený v mezipaměti považován za čerstvý. Tato hodnota se vypočítá takto: RTSeconds = Max – stáří mezipaměti.

- RTTimePeriod: převede zbývající hodnotu TTL (tj. RTSeconds) na přibližný ekvivalent větší jednotky (například dny).

- ExpiresSeconds: Určuje počet sekund zbývajících před datem a časem zadaným v `Expires` hlavičce odpovědi. Pokud `Expires` Hlavička odpovědi nebyla v odpovědi obsažena, hodnota tohoto termínu je *none*.

### <a name="sample-response-header"></a>Hlavička ukázkové odpovědi

V následujícím ukázkovém záhlaví odpovědi se uvádí stav mezipaměti požadovaného obsahu v době, kdy byl vyžádán:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

