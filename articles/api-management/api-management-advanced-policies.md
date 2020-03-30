---
title: Rozšířené zásady správy rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si o pokročilých zásadách, které jsou k dispozici pro použití ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: c8ef481fe277d6451923da828f0e7473354c24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266166"
---
# <a name="api-management-advanced-policies"></a>Pokročilé zásady služby API Management

Toto téma obsahuje odkaz na následující zásady správy rozhraní API. Informace o přidávání a konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a>Pokročilé zásady

-   [Tok řízení](api-management-advanced-policies.md#choose) - Podmíněně použije příkazy zásad na základě výsledků vyhodnocení logických [výrazů](api-management-policy-expressions.md).
-   [Předávání požadavků](#ForwardRequest) – předá požadavek back-endové službě.
-   [Omezit souběžnost](#LimitConcurrency) – zabrání uzavřené zásady provádění o více než zadaný počet požadavků najednou.
-   [Protokolovat do centra událostí](#log-to-eventhub) – odesílá zprávy v zadaném formátu do centra událostí definovaného entitou Protokolování.
-   [Mock odpověď](#mock-response) - Přeruší spuštění kanálu a vrátí zesměšňované odpovědi přímo volajícímu.
-   [Opakování](#Retry) - opakování spuštění uzavřených příkazů zásad, pokud a dokud není splněna podmínka. Spuštění se bude opakovat v určených časových intervalech a až do zadaného počtu opakování.
-   [Návratová odpověď](#ReturnResponse) - Přeruší spuštění kanálu a vrátí zadanou odpověď přímo volajícímu.
-   [Odeslat jednosměrný požadavek](#SendOneWayRequest) - Odešle požadavek na zadanou adresu URL bez čekání na odpověď.
-   [Odeslat požadavek](#SendRequest) - Odešle požadavek na zadanou adresu URL.
-   [Nastavení http proxy -](#SetHttpProxy) Umožňuje směrovat předané požadavky prostřednictvím proxy serveru HTTP.
-   [Set request method](#SetRequestMethod) - Umožňuje změnit metodu HTTP pro požadavek.
-   [Nastavit stavový kód](#SetStatus) - Změní stavový kód HTTP na zadanou hodnotu.
-   [Set variable](api-management-advanced-policies.md#set-variable) - Zachová hodnotu v pojmenované [proměnné kontextu](api-management-policy-expressions.md#ContextVariables) pro pozdější přístup.
-   [Trasování](#Trace) – přidá vlastní trasování do výstupu [nástroje API Inspector,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) telemetry Application Insights a diagnostické protokoly.
-   [Čekání](#Wait) - Čeká na přiložené [odeslat požadavek](api-management-advanced-policies.md#SendRequest), [Získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey)nebo [Řízení toku](api-management-advanced-policies.md#choose) zásady dokončit před pokračováním.

## <a name="control-flow"></a><a name="choose"></a>Řídicí tok

Zásada `choose` použije přiložené příkazy zásad na základě výsledku vyhodnocení logických výrazů, podobně jako konstrukce if-then-else nebo switch v programovacím jazyce.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a>Prohlášení o zásadách

```xml
<choose>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <when condition="Boolean expression | Boolean constant">
        <!— one or more policy statements to be applied if the above condition is true  -->
    </when>
    <otherwise>
        <!— one or more policy statements to be applied if none of the above conditions are true  -->
</otherwise>
</choose>
```

Zásady toku řízení musí `<when/>` obsahovat alespoň jeden prvek. Prvek `<otherwise/>` je volitelný. Podmínky `<when/>` v prvcích jsou vyhodnocovány v pořadí podle jejich vzhledu v rámci zásady. Budou použity příkazy zásad `<when/>` uzavřené v rámci `true` prvního prvku s atributem condition equals. Zásady uzavřené `<otherwise/>` v rámci prvku, pokud je k `<when/>` dispozici, budou `false`použity, pokud jsou všechny atributy podmínky prvku .

### <a name="examples"></a>Příklady

#### <a name="example"></a><a name="ChooseExample"></a>Příklad

Následující příklad ukazuje [zásadu set-variable](api-management-advanced-policies.md#set-variable) a dvě zásady toku řízení.

Zásada nastavené proměnné je ve vstupníčásti a vytvoří logickou `isMobile` [kontextovou](api-management-policy-expressions.md#ContextVariables) proměnnou, která je nastavena na hodnotu true, pokud hlavička `User-Agent` požadavku obsahuje text `iPad` nebo `iPhone`.

První zásada toku řízení je také v příchozí části a podmíněně použije jednu ze dvou [zásad parametru řetězce protokolu Set v](api-management-transformation-policies.md#SetQueryStringParameter) závislosti na hodnotě proměnné `isMobile` kontextu.

Druhá zásada toku řízení je v části odchozí a podmíněně použije `isMobile` zásadu `true` [Převést XML na JSON,](api-management-transformation-policies.md#ConvertXMLtoJSON) pokud je nastavena na .

```xml
<policies>
    <inbound>
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <set-query-parameter name="mobile" exists-action="override">
                    <value>true</value>
                </set-query-parameter>
            </when>
            <otherwise>
                <set-query-parameter name="mobile" exists-action="override">
                    <value>false</value>
                </set-query-parameter>
            </otherwise>
        </choose>
    </inbound>
    <outbound>
        <base />
        <choose>
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>
            </when>
        </choose>
    </outbound>
</policies>
```

#### <a name="example"></a>Příklad

Tento příklad ukazuje, jak provádět filtrování obsahu odebráním datových prvků z `Starter` odpovědi přijaté ze služby back-end při používání produktu. Ukázka konfigurace a používání těchto zásad najdete v [tématu Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 34:30. Začněte v 31:50 a podívejte se na přehled [rozhraní API the Dark Sky Forecast API](https://developer.forecast.io/) použitého pro tuto ukázku.

```xml
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->
<choose>
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">
    <set-body>@{
        var response = context.Response.Body.As<JObject>();
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {
          response.Property (key).Remove ();
        }
        return response.ToString();
      }
    </set-body>
  </when>
</choose>
```

### <a name="elements"></a>Elementy

| Element   | Popis                                                                                                                                                                                                                                                               | Požaduje se |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Zvolte    | Kořenový prvek.                                                                                                                                                                                                                                                             | Ano      |
| Kdy      | Podmínka pro použití `if` pro `ifelse` nebo `choose` části zásad. Pokud `choose` zásada `when` obsahuje více oddílů, jsou vyhodnocovány postupně. Jakmile `condition` a when element vyhodnotí `true` `when` , žádné další podmínky jsou vyhodnoceny. | Ano      |
| Jinak | Obsahuje fragment zásad, který má být použit, pokud žádná z `when` podmínek nevyhodnotí `true`.                                                                                                                                                                               | Ne       |

### <a name="attributes"></a>Atributy

| Atribut                                              | Popis                                                                                               | Požaduje se |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| condition="Logický výraz &#124; logická konstanta" | Logický výraz nebo konstanta vyhodnocovat při vyhodnocení obsahující `when` příkaz zásady. | Ano      |

### <a name="usage"></a><a name="ChooseUsage"></a>Použití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="forward-request"></a><a name="ForwardRequest"></a>Žádost o předání

Zásady `forward-request` předá příchozí požadavek back-endové službě zadané v [kontextu](api-management-policy-expressions.md#ContextVariables)požadavku . Adresa URL back-endové služby je určena v [nastavení](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) rozhraní API a lze ji změnit pomocí zásad [služby set back-end.](api-management-transformation-policies.md)

> [!NOTE]
> Odebrání této zásady má za následek, že požadavek není předán do back-endové služby a zásady v části odchozí jsou vyhodnoceny okamžitě po úspěšném dokončení zásad v příchozí části.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

Následující zásady na úrovni rozhraní API předá všechny požadavky rozhraní API do back-endové služby s časovým intervalem 60 sekund.

```xml
<!-- api level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="60"/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Příklad

Tato zásada úrovně `base` operace používá prvek ke zdědění zásadback-endu z oboru nadřazené úrovně rozhraní API.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <base/>
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Příklad

Tato zásada úrovně operace explicitně předává všechny požadavky na back-endovou službu s časovým časem 120 a nedědí zásady back-endu na úrovni nadřazené rozhraní API. Pokud back-endová služba odpoví kódem stavu chyby od 400 do 599 včetně, bude spuštěna část [při chybě.](api-management-error-handling-policies.md)

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120" fail-on-error-status-code="true" />
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Příklad

Tato zásada úrovně operace nepředává požadavky back-endové službě.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <!-- no forwarding to backend -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

### <a name="elements"></a>Elementy

| Element         | Popis   | Požaduje se |
| --------------- | ------------- | -------- |
| žádost o předávání | Kořenový prvek. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut                                     | Popis                                                                                                                                                                                                                                                                                                    | Požaduje se | Výchozí |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout="celé číslo"                             | Doba v sekundách čekání na hlavičky odpovědi HTTP, které mají být vráceny back-endové služby před vypršení min. Minimální hodnota je 0 sekund. Hodnoty větší než 240 sekund nemusí být dodrženy, protože základní síťová infrastruktura může po uplynutí této doby vynechat nečinná připojení. | Ne       | Žádný    |
| follow-redirects="false &#124; true"          | Určuje, zda přesměrování ze služby back-end následuje brána nebo vráceny volajícímu.                                                                                                                                                                                                    | Ne       | false (nepravda)   |
| buffer-request-body="false &#124; true"       | Pokud je nastaven požadavek "true", je uložen do vyrovnávací paměti a bude znovu použit při [opakování](api-management-advanced-policies.md#Retry).                                                                                                                                                                                               | Ne       | false (nepravda)   |
| fail-on-error-status-code="false &#124; true" | Pokud je nastavena na true triggers [on-error](api-management-error-handling-policies.md) sekce pro kódy odezvy v rozsahu od 400 do 599 včetně.                                                                                                                                                                      | Ne       | false (nepravda)   |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** back-end
-   **Obory zásad:** všechny obory

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a>Omezit souběžnost

Zásady `limit-concurrency` brání uzavřené zásady provádění o více než zadaný počet požadavků v libovolném okamžiku. Při překročení tohoto počtu se nové požadavky okamžitě nezdaří se stavovým kódem 429 Příliš mnoho požadavků.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a>Prohlášení o zásadách

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

Následující příklad ukazuje, jak omezit počet požadavků předaných do back-endu na základě hodnoty kontextové proměnné.

```xml
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Element           | Popis   | Požaduje se |
| ----------------- | ------------- | -------- |
| limit-souběžnost | Kořenový prvek. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                                                        | Požaduje se | Výchozí |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Řetězec. Výraz povolen. Určuje obor souběžnosti. Mohou být sdíleny více zásad. | Ano      | Není dostupné.     |
| maximální počet | Celé číslo Určuje maximální počet požadavků, které mohou vstoupit do zásady.           | Ano      | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a>Protokolovat do centra událostí

Zásada `log-to-eventhub` odesílá zprávy v zadaném formátu do centra událostí definovaného entitou Logger. Jak již název napovídá, zásada se používá pro ukládání informací o kontextu vybrané žádosti nebo odpovědi pro online nebo offline analýzu.

> [!NOTE]
> Podrobný průvodce konfigurací centra událostí a protokolováním událostí najdete v tématu [Protokolování událostí správy rozhraní API pomocí centra událostí Azure](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Příklad

Libovolný řetězec lze použít jako hodnotu, která má být zaznamenána v centru událostí. V tomto příkladu jsou do protokolování centra událostí registrovaného `contoso-logger` s ID zaznamenány datum a čas, název služby nasazení, ID požadavku, IP adresa a název operace pro všechna příchozí volání.

```xml
<policies>
  <inbound>
    <log-to-eventhub logger-id ='contoso-logger'>
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )
    </log-to-eventhub>
  </inbound>
  <outbound>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Element         | Popis                                                                     | Požaduje se |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| log-to-eventhub | Kořenový prvek. Hodnota tohoto prvku je řetězec pro protokolování do centra událostí. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut     | Popis                                                               | Požaduje se                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| logger-id     | ID loggeru registrovaného ve službě API Management.         | Ano                                                                  |
| oddíl-id  | Určuje index oddílu, do kterého jsou odesílány zprávy.             | Nepovinný parametr. Tento atribut nelze použít, pokud `partition-key` je použit. |
| klíč oddílu | Určuje hodnotu použitou pro přiřazení oddílu při odeslání zpráv. | Nepovinný parametr. Tento atribut nelze použít, pokud `partition-id` je použit.  |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="mock-response"></a><a name="mock-response"></a>Falešná odpověď

`mock-response`, jak název napovídá, se používá k zesměšňovat api a operace. Přeruší normální spuštění kanálu a vrátí falešnou odpověď volajícímu. Zásada se vždy snaží vrátit odpovědi s nejvyšší věrností. Upřednostňuje příklady obsahu odpovědí, kdykoli jsou k dispozici. Generuje ukázkové odpovědi ze schémat, pokud jsou k dispozici schémata a příklady nejsou. Pokud nejsou nalezeny žádné příklady nebo schémata, jsou vráceny odpovědi bez obsahu.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<mock-response status-code="code" content-type="media type"/>

```

### <a name="examples"></a>Příklady

```xml
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>
```

### <a name="elements"></a>Elementy

| Element       | Popis   | Požaduje se |
| ------------- | ------------- | -------- |
| falešná odpověď | Kořenový prvek. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut    | Popis                                                                                           | Požaduje se | Výchozí |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| stavový kód  | Určuje kód stavu odpovědi a slouží k výběru odpovídajícího příkladu nebo schématu.                 | Ne       | 200     |
| typ obsahu | Určuje `Content-Type` hodnotu hlavičky odpovědi a slouží k výběru odpovídajícího příkladu nebo schématu. | Ne       | Žádný    |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, při chybě

-   **Obory zásad:** všechny obory

## <a name="retry"></a><a name="Retry"></a>Opakovat

`retry` Zásady provede své podřízené zásady jednou a potom `condition` `false` opakuje jejich `count` provádění, dokud opakování se stane nebo opakování je vyčerpán.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<retry
    condition="boolean expression or literal"
    count="number of retry attempts"
    interval="retry interval in seconds"
    max-interval="maximum retry interval in seconds"
    delta="retry interval delta in seconds"
    first-fast-retry="boolean expression or literal">
        <!-- One or more child policies. No restrictions -->
</retry>

```

### <a name="example"></a>Příklad

V následujícím příkladu je předávání požadavků opakováno až desetkrát pomocí algoritmu exponenciálního opakování. Vzhledem k tomu, `first-fast-retry` že je nastavena na false, všechny pokusy o opakování jsou předmětem algoritmu exponenciálního opakování.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request buffer-request-body="true" />
</retry>

```

### <a name="elements"></a>Elementy

| Element | Popis                                                         | Požaduje se |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Kořenový prvek. Může obsahovat jakékoli jiné zásady jako podřízené prvky. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut        | Popis                                                                                                                                           | Požaduje se | Výchozí |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| Podmínka        | Logický literál nebo [výraz](api-management-policy-expressions.md) určující, zda má`false`být opakování`true`zastaveno ( ) nebo pokračovat ( ).      | Ano      | Není dostupné.     |
| count            | Kladné číslo určující maximální počet pokusů o pokus.                                                                                | Ano      | Není dostupné.     |
| interval         | Kladné číslo v sekundách určující interval čekání mezi pokusy o opakování.                                                                 | Ano      | Není dostupné.     |
| maximální interval     | Kladné číslo v sekundách určující maximální čekací interval mezi pokusy o opakování. Používá se k implementaci exponenciálního algoritmu opakování. | Ne       | Není dostupné.     |
| Delta            | Kladné číslo v sekundách určující přírůstek intervalu čekání. Používá se k implementaci lineárních a exponenciálních algoritmů opakování.             | Ne       | Není dostupné.     |
| první rychlý pokus | Pokud je `true` nastavena na , první pokus o opakování se provádí okamžitě.                                                                                  | Ne       | `false` |

> [!NOTE]
> Pokud `interval` je zadán pouze **pevný** interval opakování jsou prováděny.
> Pokud jsou `interval` `delta` zadány pouze a jsou zadány, lineární **interval** opakování algoritmus, kde `interval + (count - 1)*delta`čekací doba mezi opakování se vypočítá podle následujícího vzorce - .
> `interval`Když `max-interval` je `delta` zadán algoritmus opakování **exponenciálního** intervalu, kde čekací doba mezi opakovanými pokusy exponenciálně `interval` roste `max-interval` z hodnoty na `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`hodnotu podle následujícího vzorce - .

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Všimněte si, že podřízené zásady omezení použití budou zděděny touto zásadou.

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="return-response"></a><a name="ReturnResponse"></a>Odpověď na vrácení

Zásada `return-response` přeruší spuštění kanálu a vrátí volajícímu výchozí nebo vlastní odpověď. Výchozí odpověď `200 OK` je bez těla. Vlastní odpověď lze zadat pomocí kontextové proměnné nebo příkazů zásad. Pokud jsou k dispozici obě, odpověď obsažená v proměnné kontextu je upravena příkazy zásad před vrácením volajícímu.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Příklad

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elementy

| Element         | Popis                                                                               | Požaduje se |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| návratová odpověď | Kořenový prvek.                                                                             | Ano      |
| set-header      | Příkaz zásad [set-header.](api-management-transformation-policies.md#SetHTTPheader) | Ne       |
| set-tělo        | Prohlášení zásad [set-body.](api-management-transformation-policies.md#SetBody)         | Ne       |
| stav nastavení      | Prohlášení o [zásadách nastaveného stavu.](api-management-advanced-policies.md#SetStatus)           | Ne       |

### <a name="attributes"></a>Atributy

| Atribut              | Popis                                                                                                                                                                          | Požaduje se  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| název proměnné odpovědi | Název kontextové proměnné odkazované například z upstream [send-request](api-management-advanced-policies.md#SendRequest) policy `Response` a obsahující objekt | Nepovinný parametr. |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a>Odeslat jednosměrný požadavek

Zásada `send-one-way-request` odešle zadaný požadavek na zadanou adresu URL bez čekání na odpověď.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<send-one-way-request mode="new | copy">
  <url>...</url>
  <method>...</method>
  <header name="" exists-action="override | skip | append | delete">...</header>
  <body>...</body>
  <authentication-certificate thumbprint="thumbprint" />
</send-one-way-request>

```

### <a name="example"></a>Příklad

Tato ukázková zásada ukazuje `send-one-way-request` příklad použití zásady k odeslání zprávy do chatovací místnosti Slack, pokud je kód odpovědi HTTP větší nebo roven 500. Další informace o této ukázce najdete [v tématu Používání externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element                    | Popis                                                                                                 | Požaduje se                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| send-one-way-request       | Kořenový prvek.                                                                                               | Ano                             |
| url                        | Adresa URL požadavku.                                                                                     | Ne, pokud mode=copy; jinak ano. |
| method                     | Metoda HTTP pro požadavek.                                                                            | Ne, pokud mode=copy; jinak ano. |
| záhlaví                     | Hlavička požadavku. Použijte více prvků záhlaví pro více hlaviček požadavku.                                  | Ne                              |
| text                       | Tělo požadavku.                                                                                           | Ne                              |
| ověřovací certifikát | [Certifikát, který se má použít pro ověřování klienta](api-management-authentication-policies.md#ClientCertificate) | Ne                              |

### <a name="attributes"></a>Atributy

| Atribut     | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Požaduje se | Výchozí  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="řetězec" | Určuje, zda se jedná o nový požadavek nebo kopii aktuálního požadavku. V odchozím režimu mode=copy neinicializuje tělo požadavku.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Ne       | Nová      |
| jméno          | Určuje název hlavičky, který se má nastavit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ano      | Není dostupné.      |
| existuje-akce | Určuje, jakou akci je třeba provést, když je záhlaví již zadáno. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> - přepsání - nahradí hodnotu existující hlavičky.<br />- přeskočit - nenahradí existující hodnotu záhlaví.<br />- append - připojí hodnotu k existující hodnotě záhlaví.<br />- delete - odstraní hlavičku z požadavku.<br /><br /> Pokud je `override` nastaveno zařazení více položek se stejným názvem, bude záhlaví nastaveno podle všech položek (které budou uvedeny vícekrát); ve výsledku budou nastaveny pouze uvedené hodnoty. | Ne       | override |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="send-request"></a><a name="SendRequest"></a>Odeslat požadavek

Zásada `send-request` odešle zadaný požadavek na zadanou adresu URL a nečeká déle než nastavená hodnota časového času.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error
="false|true">
  <set-url>...</set-url>
  <set-method>...</set-method>
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>
  <set-body>...</set-body>
  <authentication-certificate thumbprint="thumbprint" />
</send-request>

```

### <a name="example"></a>Příklad

Tento příklad ukazuje jeden způsob, jak ověřit referenční token s autorizačním serverem. Další informace o této ukázce najdete [v tématu Používání externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
        <!-- Check active property in response -->
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
            <!-- Return 401 Unauthorized with http-problem payload -->
            <return-response>
                <set-status code="401" reason="Unauthorized" />
                <set-header name="WWW-Authenticate" exists-action="override">
                    <value>Bearer error="invalid_token"</value>
                </set-header>
            </return-response>
        </when>
    </choose>
  <base />
</inbound>

```

### <a name="elements"></a>Elementy

| Element                    | Popis                                                                                                 | Požaduje se                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| odeslat žádost               | Kořenový prvek.                                                                                               | Ano                             |
| url                        | Adresa URL požadavku.                                                                                     | Ne, pokud mode=copy; jinak ano. |
| method                     | Metoda HTTP pro požadavek.                                                                            | Ne, pokud mode=copy; jinak ano. |
| záhlaví                     | Hlavička požadavku. Použijte více prvků záhlaví pro více hlaviček požadavku.                                  | Ne                              |
| text                       | Tělo požadavku.                                                                                           | Ne                              |
| ověřovací certifikát | [Certifikát, který se má použít pro ověřování klienta](api-management-authentication-policies.md#ClientCertificate) | Ne                              |

### <a name="attributes"></a>Atributy

| Atribut                       | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Požaduje se | Výchozí  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| mode="řetězec"                   | Určuje, zda se jedná o nový požadavek nebo kopii aktuálního požadavku. V odchozím režimu mode=copy neinicializuje tělo požadavku.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | Ne       | Nová      |
| response-variable-name="řetězec" | Název proměnné kontextu, která obdrží objekt odpovědi. Pokud proměnná neexistuje, bude vytvořena po úspěšném provedení zásady a [`context.Variable`](api-management-policy-expressions.md#ContextVariables) bude přístupná prostřednictvím kolekce.                                                                                                                                                                                                                                                                                                                          | Ano      | Není dostupné.      |
| timeout="celé číslo"               | Časový interval v sekundách před nezdaří volání adresy URL.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | Ne       | 60       |
| chyba ignorování                    | Pokud true a požadavek má za následek chybu:<br /><br /> - Pokud byl zadán název proměnné odpovědi, bude obsahovat hodnotu null.<br />- Pokud nebyl zadán název proměnné odpovědi, kontext. Požadavek nebude aktualizován.                                                                                                                                                                                                                                                                                                                                                                                   | Ne       | false (nepravda)    |
| jméno                            | Určuje název hlavičky, který se má nastavit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Ano      | Není dostupné.      |
| existuje-akce                   | Určuje, jakou akci je třeba provést, když je záhlaví již zadáno. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> - přepsání - nahradí hodnotu existující hlavičky.<br />- přeskočit - nenahradí existující hodnotu záhlaví.<br />- append - připojí hodnotu k existující hodnotě záhlaví.<br />- delete - odstraní hlavičku z požadavku.<br /><br /> Pokud je `override` nastaveno zařazení více položek se stejným názvem, bude záhlaví nastaveno podle všech položek (které budou uvedeny vícekrát); ve výsledku budou nastaveny pouze uvedené hodnoty. | Ne       | override |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a>Nastavení http proxy serveru

Zásada `proxy` umožňuje směrovat požadavky předané do back-endů prostřednictvím proxy serveru HTTP. Mezi bránou a proxy serverem je podporovánpouze protokol HTTP (nikoli protokol HTTPS). Pouze základní ověřování a ověřování NTLM.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Příklad

Všimněte si použití [vlastností](api-management-howto-properties.md) jako hodnot uživatelského jména a hesla, abyste zabránili ukládání citlivých informací v dokumentu zásad.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

| Element | Popis  | Požaduje se |
| ------- | ------------ | -------- |
| proxy   | Kořenový prvek | Ano      |

### <a name="attributes"></a>Atributy

| Atribut         | Popis                                            | Požaduje se | Výchozí |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| url="řetězec"      | Adresa URL proxy http://host:portve formě .             | Ano      | Není dostupné.     |
| uživatelské jméno="řetězec" | Uživatelské jméno, které má být použito pro ověřování pomocí proxy serveru. | Ne       | Není dostupné.     |
| password="řetězec" | Heslo, které má být použito pro ověřování pomocí proxy serveru. | Ne       | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny obory

## <a name="set-request-method"></a><a name="SetRequestMethod"></a>Nastavit metodu požadavku

Zásada `set-method` umožňuje změnit metodu požadavku HTTP pro požadavek.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Příklad

Tato ukázková zásada, která používá zásadu, `set-method` ukazuje příklad odeslání zprávy do chatovací místnosti Slack, pokud je kód odpovědi HTTP větší nebo roven 500. Další informace o této ukázce najdete [v tématu Používání externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element    | Popis                                                       | Požaduje se |
| ---------- | ----------------------------------------------------------------- | -------- |
| set-metoda | Kořenový prvek. Hodnota prvku určuje metodu HTTP. | Ano      |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, při chybě

-   **Obory zásad:** všechny obory

## <a name="set-status-code"></a><a name="SetStatus"></a>Nastavení stavového kódu

Zásada `set-status` nastaví stavový kód HTTP na zadanou hodnotu.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Příklad

Tento příklad ukazuje, jak vrátit odpověď 401, pokud je token autorizace neplatný. Další informace najdete [v tématu Používání externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>

```

### <a name="elements"></a>Elementy

| Element    | Popis   | Požaduje se |
| ---------- | ------------- | -------- |
| stav nastavení | Kořenový prvek. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut       | Popis                                                | Požaduje se | Výchozí |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| kód="celé číslo"  | Stavový kód HTTP, který chcete vrátit.                            | Ano      | Není dostupné.     |
| reason="řetězec" | Popis důvodu vrácení stavového kódu. | Ano      | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** odchozí, back-end, chyba
-   **Obory zásad:** všechny obory

## <a name="set-variable"></a><a name="set-variable"></a>Nastavit proměnnou

Zásada `set-variable` deklaruje [kontextovou](api-management-policy-expressions.md#ContextVariables) proměnnou a přiřazuje jí hodnotu určenou prostřednictvím [výrazu](api-management-policy-expressions.md) nebo literálu řetězce. Pokud výraz obsahuje literál, bude převeden na řetězec a typ `System.String`hodnoty bude .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a>Prohlášení o zásadách

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a>Příklad

Následující příklad ukazuje nastavenou proměnnou zásadu v příchozí části. Tato zásada proměnné `isMobile` nastavení vytvoří logickou [kontextovou](api-management-policy-expressions.md#ContextVariables) `User-Agent` proměnnou, která `iPad` `iPhone`je nastavena na hodnotu true, pokud hlavička požadavku obsahuje text nebo .

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

| Element      | Popis   | Požaduje se |
| ------------ | ------------- | -------- |
| set-proměnná | Kořenový prvek. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                              | Požaduje se |
| --------- | ------------------------------------------------------------------------ | -------- |
| jméno      | Název proměnné.                                                | Ano      |
| value     | Hodnota proměnné. Může se jedná o výraz nebo hodnotu literálu. | Ano      |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě
-   **Obory zásad:** všechny obory

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a>Povolené typy

Výrazy použité `set-variable` v zásadě musí vrátit jeden z následujících základních typů.

-   Systém.Logická hodnota
-   System.SByte
-   Systém.Bajt
-   Systém.UInt16
-   Systém.UInt32
-   Systém.UInt64
-   Systém.Int16
-   Systém.Int32
-   Systém.Int64
-   Systém.Desetinné místo
-   System.Single
-   Systém.Double
-   System.Guid
-   System.string
-   System.Char
-   System.datetime
-   System.TimeSpan
-   System.Byte?
-   System.UInt16?
-   System.UInt32?
-   System.UInt64?
-   System.Int16?
-   System.Int32?
-   System.Int64?
-   System.Decimal?
-   System.Single?
-   System.Double?
-   System.Guid?
-   System.string?
-   System.Char?
-   System.datetime?

## <a name="trace"></a><a name="Trace"></a>Trasování

Zásada `trace` přidá vlastní trasování do výstupu nástroje API Inspector, telemetries Application Insights a/nebo diagnostické protokoly.

-   Zásada přidá vlastní trasování do výstupu [inspektora rozhraní API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) `Ocp-Apim-Trace` při aktivaci trasování, `Ocp-Apim-Subscription-Key` tj.
-   Zásada vytvoří telemetrická [trasování](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) v Application Insights, `severity` když je [povolena integrace Application Insights](https://docs.microsoft.com/azure/api-management/api-management-howto-app-insights) a úroveň zadaná v zásadě je na nebo vyšší než `verbosity` úroveň zadaná v nastavení diagnostiky.
-   Zásada přidá vlastnost v položce protokolu, když je [povolena diagnostická protokoly](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) a úroveň závažnosti zadaná v zásadě je na úrovni podrobnosti zadané v nastavení diagnostiky nebo vyšší.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a>Příklad

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elementy

| Element  | Popis                                                                                                                                          | Požaduje se |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| trasování    | Kořenový prvek.                                                                                                                                        | Ano      |
| zpráva  | Řetězec nebo výraz, který má být protokolován.                                                                                                                 | Ano      |
| zprostředkovatele identity | Přidá vlastní vlastnost application insights [trace](https://docs.microsoft.com/azure/azure-monitor/app/data-model-trace-telemetry) telemetrie. | Ne       |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                                                                               | Požaduje se | Výchozí |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Řetězec literál smysluplné do prohlížeče trasování a určení zdroje zprávy.                                   | Ano      | Není dostupné.     |
| severity  | Určuje úroveň závažnosti trasování. Povolené hodnoty `verbose` `information`jsou `error` , , (od nejnižší po nejvyšší). | Ne       | Verbose |
| jméno      | Název vlastnosti.                                                                                                     | Ano      | Není dostupné.     |
| value     | Hodnota vlastnosti                                                                                                    | Ano      | Není dostupné.     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

## <a name="wait"></a><a name="Wait"></a>Počkej

Zásady `wait` provádí své okamžité podřízené zásady paralelně a čeká na všechny nebo jeden z jeho okamžité podřízené zásady k dokončení před dokončením. Zásady čekání může mít jako své okamžité podřízené zásady [Odeslat požadavek](api-management-advanced-policies.md#SendRequest), [Získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey)a [Zásady toku řízení.](api-management-advanced-policies.md#choose)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Příklad

V následujícím příkladu `choose` jsou dvě zásady `wait` jako okamžité podřízené zásady zásad. Každá z `choose` těchto zásad se provádí paralelně. Každá `choose` zásada se pokusí načíst hodnotu uloženou v mezipaměti. Pokud je mezipaměť chybí, back-endová služba je volána k poskytnutí hodnoty. V tomto `wait` příkladu zásady nedokončí, dokud všechny jeho `for` okamžité podřízené zásady dokončit, protože atribut je nastavenna `all`. V tomto příkladu jsou`execute-branch-one` `value-one`kontextové proměnné ( , , `execute-branch-two`, a `value-two`) deklarovány mimo rozsah tohoto příkladu zásad.

```xml
<wait for="all">
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-one="])">
      <cache-lookup-value key="key-one" variable-name="value-one" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-one="))">
          <send-request mode="new" response-variable-name="value-one">
            <set-url>https://backend-one</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
  <choose>
    <when condition="@((bool)context.Variables["execute-branch-two="])">
      <cache-lookup-value key="key-two" variable-name="value-two" />
      <choose>
        <when condition="@(!context.Variables.ContainsKey("value-two="))">
          <send-request mode="new" response-variable-name="value-two">
            <set-url>https://backend-two</set-url>
            <set-method>GET</set-method>
          </send-request>
        </when>
      </choose>
    </when>
  </choose>
</wait>

```

### <a name="elements"></a>Elementy

| Element | Popis                                                                                                   | Požaduje se |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| Počkej    | Kořenový prvek. Může obsahovat pouze `send-request` `cache-lookup-value`podřízené `choose` prvky , a zásady. | Ano      |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                                                                                                                                                                                                                                                                                                                                                                            | Požaduje se | Výchozí |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| pro       | Určuje, `wait` zda zásady čeká na dokončení všech zásad okamžité podřízené nebo pouze jeden. Povolené hodnoty jsou následující:<br /><br /> - `all`- čekat na dokončení všech okamžitých podřízených politik<br />- any - počkejte na okamžité podřízené politiky k dokončení. Po dokončení první okamžité podřízené `wait` zásady, zásady dokončena a provádění jakékoli jiné okamžité podřízené zásady je ukončena. | Ne       | Vše     |

### <a name="usage"></a>Využití

Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end
-   **Obory zásad:** všechny obory

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami naleznete v tématu:

-   [Zásady ve správě rozhraní API](api-management-howto-policies.md)
-   [Výrazy zásad](api-management-policy-expressions.md)
-   [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
-   [Ukázky zásad](policy-samples.md)
