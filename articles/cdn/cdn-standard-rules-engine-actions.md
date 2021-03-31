---
title: Akce v modulu Standard rules pro Azure CDN | Microsoft Docs
description: Referenční dokumentace pro akce v modulu Standard rules pro Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 08/04/2020
ms.author: allensu
ms.openlocfilehash: 051737a9f5e0d4092cda26a3f7ce3df1d7f535ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87760120"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Akce v modulu Standard rules pro Azure CDN

V [modulu Standard Rules](cdn-standard-rules-engine.md) pro Azure Content Delivery Network (Azure CDN) se pravidlo skládá z jedné nebo více podmínek shody a akci. Tento článek poskytuje podrobné popisy akcí, které můžete použít v modulu Standard rules pro Azure CDN.

Druhá část pravidla je akce. Akce definuje chování, které se použije na typ požadavku, který určuje podmínku shody nebo sada podmínek shody.

## <a name="actions"></a>Akce

Následující akce jsou k dispozici pro použití v modulu Standard rules pro Azure CDN. 

### <a name="cache-expiration"></a>Vypršení platnosti mezipaměti

Pomocí této akce můžete přepsat hodnotu TTL (Time to Live) koncového bodu pro požadavky, které určují podmínky shody pravidel.

#### <a name="required-fields"></a>Povinná pole

Chování mezipaměti |  Popis              
---------------|----------------
Vynechat mezipaměť | Pokud je vybrána tato možnost a pravidlo odpovídá, není obsah uložen do mezipaměti.
Přepis | Pokud je vybrána tato možnost a pravidlo odpovídá, hodnota TTL vrácená z vašeho zdroje je přepsána hodnotou zadanou v akci. Toto chování bude použito pouze v případě, že odpověď bude možné ukládat do mezipaměti. Pro hlavičkovou odpověď Cache-Control s hodnotami "žádná mezipaměť", "Private", "No-Store" nebude tato akce platná.
Nastavit, pokud chybí | Pokud je vybrána tato možnost a pravidlo se shoduje s tím, že se od počátku nevrátí žádná hodnota TTL, nastaví pravidlo hodnotu TTL na hodnotu zadanou v akci. Toto chování bude použito pouze v případě, že odpověď bude možné ukládat do mezipaměti. Pro hlavičkovou odpověď Cache-Control s hodnotami "žádná mezipaměť", "Private", "No-Store" nebude tato akce platná.

#### <a name="additional-fields"></a>Další pole

Dny | Hodiny | V řádu minut | Sekundy
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Řetězec dotazu na klíč mezipaměti

Tuto akci použijte k úpravě klíče mezipaměti založeného na řetězcích dotazů.

#### <a name="required-fields"></a>Povinná pole

Chování | Popis
---------|------------
Zařadit členy | Pokud je vybrána tato možnost a pravidlo odpovídá, budou řetězce dotazů zadané v parametrech zahrnuty při vygenerování klíče mezipaměti. 
Ukládat do mezipaměti každou jedinečnou adresu URL | Pokud je vybrána tato možnost a pravidlo odpovídá, každá jedinečná adresa URL má svůj vlastní klíč mezipaměti. 
Exclude | Pokud je vybrána tato možnost a pravidlo odpovídá, jsou při vygenerování klíče mezipaměti vyloučeny řetězce dotazů zadané v parametrech.
Ignorovat řetězce dotazů | Když se vybere tato možnost a pravidlo se shoduje, řetězce dotazů se při vygenerování klíče mezipaměti neberou v úvahu. 

### <a name="modify-request-header"></a>Upravit hlavičku požadavku

Tuto akci použijte, pokud chcete upravit hlavičky, které se nacházejí v žádostech odeslaných na váš původ.

#### <a name="required-fields"></a>Povinná pole

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Pokud je vybrána tato možnost a pravidlo odpovídá, bude do žádosti se zadanou hodnotou přidána hlavička zadaná v **názvu záhlaví** . Pokud hlavička již existuje, hodnota se připojí k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo odpovídá, bude do žádosti se zadanou hodnotou přidána hlavička zadaná v **názvu záhlaví** . Pokud hlavička již existuje, zadaná hodnota přepíše existující hodnotu. | Řetězec
Odstranit | Když je vybraná tato možnost, pravidlo se shoduje s a hlavička zadaná v pravidle je k dispozici, hlavička se z požadavku odstraní. | Řetězec

### <a name="modify-response-header"></a>Upravit hlavičku odpovědi

Tuto akci použijte k úpravě hlaviček, které jsou k dispozici v odpovědích vrácených klientům.

#### <a name="required-fields"></a>Povinná pole

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v **názvu záhlaví** přidána k odpovědi pomocí zadané **hodnoty**. Pokud hlavička již existuje, **hodnota** se připojí k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v **názvu záhlaví** přidána k odpovědi pomocí zadané **hodnoty**. Pokud je již hlavička přítomna, **hodnota** přepíše existující hodnotu. | Řetězec
Odstranit | Když je vybraná tato možnost, pravidlo se shoduje s a hlavička zadaná v pravidle je k dispozici, hlavička se z odpovědi odstraní. | Řetězec

### <a name="url-redirect"></a>Přesměrování adresy URL

Tuto akci použijte k přesměrování klientů na novou adresu URL. 

#### <a name="required-fields"></a>Povinná pole

Pole | Popis 
------|------------
Typ | Vyberte typ odpovědi, který se má vrátit žadateli: Nalezeno (302), přesunuto (301), dočasné přesměrování (307) a trvalé přesměrování (308).
Protokol | Požadavek shody, HTTP, HTTPS.
Název hostitele | Vyberte název hostitele, na který chcete požadavek přesměrovat. Ponechte prázdné, pokud chcete zachovat příchozího hostitele.
Cesta | Zadejte cestu, která se má použít v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí cestu.  
Řetězec dotazu | Zadejte řetězec dotazu použitý v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí řetězec dotazu. 
Fragment | Definujte fragment, který se použije v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí fragment. 

Důrazně doporučujeme, abyste použili absolutní adresu URL. Použití relativní adresy URL může přesměrovat Azure CDN adresy URL na neplatnou cestu. 

### <a name="url-rewrite"></a>Přepsání adresy URL

Pomocí této akce přepište cestu k žádosti, která je v cestě k původnímu zdroji.

#### <a name="required-fields"></a>Povinná pole

Pole | Popis 
------|------------
Zdrojový vzor | Definujte zdrojový vzor v cestě URL, která má být nahrazena. V současné době zdrojový vzor používá shodu na základě předpony. Pro vyhledání všech cest URL použijte lomítko ( **/** ) jako hodnotu zdrojového vzoru.
Cíl | Zadejte cílovou cestu, která se má použít při přepisování. Cílová cesta přepíše zdrojový vzor.
Zachovat neshodnou cestu | Pokud je nastaveno na **Ano**, zbývající cesta po zdrojovém vzoru se připojí k nové cílové cestě. 

## <a name="next-steps"></a>Další kroky

- [Přehled Azure CDN](cdn-overview.md)
- [Reference ke stroji pravidel Standard](cdn-standard-rules-engine-reference.md)
- [Podmínky shody v modulu Standard Rules](cdn-standard-rules-engine-match-conditions.md)
- [Vynucení HTTPS s využitím stroje pravidel Standard](cdn-standard-rules-engine.md)
