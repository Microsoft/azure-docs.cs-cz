---
title: Azure CDN z akcí modulu Microsoft Standard Rules | Microsoft Docs
description: Referenční dokumentace pro Azure CDN z akcí modulu Microsoft Standard Rules.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615985"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Azure CDN z akcí modulu Microsoft Standard Rules

V tomto článku jsou uvedené podrobné popisy dostupných akcí pro Azure Content Delivery Network (CDN) od [stroje Microsoft Standard Rules](cdn-standard-rules-engine.md).

Druhá část pravidla je akce. Akce definuje chování, které se použije na typ požadavku, který je identifikovaný sadou podmínek shody.

## <a name="actions"></a>Akce

K dispozici jsou tyto akce, které je možné použít. 

## <a name="cache-expiration"></a>Vypršení platnosti mezipaměti

Tato akce vám umožní přepsat hodnotu TTL koncového bodu pro požadavky zadané v podmínkách shody pravidel.

**Povinná pole**

Chování mezipaměti |                
---------------|----------------
Vynechat mezipaměť | Pokud je vybrána tato možnost a pravidlo odpovídá, nebude obsah uložen do mezipaměti.
Prioritu | Pokud je vybrána tato možnost a pravidlo odpovídá, hodnota TTL vrácená ze zdroje bude přepsána hodnotou zadanou v akci.
Nastavit, pokud chybí | Pokud je vybrána tato možnost a pravidlo se shoduje s tím, že se hodnota TTL nevrátila ze zdroje, nastaví pravidlo hodnotu TTL na hodnotu zadanou v akci.

**Další pole**

Dny | Hodiny | Minuty | Sekund
-----|-------|---------|--------
Hmot | Hmot | Hmot | Hmot 

## <a name="cache-key-query-string"></a>Řetězec dotazu na klíč mezipaměti

Tato akce umožňuje upravit klíč mezipaměti v závislosti na řetězcích dotazů.

**Povinná pole**

Chování | Popis
---------|------------
připojit | Pokud je vybrána tato možnost a pravidlo odpovídá, budou řetězce dotazů zadané v parametrech zahrnuty při generování klíče mezipaměti. 
Ukládat do mezipaměti každou jedinečnou adresu URL | Pokud je vybrána tato možnost a pravidlo odpovídá, bude mít každá jedinečná adresa URL vlastní klíč mezipaměti. 
Exclude | Pokud je vybrána tato možnost a pravidlo odpovídá, budou při generování klíče mezipaměti vyloučeny řetězce dotazů zadané v parametrech.
Ignorovat řetězce dotazu | Pokud je vybrána tato možnost a pravidlo odpovídá, řetězce dotazů nebudou zváženy při generování klíče mezipaměti. 

## <a name="modify-request-header"></a>Upravit hlavičku požadavku

Tato akce umožňuje změnit hlavičky obsažené v žádostech odeslaných na váš původ.

**Povinná pole**

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v názvu záhlaví přidána do žádosti se zadanou hodnotou. Pokud hlavička již existuje, bude hodnota připojena k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v názvu záhlaví přidána do žádosti se zadanou hodnotou. Pokud hlavička již existuje, hodnota přepíše existující hodnotu. | Řetězec
Odstranit | Pokud je vybrána tato možnost a pravidlo odpovídá a hlavička zadaná v pravidle je uvedena, bude odstraněna z požadavku. | Řetězec

## <a name="modify-response-header"></a>Upravit hlavičku odpovědi

Tato akce umožňuje měnit hlavičky přítomné v odpovědích vrácených koncovým klientům.

**Povinná pole**

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v názvu záhlaví přidána do odpovědi se zadanou hodnotou. Pokud hlavička již existuje, bude hodnota připojena k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v názvu záhlaví přidána do odpovědi se zadanou hodnotou. Pokud hlavička již existuje, hodnota přepíše existující hodnotu. | Řetězec
Odstranit | Pokud je vybrána tato možnost a pravidlo odpovídá a hlavička zadaná v pravidle je uvedena, bude odstraněna z odpovědi. | Řetězec

## <a name="url-redirect"></a>Přesměrování adresy URL

Tato akce vám umožní přesměrovat koncové klienty na novou adresu URL. 

**Povinná pole**

Pole | Popis 
------|------------
Typ | Vyberte typ odpovědi, který se vrátí žadateli. Možnosti jsou-302 nalezeno, 301 přesunuto, 307 dočasné přesměrování a 308 trvalé přesměrování
Protocol (Protokol) | Požadavek shody, HTTP nebo HTTPS
Název hostitele | Vyberte název hostitele, na který bude požadavek přesměrován. Nechejte prázdné, pokud chcete zachovat příchozího hostitele.
Cesta | Zadejte cestu, která se má použít v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí cestu.  
Řetězec dotazu | Zadejte řetězec dotazu použitý v přesměrování. Nechejte prázdné, pokud chcete zachovat příchozí řetězec dotazu. 
Zpomalen | Definujte fragment, který se má použít v přesměrování. Nechejte prázdné, pokud chcete zachovat příchozí fragment. 

Důrazně doporučujeme použít absolutní adresu URL. Použití relativní adresy URL může přesměrovat adresy URL CDN na neplatnou cestu. 

## <a name="url-rewrite"></a>Přepsání adresy URL

Tato akce vám umožní přepsat cestu k žádosti en route na váš původ.

**Povinná pole**

Pole | Popis 
------|------------
Zdrojový vzor | Definujte zdrojový vzor v cestě URL, která má být nahrazena. V současné době zdrojový vzor používá shodu na základě předpony. Pro vyhledání všech cest URL použijte jako hodnotu zdrojového vzoru znak "/".
Cíl | Definujte cílovou cestu, která se má použít při přepisování. Tato akce přepíše zdrojový vzor.
Zachovat neshodnou cestu | Pokud ano, zbývající cesta po zdrojovém vzoru se připojí k nové cílové cestě. 


[Zpět na začátek](#actions)

</br>

## <a name="next-steps"></a>Další kroky

- [Přehled služby Azure Content Delivery Network](cdn-overview.md)
- [Referenční informace ke stroji pravidel](cdn-standard-rules-engine-reference.md)
- [Podmínky shody stroje pravidel](cdn-standard-rules-engine-match-conditions.md)
- [Vynutilit HTTPS pomocí modulu Standard Rules](cdn-standard-rules-engine.md)
