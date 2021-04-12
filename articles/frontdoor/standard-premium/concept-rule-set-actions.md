---
title: Konfigurace akcí sady pravidel služby Azure front-end Standard/Premium
description: Tento článek poskytuje seznam různých akcí, které můžete provést se sadou pravidel pro službu Azure front-dveří.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: yuajia
ms.openlocfilehash: e4698a1c1576d15042dd050e0123b83dba39a3e3
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064746"
---
# <a name="azure-front-door-standardpremium-preview-rule-set-actions"></a>Akce sady pravidel pro službu Azure front bran Standard/Premium (Preview)

> [!Note]
> Tato dokumentace je určena pro Azure front-end Standard/Premium (Preview). Hledáte informace o frontách Azure na začátku? Podívejte se [sem](../front-door-overview.md).

[Sada pravidel](concept-rule-set.md) služby Azure front-Premium Standard/Premium se skládá z pravidel s kombinací podmínek a akcí shody. Tento článek poskytuje podrobný popis akcí, které můžete použít v sadě Azure front-Only Rule Standard/Premium. Akce definuje chování, které se použije na typ požadavku, který určuje podmínky shody. V sadě pravidel front-end (Standard/Premium) Azure může pravidlo obsahovat až pět akcí.

> [!IMPORTANT]
> Služba Azure front-in standard/Premium (Preview) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

K dispozici jsou následující akce, které je možné použít v sadě pravidel služby Azure front-dveří.  

## <a name="cache-expiration"></a><a name="CacheExpiration"></a> Vypršení platnosti mezipaměti

Použijte akci **vypršení platnosti mezipaměti** k přepsání hodnoty TTL (Time to Live) koncového bodu pro požadavky, které určují podmínky shody pravidel.

> [!NOTE]
> Zdroje mohou určovat, že Neukládat konkrétní odpovědi do mezipaměti pomocí `Cache-Control` hlavičky s hodnotou `no-cache` , `private` nebo `no-store` . V těchto případech nebudou přední dveře obsah ukládat do mezipaměti a tato akce nebude mít žádný vliv.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-------|------------------|
| Chování mezipaměti | <ul><li>**Vynechat mezipaměť:** Obsah by neměl být uložen do mezipaměti. V části šablony ARM nastavte `cacheBehavior` vlastnost na `BypassCache` .</li><li>**Přepsat:** Hodnota TTL vrácená z vašeho zdroje je přepsána hodnotou zadanou v akci. Toto chování bude použito pouze v případě, že odpověď bude možné ukládat do mezipaměti. V části šablony ARM nastavte `cacheBehavior` vlastnost na `Override` .</li><li>**Nastavit, pokud chybí:** Pokud se z vašeho zdroje nevrátí žádná hodnota TTL, nastaví pravidlo hodnotu TTL na hodnotu zadanou v akci. Toto chování bude použito pouze v případě, že odpověď bude možné ukládat do mezipaměti. V části šablony ARM nastavte `cacheBehavior` vlastnost na `SetIfMissing` .</li></ul> |
| Doba uložení mezipaměti | Když je _chování mezipaměti_ nastavené na `Override` nebo `Set if missing` , musí tato pole určovat dobu trvání mezipaměti, která se má použít. Maximální doba trvání je 366 dní.<ul><li>V Azure Portal: zadejte dny, hodiny, minuty a sekundy.</li><li>V šablonách ARM: zadejte dobu trvání ve formátu `d.hh:mm:ss` . |

### <a name="example"></a>Příklad

V tomto příkladu přepíšeme vypršení platnosti mezipaměti na 6 hodin pro odpovídající žádosti, které ještě neurčují dobu trvání mezipaměti.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-expiration.png" alt-text="Snímek obrazovky portálu znázorňující akci vypršení platnosti mezipaměti":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a><a name="CacheKeyQueryString"></a> Řetězec dotazu na klíč mezipaměti

