---
title: Konfigurace akcí sady pravidel služby Azure front-end Standard/Premium
description: Tento článek poskytuje seznam různých akcí, které můžete provést se sadou pravidel pro službu Azure front-dveří.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: c9995df0f292c5e528156a3280df5484db017fca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101099196"
---
# <a name="azure-front-door-standardpremium-rule-set-actions"></a>Akce sady pravidel služby Azure front-Premium Standard/Premium

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

[Sada pravidel](concept-rule-set.md) pro přední dveře Azure se skládá z pravidel s kombinací podmínek a akcí shody. Tento článek poskytuje podrobný popis akcí, které můžete v sadě pravidel použít. Akce definuje chování, které se použije na typ požadavku, který určuje podmínky shody. V sadě pravidel front-end pro Azure může pravidlo obsahovat až pět akcí. Proměnná serveru je podporovaná u všech akcí.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

K dispozici jsou následující akce, které je možné použít v sadě pravidel služby Azure front-dveří.  

## <a name="cache-expiration"></a>Vypršení platnosti mezipaměti

Pomocí této akce můžete přepsat hodnotu TTL (Time to Live) koncového bodu pro požadavky, které určují podmínky shody pravidel.

### <a name="required-fields"></a>Povinná pole

Následující popis platí při výběru těchto chování mezipaměti a odpovídajících pravidel:

Chování mezipaměti |  Description              
---------------|----------------
Vynechat mezipaměť | Obsah není uložen v mezipaměti.
Přepis | Hodnota TTL vrácená z vašeho zdroje je přepsána hodnotou zadanou v akci. Toto chování bude použito pouze v případě, že odpověď bude možné ukládat do mezipaměti. Pro hlavičkovou odpověď Cache-Control s hodnotami "žádná mezipaměť", "Private", "No-Store" se akce nepoužije.
Nastavit, pokud chybí | Pokud se z vašeho zdroje nevrátí žádná hodnota TTL, nastaví pravidlo hodnotu TTL na hodnotu zadanou v akci. Toto chování bude použito pouze v případě, že odpověď bude možné ukládat do mezipaměti. Pro hlavičkovou odpověď Cache-Control s hodnotami "žádná mezipaměť", "Private", "No-Store" se akce nepoužije.

### <a name="additional-fields"></a>Další pole

Dny | Hodiny | V řádu minut | Sekundy
-----|-------|---------|--------
Int | Int | Int | Int 

## <a name="cache-key-query-string"></a>Řetězec dotazu na klíč mezipaměti

Tuto akci použijte k úpravě klíče mezipaměti založeného na řetězcích dotazů.

### <a name="required-fields"></a>Povinná pole

Následující popis platí při výběru těchto chování a shody pravidel:

Chování | Description
---------|------------
Zařadit členy | Řetězce dotazů zadané v parametrech jsou zahrnuté při vygenerování klíče mezipaměti. 
Ukládat do mezipaměti každou jedinečnou adresu URL | Každá jedinečná adresa URL má svůj vlastní klíč mezipaměti. 
Exclude | Řetězce dotazů zadané v parametrech jsou vyloučené při vygenerování klíče mezipaměti.
Ignorovat řetězce dotazů | Při generování klíče mezipaměti se neberou v úvahu řetězce dotazů. 

## <a name="modify-request-header"></a>Upravit hlavičku požadavku

Tuto akci použijte, pokud chcete upravit hlavičky, které se nacházejí v žádostech odeslaných na váš původ.

### <a name="required-fields"></a>Povinná pole

Následující popis platí při výběru těchto akcí a odpovídajících pravidel:

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Hlavička zadaná v **názvu záhlaví** se přidá do žádosti se zadanou hodnotou. Pokud hlavička již existuje, hodnota se připojí k existující hodnotě. | Řetězec
Přepsat | Hlavička zadaná v **názvu záhlaví** se přidá do žádosti se zadanou hodnotou. Pokud hlavička již existuje, zadaná hodnota přepíše existující hodnotu. | Řetězec
Odstranit | Pokud je k dispozici hlavička uvedená v pravidle, hlavička se odstraní z požadavku. | Řetězec

## <a name="modify-response-header"></a>Upravit hlavičku odpovědi

Tuto akci použijte k úpravě hlaviček, které jsou k dispozici v odpovědích vrácených klientům.

### <a name="required-fields"></a>Povinná pole

Následující popis platí při výběru těchto akcí a odpovídajících pravidel:

Akce | Název hlavičky protokolu HTTP | Hodnota
-------|------------------|------
Připojit | Hlavička zadaná v **názvu záhlaví** se přidá k odpovědi pomocí zadané **hodnoty**. Pokud hlavička již existuje, **hodnota** se připojí k existující hodnotě. | Řetězec
Přepsat | Hlavička zadaná v **názvu záhlaví** se přidá k odpovědi pomocí zadané **hodnoty**. Pokud je již hlavička přítomna, **hodnota** přepíše existující hodnotu. | Řetězec
Odstranit | Pokud je k dispozici hlavička uvedená v pravidle, hlavička se odstraní z odpovědi. | Řetězec

