---
title: Akce v modulu standardních pravidel pro Azure CDN | Dokumenty společnosti Microsoft
description: Referenční dokumentace pro akce v modulu standardnípravidla pro Síť doručování obsahu Azure (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259948"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Akce v modulu standardních pravidel pro Azure CDN

V [modulu standardnípravidla](cdn-standard-rules-engine.md) pro Azure Content Delivery Network (Azure CDN) se pravidlo skládá z jedné nebo více podmínek shody a akce. Tento článek obsahuje podrobný popis akcí, které můžete použít v modulu standardní pravidla pro Azure CDN.

Druhá část pravidla je akce. Akce definuje chování, které se použije na typ požadavku, který identifikuje podmínka shody nebo sada podmínek shody.

## <a name="actions"></a>Akce

Následující akce jsou k dispozici pro použití v modulu standardní pravidla pro Azure CDN. 

### <a name="cache-expiration"></a>Vypršení platnosti mezipaměti

Pomocí této akce přepište hodnotu time to live (TTL) koncového bodu pro požadavky, které pravidla odpovídají podmínkám.

#### <a name="required-fields"></a>Povinná pole

Chování mezipaměti |  Popis              
---------------|----------------
Obejít mezipaměť | Pokud je vybrána tato možnost a pravidlo odpovídá, obsah není uložen do mezipaměti.
Přepis | Pokud je vybrána tato možnost a pravidlo odpovídá, hodnota TTL vrácená z počátku je přepsána hodnotou zadanou v akci.
Nastavit, pokud chybí | Pokud je vybrána tato možnost a pravidlo odpovídá, pokud nebyla vrácena žádná hodnota TTL z vašeho počátku, pravidlo nastaví TTL na hodnotu zadanou v akci.

#### <a name="additional-fields"></a>Další pole

Dny | Hodiny | Minuty | Sekundy
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Řetězec dotazu klíče mezipaměti

Tato akce slouží k úpravě klíče mezipaměti na základě řetězců dotazu.

#### <a name="required-fields"></a>Povinná pole

Chování | Popis
---------|------------
Zařadit členy | Pokud je vybrána tato možnost a pravidlo odpovídá, řetězce dotazu zadané v parametrech jsou zahrnuty při generování klíče mezipaměti. 
Ukládat do mezipaměti každou jedinečnou adresu URL | Pokud je tato možnost vybrána a pravidlo se shoduje, každá jedinečná adresa URL má svůj vlastní klíč mezipaměti. 
Exclude | Pokud je vybrána tato možnost a pravidlo odpovídá, řetězce dotazu zadané v parametrech jsou vyloučeny při generování klíče mezipaměti.
Ignorovat řetězce dotazu | Pokud je vybrána tato možnost a pravidlo odpovídá, řetězce dotazu se při generování klíče mezipaměti neberou v úvahu. 

### <a name="modify-request-header"></a>Upravit hlavičku požadavku

Tato akce slouží k úpravě záhlaví, která se nacházejí v požadavcích odeslaných do vašeho původu.

#### <a name="required-fields"></a>Povinná pole

Akce | Název záhlaví protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Pokud je vybrána tato možnost a pravidlo se shoduje, hlavička zadaná v **názvu záhlaví** je přidána do požadavku se zadanou hodnotou. Pokud je záhlaví již k dispozici, hodnota je připojena k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo se shoduje, hlavička zadaná v **názvu záhlaví** je přidána do požadavku se zadanou hodnotou. Pokud je záhlaví již k dispozici, zadaná hodnota přepíše existující hodnotu. | Řetězec
Odstranit | Pokud je vybrána tato možnost, pravidlo odpovídá a záhlaví zadané v pravidle je k dispozici, záhlaví je odstraněn z požadavku. | Řetězec

### <a name="modify-response-header"></a>Změnit záhlaví odpovědi

Tato akce slouží k úpravě záhlaví, které jsou přítomny v odpovědích vrácených vašim klientům.

#### <a name="required-fields"></a>Povinná pole

Akce | Název hlavičky HTTP | Hodnota
-------|------------------|------
Připojit | Pokud je vybrána tato možnost a pravidlo se shoduje, je hlavička zadaná v **názvu záhlaví** přidána k odpovědi pomocí zadané **hodnoty**. Pokud záhlaví je již k dispozici, **Value** je připojen k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo se shoduje, je hlavička zadaná v **názvu záhlaví** přidána k odpovědi pomocí zadané **hodnoty**. Pokud je záhlaví již k dispozici, **Value** přepíše existující hodnotu. | Řetězec
Odstranit | Pokud je vybrána tato možnost, pravidlo odpovídá a záhlaví zadané v pravidle je k dispozici, záhlaví je odstraněn z odpovědi. | Řetězec

### <a name="url-redirect"></a>Přesměrování adresy URL

Tato akce slouží k přesměrování klientů na novou adresu URL. 

#### <a name="required-fields"></a>Povinná pole

Pole | Popis 
------|------------
Typ | Vyberte typ odpovědi, který se má vrátit žadateli: Nalezeno (302), Přesunuto (301), Dočasné přesměrování (307) a Trvalé přesměrování (308).
Protocol (Protokol) | Požadavek na shodu, HTTP, HTTPS.
Název hostitele | Vyberte název hostitele, na který má být požadavek přesměrován. Chcete-li zachovat příchozího hostitele, ponechejte prázdné.
Cesta | Definujte cestu, která se má použít v přesměrování. Chcete-li zachovat příchozí cestu, ponechejte prázdné.  
Řetězec dotazu | Definujte řetězec dotazu použitý v přesměrování. Chcete-li zachovat řetězec příchozího dotazu, ponechejte prázdné. 
Fragment | Definujte fragment, který se má použít v přesměrování. Ponechte prázdné, chcete-li zachovat příchozí fragment. 

Důrazně doporučujeme použít absolutní adresu URL. Použití relativní adresy URL může přesměrovat adresy URL Azure CDN na neplatnou cestu. 

### <a name="url-rewrite"></a>Přepsání adresy URL

Pomocí této akce přepište cestu požadavku, který je na cestě k vašemu původu.

#### <a name="required-fields"></a>Povinná pole

Pole | Popis 
------|------------
Zdrojový vzor | Definujte zdrojový vzor v cestě URL, kterou chcete nahradit. V současné době zdrojový vzorek používá shodu založenou na předponové pouzdře. Chcete-li porovnat všechny cesty URL,**/** použijte jako hodnotu zdrojového vzoru lomítko ( ).
Cíl | Definujte cílovou cestu, která se má použít při přepsání. Cílová cesta přepíše zdrojový vzorek.
Zachovat neodpovídající cestu | Pokud je nastavena na **Ano**, zbývající cesta za zdrojovým vzorem je připojena k nové cílové cestě. 

## <a name="next-steps"></a>Další kroky

- [Přehled azure cdn](cdn-overview.md)
- [Reference ke stroji pravidel Standard](cdn-standard-rules-engine-reference.md)
- [Podmínky shody v modulu standardních pravidel](cdn-standard-rules-engine-match-conditions.md)
- [Vynucení HTTPS s využitím stroje pravidel Standard](cdn-standard-rules-engine.md)