Použijte akci **řetězce dotazu na klíč mezipaměti** pro úpravu klíče mezipaměti založeného na řetězcích dotazů. Klíč mezipaměti je způsob, jakým přední dvířka identifikují jedinečné požadavky na ukládání do mezipaměti.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-------|------------------|
| Chování | <ul><li>**Zahrnout:** Řetězce dotazů zadané v parametrech jsou zahrnuté při vygenerování klíče mezipaměti. V části šablony ARM nastavte `queryStringBehavior` vlastnost na `Include` .</li><li>**Ukládat do mezipaměti každou jedinečnou adresu URL:** Každá jedinečná adresa URL má svůj vlastní klíč mezipaměti. V šablonách ARM použijte `queryStringBehavior` `IncludeAll` .</li><li>**Vyloučit:** Řetězce dotazů zadané v parametrech jsou vyloučené při vygenerování klíče mezipaměti. V části šablony ARM nastavte `queryStringBehavior` vlastnost na `Exclude` .</li><li>**Ignorovat řetězce dotazů:** Při generování klíče mezipaměti se neberou v úvahu řetězce dotazů. V části šablony ARM nastavte `queryStringBehavior` vlastnost na `ExcludeAll` .</li></ul>  |
| Parametry | Seznam názvů parametrů řetězce dotazu oddělený čárkami |

### <a name="example"></a>Příklad

V tomto příkladu upravujeme klíč mezipaměti, aby zahrnoval parametr řetězce dotazu s názvem `customerId` .

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/cache-key-query-string.png" alt-text="Snímek obrazovky portálu zobrazující akci řetězce dotazu na klíč mezipaměti":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a><a name="ModifyRequestHeader"></a> Upravit hlavičku požadavku

Pomocí akce **upravit hlavičku žádosti** můžete v žádosti upravit záhlaví, když se pošle k vašemu původu.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-------|------------------|
| Operátor | <ul><li>**Připojit:** Zadaná hlavička se přidá do žádosti se zadanou hodnotou. Pokud hlavička již existuje, hodnota se připojí k existující hodnotě záhlaví pomocí zřetězení řetězců. Nepřidaly se žádné oddělovače. V šablonách ARM použijte `headerAction` `Append` .</li><li>**Přepsat:** Zadaná hlavička se přidá do žádosti se zadanou hodnotou. Pokud hlavička již existuje, zadaná hodnota přepíše existující hodnotu. V šablonách ARM použijte `headerAction` `Overwrite` .</li><li>**Odstranit:** Pokud je k dispozici hlavička uvedená v pravidle, hlavička se odstraní z požadavku. V šablonách ARM použijte `headerAction` `Delete` .</li></ul> |
| Název hlavičky | Název záhlaví, které chcete upravit. |
| Hodnota hlavičky | Hodnota, která má být připojena nebo přepsána. |

### <a name="example"></a>Příklad

V tomto příkladu přidáme hodnotu `AdditionalValue` k `MyRequestHeader` hlavičce požadavku. Pokud počátek nastavil hlavičku odpovědi na hodnotu `ValueSetByClient` , potom po použití této akce bude mít hlavička žádosti hodnotu `ValueSetByClientAdditionalValue` .

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-request-header.png" alt-text="Snímek obrazovky portálu znázorňující akci pro úpravu žádosti v hlavičce":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a><a name="ModifyResponseHeader"></a> Upravit hlavičku odpovědi

Použijte akci **upravit hlavičku odpovědi** pro úpravu hlaviček, které se nacházejí v odpovědích předtím, než se vrátí vašim klientům.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|-------|------------------|
| Operátor | <ul><li>**Připojit:** Zadaná hlavička se přidá do odpovědi se zadanou hodnotou. Pokud hlavička již existuje, hodnota se připojí k existující hodnotě záhlaví pomocí zřetězení řetězců. Nepřidaly se žádné oddělovače. V šablonách ARM použijte `headerAction` `Append` .</li><li>**Přepsat:** Zadaná hlavička se přidá do odpovědi se zadanou hodnotou. Pokud hlavička již existuje, zadaná hodnota přepíše existující hodnotu. V šablonách ARM použijte `headerAction` `Overwrite` .</li><li>**Odstranit:** Pokud je k dispozici hlavička uvedená v pravidle, hlavička se odstraní z odpovědi.  V šablonách ARM použijte `headerAction` `Delete` .</li></ul> |
| Název hlavičky | Název záhlaví, které chcete upravit. |
| Hodnota hlavičky | Hodnota, která má být připojena nebo přepsána. |