## <a name="url-redirect"></a>Přesměrování adresy URL

Tuto akci použijte k přesměrování klientů na novou adresu URL. 

### <a name="required-fields"></a>Povinná pole

Pole | Description 
------|------------
Typ přesměrování | Vyberte typ odpovědi, který se má vrátit žadateli: Nalezeno (302), přesunuto (301), dočasné přesměrování (307) a trvalé přesměrování (308).
Protokol přesměrování | Požadavek shody, HTTP, HTTPS.
Cílový hostitel | Vyberte název hostitele, na který chcete požadavek přesměrovat. Ponechte prázdné, pokud chcete zachovat příchozího hostitele.
Cílová cesta | Zadejte cestu, která se má použít v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí cestu.  
Řetězec dotazu | Zadejte řetězec dotazu použitý v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí řetězec dotazu. 
Fragment cíle | Definujte fragment, který se použije v přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí fragment. 

## <a name="url-rewrite"></a>Přepsání adresy URL

Pomocí této akce přepište cestu k žádosti, která je v cestě k původnímu zdroji.

### <a name="required-fields"></a>Povinná pole

Pole | Description 
------|------------
Zdrojový vzor | Definujte zdrojový vzor v cestě URL, která má být nahrazena. V současné době zdrojový vzor používá shodu na základě předpony. Pro vyhledání všech cest URL použijte lomítko ( **/** ) jako hodnotu zdrojového vzoru.
Cíl | Zadejte cílovou cestu, která se má použít při přepisování. Cílová cesta přepíše zdrojový vzor.
Zachovat neshodnou cestu | Pokud je nastaveno na **Ano**, zbývající cesta po zdrojovém vzoru se připojí k nové cílové cestě. 

## <a name="server-variable"></a>Proměnná serveru

### <a name="supported-variables"></a>Podporované proměnné

| Název proměnné | Description                                                  |
| -------------------------- | :----------------------------------------------------------- |
| socket_ip                  | IP adresa přímého připojení k hraničnímu zařízení Azure pro frontu. Pokud klient pro odeslání žádosti použil proxy server HTTP nebo nástroj pro vyrovnávání zatížení, hodnota SocketIp je IP adresa proxy serveru nebo nástroje pro vyrovnávání zatížení. |
| client_ip                  | IP adresa klienta, který vytvořil původní požadavek. Pokud v žádosti existovala hlavička X předaná-pro, pak je IP adresa klienta převzata ze stejné. |
| client_port                | Port IP klienta, který odeslal požadavek. |
| název hostitele                      | Název hostitele v požadavku od klienta. |
| geo_country                     | Určuje zemi nebo oblast původu žadatele prostřednictvím kódu země nebo oblasti. |
| http_method                | Metoda použitá pro vytvoření žádosti adresy URL Například GET nebo POST. |
| http_version               | Protokol žádosti. Obvykle HTTP/1.0, HTTP/1.1 nebo HTTP/2.0. |
| query_string               | Seznam párů proměnných/hodnot, které následují po "?" v požadované adrese URL. Příklad: v požadavku se *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* QUERY_STRING hodnota *ID = 123&title = Fabrikam* |
| request_scheme             | Schéma žádosti: http nebo HTTPS. |
| request_uri                | Úplný identifikátor URI původní žádosti (s argumenty). Příklad: v požadavku se *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* REQUEST_URI hodnota */article.aspx? ID = 123&title = Fabrikam* |
| server_port                | Port serveru, který přijal požadavek. |
| ssl_protocol    | Protokol vytvořeného připojení TLS. |
| url_path                   | Identifikuje konkrétní prostředek v hostiteli, ke kterému chce webový klient získat přístup. Toto je část identifikátoru URI požadavku bez argumentů. Příklad: v požadavku se *http://contoso.com:8080/article.aspx?id=123&title=fabrikam* hodnota uri_path */article.aspx* |

### <a name="server-variable-format"></a>Formát proměnné serveru    

**Formát:** {Variable: offset}, {proměnná: offset: Length}, {Variable}

### <a name="supported-server-variable-actions"></a>Podporované akce proměnné serveru

* Hlavička požadavku
* Hlavička odpovědi
* Řetězec dotazu na klíč mezipaměti
* Přepsání adresy URL
* Přesměrování adresy URL

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [sadě pravidel Stanard/Premium pro Azure](concept-rule-set.md).
* Přečtěte si další informace o [podmínkách shody sady pravidel](concept-rule-set-match-conditions.md).
