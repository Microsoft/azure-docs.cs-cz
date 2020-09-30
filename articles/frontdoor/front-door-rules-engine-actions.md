---
title: Akce modulu pravidel pro přední dveře Azure
description: Tento článek poskytuje seznam různých akcí, které můžete provádět s modulem pravidel pro službu Azure dopředných dveří.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: ff61af192471bcfc9bdb9f1ce3970d5c22f39579
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569780"
---
# <a name="azure-front-door-rules-engine-actions"></a>Akce stroje pravidel služby Azure Front Door

V [modulu pravidel AFD](front-door-rules-engine.md)se pravidlo skládá z nuly nebo více podmínek a akcí shody. Tento článek poskytuje podrobné popisy akcí, které můžete použít v modulu pravidel AFD.

Akce definuje chování, které se použije na typ požadavku, který určuje podmínku shody nebo sada podmínek shody. V modulu pravidel AFD může pravidlo obsahovat až pět akcí. Akce přepsání konfigurace trasy (přesměrování nebo přesměrování) může být pouze jedna z nich.

K dispozici jsou následující akce, které je možné použít v modulu pravidel služby Azure front-dveří.  

## <a name="modify-request-header"></a>Upravit hlavičku požadavku

Tuto akci použijte, pokud chcete upravit hlavičky, které se nacházejí v žádostech odeslaných na váš původ.

### <a name="required-fields"></a>Povinná pole

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Když se vybere tato možnost a pravidlo se shoduje s, hlavička zadaná v **názvu záhlaví** se přidá do žádosti se zadanou hodnotou. Pokud hlavička již existuje, hodnota se připojí k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v **názvu záhlaví** přidána do žádosti se zadanou hodnotou. Pokud hlavička již existuje, zadaná hodnota přepíše existující hodnotu. | Řetězec
Odstranit | Když se tato možnost vybere s pravidly pro porovnání a hlavička zadaná v pravidle je k dispozici, záhlaví se odstraní z požadavku. | Řetězec

## <a name="modify-response-header"></a>Upravit hlavičku odpovědi

Tuto akci použijte k úpravě hlaviček, které jsou k dispozici v odpovědích vrácených klientům.

### <a name="required-fields"></a>Povinná pole

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Když se vybere tato možnost a pravidlo se shoduje s, hlavička zadaná v **názvu záhlaví** se přidá k odpovědi pomocí zadané **hodnoty**. Pokud hlavička již existuje, **hodnota** se připojí k existující hodnotě. | Řetězec
Přepsat | Pokud je vybrána tato možnost a pravidlo odpovídá, bude hlavička zadaná v **názvu záhlaví** přidána k odpovědi pomocí zadané **hodnoty**. Pokud je již hlavička přítomna, **hodnota** přepíše existující hodnotu. | Řetězec
Odstranit | Když se vybere tato možnost a pravidlo se shoduje s hlavičkou zadanou v pravidle, bude se záhlaví odstranit z odpovědi. | Řetězec

## <a name="route-configuration-overrides"></a>Přepisování konfigurace směrování 

### <a name="route-type-redirect"></a>Typ trasy: přesměrování

Tuto akci použijte k přesměrování klientů na novou adresu URL. 

#### <a name="required-fields"></a>Povinná pole

Pole | Description 
------|------------
Typ přesměrování | Vyberte typ odpovědi, který se má vrátit žadateli: Nalezeno (302), přesunuto (301), dočasné přesměrování (307) a trvalé přesměrování (308).
Protokol přesměrování | Požadavek shody, HTTP, HTTPS.
Cílový hostitel | Vyberte název hostitele, na který chcete požadavek přesměrovat. Ponechte prázdné, pokud chcete zachovat příchozího hostitele.
Cílová cesta | Zadejte cestu, která se má použít v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí cestu.  
Řetězec dotazu | Zadejte řetězec dotazu použitý v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí řetězec dotazu. 
Fragment cíle | Definujte fragment, který se použije v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí fragment. 


### <a name="route-type-forward"></a>Typ trasy: přesměrování

Tuto akci použijte pro přeposílání klientů na novou adresu URL. Tato akce také obsahuje dílčí akce pro přepsání adresy URL a ukládání do mezipaměti. 

Pole | Description 
------|------------
Back-endový fond | Vyberte back-end fond pro přepsání a obsluhu požadavků. zobrazí se také všechny předem nakonfigurované back-end fondy, které jsou aktuálně v profilu front-endu. 
Protokol předávání | Požadavek shody, HTTP, HTTPS.
Přepsání adresy URL | Pomocí této akce přepište cestu k žádosti, která je v cestě k původnímu zdroji. Pokud je tato možnost povolená, podívejte se na další požadovaná pole.
Ukládání do mezipaměti | Povoleno, zakázáno. Pokud je povoleno, podívejte se na následující další pole. 

#### <a name="url-rewrite"></a>Přepsání adresy URL

Pomocí tohoto nastavení můžete nakonfigurovat volitelnou **vlastní cestu přesměrování** , která se má použít při vytváření žádosti pro předání do back-endu.

Pole | Description 
------|------------
Vlastní cesta přesměrování | Zadejte cestu, na kterou mají být požadavky předány. 

#### <a name="caching"></a>Ukládání do mezipaměti

Pomocí těchto nastavení můžete řídit, jak se soubory ukládají do mezipaměti pro požadavky obsahující řetězce dotazů. Zda se má obsah ukládat do mezipaměti na základě všech parametrů nebo vybraných parametrů. Pomocí dalších nastavení můžete přepsat hodnotu TTL (Time to Live) a určit tak, jak dlouho zůstane obsah v mezipaměti. Pokud chcete vynutit ukládání do mezipaměti jako akci, nastavte pole ukládání do mezipaměti na povoleno. Když vynutíte ukládání do mezipaměti, zobrazí se následující možnosti: 

Chování mezipaměti |  Description              
---------------|----------------
Ignorovat řetězce dotazů | Po uložení prostředku do mezipaměti všechny požadavky v žádosti ignorují řetězce dotazu, dokud nevyprší platnost prostředku uloženého v mezipaměti.
Ukládat do mezipaměti každou jedinečnou adresu URL | Každý požadavek s jedinečnou adresou URL, včetně řetězce dotazu, je považován za jedinečný prostředek s vlastní mezipamětí.
Ignorovat zadané řetězce dotazu | Řetězce dotazů adresy URL dotazu uvedené v nastavení "parametry dotazu" jsou pro ukládání do mezipaměti ignorovány.
Zahrnout zadané řetězce dotazu | Pro ukládání do mezipaměti se používají řetězce dotazů adresy URL uvedené v nastavení "parametry dotazu".

Další pole |  Description 
------------------|---------------
Dynamická komprese | Přední dvířka můžou dynamicky Komprimovat obsah na hranici, což vede k menší a rychlejší reakci.
Parametry dotazů | Seznam povolených (nebo nepovolených) parametrů oddělených čárkami, který se má použít jako základ pro ukládání do mezipaměti.
Doba uložení mezipaměti | Doba vypršení platnosti mezipaměti ve dnech, hodinách, minutách, sekundách Všechny hodnoty musí být int. 

## <a name="next-steps"></a>Další kroky

- Naučte se konfigurovat modul pro první [pravidla](front-door-tutorial-rules-engine.md). 
- Další informace o [podmínkách shody stroje pravidel](front-door-rules-engine-match-conditions.md)
- Další informace o [modulu pravidel pro přední dveře Azure](front-door-rules-engine.md)