### <a name="example"></a>Příklad

V tomto příkladu odstraníme hlavičku s názvem `X-Powered-By` z odpovědí předtím, než se vrátí klientovi.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/modify-response-header.png" alt-text="Snímek obrazovky portálu znázorňující akci pro úpravu hlavičky odpovědi":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a><a name="UrlRedirect"></a> Přesměrování adresy URL

Použijte akci **přesměrování adresy URL** pro přesměrování klientů na novou adresu URL. Klientům se pošle odezva na přesměrování z předních dveří.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|----------|------------------|
| Typ přesměrování | Typ odpovědi, který se má vrátit žadateli. <ul><li>V Azure Portal: Nalezeno (302), přesunuto (301), dočasné přesměrování (307), trvalé přesměrování (308).</li><li>V šablonách ARM: `Found` , `Moved` , `TemporaryRedirect` , `PermanentRedirect`</li></ul> |
| Protokol přesměrování | <ul><li>V Azure Portal: `Match Request` , `HTTP` , `HTTPS`</li><li>V šablonách ARM: `MatchRequest` , `Http` , `Https`</li></ul> |
| Cílový hostitel | Název hostitele, na který chcete požadavek přesměrovat. Ponechte prázdné, pokud chcete zachovat příchozího hostitele. |
| Cílová cesta | Cesta, která se má použít v přesměrování Zahrňte úvodní `/` . Ponechte prázdné, pokud chcete zachovat příchozí cestu. |
| Řetězec dotazu | Řetězec dotazu použitý v přesměrování Nezahrnujte úvodní `?` . Ponechte prázdné, pokud chcete zachovat příchozí řetězec dotazu. |
| Fragment cíle | Fragment, který se má použít při přesměrování. Ponechte prázdné, pokud chcete zachovat příchozí fragment. |

### <a name="example"></a>Příklad

V tomto příkladu přesměrujeme požadavek na `https://contoso.com/exampleredirection?clientIp={client_ip}` a zároveň zachováte fragment. Je použito dočasné přesměrování HTTP (307). IP adresa klienta se používá místo `{client_ip}` tokenu v rámci adresy URL pomocí `client_ip` [proměnné serveru](#server-variables).

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-redirect.png" alt-text="Snímek obrazovky portálu znázorňující akci přesměrování adresy URL":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a><a name="UrlRewrite"></a> Přepsání adresy URL

Pomocí akce **přepisu adresy URL** přepište cestu k žádosti, která je v cestě k původnímu zdroji.

### <a name="properties"></a>Vlastnosti

| Vlastnost | Podporované hodnoty |
|----------|------------------|
| Zdrojový vzor | Definujte zdrojový vzor v cestě URL, která má být nahrazena. V současné době zdrojový vzor používá shodu na základě předpony. Pro vyhledání všech cest URL použijte lomítko ( `/` ) jako hodnotu zdrojového vzoru. |
| Cíl | Zadejte cílovou cestu, která se má použít při přepisování. Cílová cesta přepíše zdrojový vzor. |
| Zachovat neshodnou cestu | Pokud je nastaveno na _Ano_, zbývající cesta po zdrojovém vzoru se připojí k nové cílové cestě. |

### <a name="example"></a>Příklad

V tomto příkladu přepíšeme všechny požadavky na cestu `/redirection` a nezachováme zbytek cesty.

# <a name="portal"></a>[Azure Portal](#tab/portal)

:::image type="content" source="../media/concept-rule-set-actions/url-rewrite.png" alt-text="Snímek obrazovky portálu znázorňující akci přepsání adresy URL":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="server-variables"></a>Serverové proměnné

Proměnné serveru sady pravidel poskytují přístup k strukturovaným informacím o žádosti. Proměnné serveru můžete použít k dynamické změně hlaviček žádosti nebo odpovědi nebo cest pro přepis adres URL nebo řetězců dotazů, například při načtení nové stránky nebo při publikování formuláře.

### <a name="supported-variables"></a>Podporované proměnné

| Název proměnné    | Description                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | IP adresa přímého připojení k hraničnímu zařízení Azure pro frontu. Pokud klient pro odeslání žádosti použil proxy server HTTP nebo nástroj pro vyrovnávání zatížení, hodnota `socket_ip` je IP adresa proxy serveru nebo nástroje pro vyrovnávání zatížení.                                                                      |
| `client_ip`      | IP adresa klienta, který vytvořil původní požadavek. Pokud `X-Forwarded-For` v žádosti existovala hlavička, IP adresa klienta se vybrala z hlavičky.                                                                                                               |
| `client_port`    | Port IP klienta, který odeslal požadavek.                                                                                                                                                                                                                                          |
| `hostname`       | Název hostitele v požadavku od klienta.                                                                                                                                                                                                                                             |
| `geo_country`    | Určuje zemi nebo oblast původu žadatele prostřednictvím kódu země nebo oblasti.                                                                                                                                                                                                       |
| `http_method`    | Metoda použitá k vytvoření žádosti adresy URL, například `GET` nebo `POST` .                                                                                                                                                                                                                         |
| `http_version`   | Protokol žádosti. Obvykle `HTTP/1.0` , `HTTP/1.1` nebo `HTTP/2.0` .                                                                                                                                                                                                                      |
| `query_string`   | Seznam párů proměnných/hodnot, které následují po "?" v požadované adrese URL.<br />Například v požadavku `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `query_string` bude hodnota `id=123&title=fabrikam` .                                                      |
| `request_scheme` | Schéma žádosti: `http` nebo `https` .                                                                                                                                                                                                                                                    |
| `request_uri`    | Úplný identifikátor URI původní žádosti (s argumenty).<br />Například v požadavku `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `request_uri` bude hodnota `/article.aspx?id=123&title=fabrikam` .                                                                     |
| `ssl_protocol`   | Protokol vytvořeného připojení TLS.                                                                                                                                                                                                                                            |
| `server_port`    | Port serveru, který přijal požadavek.                                                                                                                                                                                                                                           |
| `url_path`       | Identifikuje konkrétní prostředek v hostiteli, ke kterému chce webový klient získat přístup. Toto je část identifikátoru URI požadavku bez argumentů.<br />Například v požadavku `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` `uri_path` bude hodnota `/article.aspx` . |

### <a name="server-variable-format"></a>Formát proměnné serveru    

Proměnné serveru lze zadat v následujících formátech:

* `{variable}`: Zahrňte celou proměnnou serveru. Například pokud je IP adresa klienta, token se `111.222.333.444` `{client_ip}` vyhodnotí na `111.222.333.444` .
* `{variable:offset}`: Zahrňte proměnnou serveru po určitém posunu, až do konce proměnné. Posun je založen na nule. Například pokud je IP adresa klienta, token se `111.222.333.444` `{client_ip:3}` vyhodnotí na `.222.333.444` .
* `{variable:offset:length}`: Zahrňte proměnnou serveru po určitém posunu až po určenou délku. Posun je založen na nule. Například pokud je IP adresa klienta, token se `111.222.333.444` `{client_ip:4:3}` vyhodnotí na `222` .

### <a name="supported-actions"></a>Podporované akce

Proměnné serveru jsou podporované na následujících akcích:

* Řetězec dotazu na klíč mezipaměti
* Upravit hlavičku požadavku
* Upravit hlavičku odpovědi
* Přesměrování adresy URL
* Přepsání adresy URL

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [sadě pravidel Premium pro Azure front-end Standard/Premium](concept-rule-set.md).
* Přečtěte si další informace o [podmínkách shody sady pravidel](concept-rule-set-match-conditions.md).
