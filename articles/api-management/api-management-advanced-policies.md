---
title: Rozšířené zásady pro Azure API Management | Microsoft Docs
description: Přečtěte si o rozšířených zásadách dostupných pro použití v Azure API Management. Podívejte se na příklady a zobrazte další dostupné prostředky.
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
ms.openlocfilehash: 6ac3457a22128f313084ab070a5a61c2d26d4b85
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87851677"
---
# <a name="api-management-advanced-policies"></a>Pokročilé zásady služby API Management

V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="advanced-policies"></a><a name="AdvancedPolicies"></a> Rozšířené zásady

-   [Tok řízení](api-management-advanced-policies.md#choose) – podmíněně aplikuje příkazy zásad na základě výsledků vyhodnocení logických [výrazů](api-management-policy-expressions.md).
-   [Dopředné žádosti](#ForwardRequest) – přepošle požadavek do služby back-end.
-   [Limit Concurrency](#LimitConcurrency) – zabrání v současné době spouštění uzavřených zásad ve více než zadaném počtu požadavků najednou.
-   [Protokol do centra událostí](#log-to-eventhub) – posílá zprávy v zadaném formátu do centra událostí, které definuje entita protokolovacího nástroje.
-   [Přípravou odezva](#mock-response) – přeruší spuštění kanálu a vrátí napodobnou odpověď přímo volajícímu.
-   [Opakovat](#Retry) – pokusy o provedení uzavřených příkazů zásad, pokud a dokud podmínka není splněna. Spuštění se zopakuje v zadaných časových intervalech a až do zadaného počtu opakování.
-   [Návratová odpověď](#ReturnResponse) – přeruší spuštění kanálu a vrátí zadanou odpověď přímo volajícímu.
-   [Odeslání](#SendOneWayRequest) jednosměrné žádosti – pošle požadavek na zadanou adresu URL bez čekání na odpověď.
-   [Odeslat požadavek](#SendRequest) – pošle požadavek na zadanou adresu URL.
-   [Nastavení proxy serveru http](#SetHttpProxy) – umožňuje směrovat přesměrované žádosti přes proxy HTTP.
-   [Nastavit metodu žádosti](#SetRequestMethod) – umožňuje změnit metodu HTTP pro požadavek.
-   [Nastavit stavový kód](#SetStatus) – změní stavový kód HTTP na zadanou hodnotu.
-   [Nastavení proměnné](api-management-advanced-policies.md#set-variable) – uchovává hodnotu v pojmenované [kontextové](api-management-policy-expressions.md#ContextVariables) proměnné pro pozdější přístup.
-   [Trasování](#Trace) – přidá vlastní trasování do výstupu [kontroly rozhraní API](./api-management-howto-api-inspector.md) Application Insights telemetrií a protokoly prostředků.
-   [Wait](#Wait) -čeká na uzavřenou [žádost o odeslání](api-management-advanced-policies.md#SendRequest), před pokračováním [Získá hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey)nebo zásady [toku řízení](api-management-advanced-policies.md#choose) .

## <a name="control-flow"></a><a name="choose"></a> Tok řízení

Tato `choose` zásada aplikuje uzavřené příkazy zásad na základě výsledku vyhodnocení logických výrazů, podobně jako konstruktor if-then-else nebo konstrukce přepínače v programovacím jazyce.

### <a name="policy-statement"></a><a name="ChoosePolicyStatement"></a> Prohlášení o zásadách

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

Zásady toku řízení musí obsahovat alespoň jeden `<when/>` element. `<otherwise/>`Element je nepovinný. Podmínky v `<when/>` prvcích jsou vyhodnocovány v pořadí podle jejich vzhledu v rámci zásad. Použijí se příkazy zásad uzavřené v prvním `<when/>` elementu s atributem podmínky Equals `true` . Zásady, které jsou uzavřeny v rámci `<otherwise/>` elementu, pokud jsou k dispozici, budou aplikovány, pokud `<when/>` jsou všechny atributy podmínky elementu `false` .

### <a name="examples"></a>Příklady

#### <a name="example"></a><a name="ChooseExample"></a> Případě

Následující příklad ukazuje zásadu [set-Variable](api-management-advanced-policies.md#set-variable) a dva zásady toku řízení.

Zásada nastavit proměnnou je v oddílu příchozí a vytvoří `isMobile` logickou [kontextovou](api-management-policy-expressions.md#ContextVariables) proměnnou, která je nastavena na hodnotu true, pokud `User-Agent` Hlavička požadavku obsahuje text `iPad` nebo `iPhone` .

První zásada toku řízení je také v oddílu příchozí a podmíněně aplikuje jednu ze dvou [sad zásad parametrů řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) v závislosti na hodnotě `isMobile` kontextové proměnné.

Druhá zásada toku řízení je v odchozím oddílu a podmíněně aplikuje zásadu [převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) , pokud `isMobile` je nastavená na `true` .

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

Tento příklad ukazuje, jak provést filtrování obsahu odebráním datových elementů z odpovědi přijaté ze služby back-endu při použití `Starter` produktu. Ukázku konfigurace a používání těchto zásad najdete v tématu [cloudový krytový díl 177: další API Management funkce s využitím Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převinutí na 34:30. Začněte v 31:50. zobrazí se přehled [rozhraní API prognózy pro tmavé nebee](https://developer.forecast.io/) , které se používá pro tuto ukázku.

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

| Element   | Popis                                                                                                                                                                                                                                                               | Povinné |
| --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| výběrem    | Kořenový element.                                                                                                                                                                                                                                                             | Yes      |
| Kdy      | Podmínka, která se má použít pro `if` části nebo pro tyto `ifelse` `choose` zásady. Pokud `choose` má zásada více `when` oddílů, vyhodnotí se postupně. Po `condition` vyhodnocování prvku, který je vyhodnocen `true` , se `when` nevyhodnotí žádné další podmínky. | Yes      |
| případech | Obsahuje fragment zásady, který se použije v případě, že žádná z `when` podmínek není vyhodnocena jako `true` .                                                                                                                                                                               | No       |

### <a name="attributes"></a>Atributy

| Atribut                                              | Popis                                                                                               | Povinné |
| ------------------------------------------------------ | --------------------------------------------------------------------------------------------------------- | -------- |
| Condition = "logický výraz &#124; Boolean Constant" | Logický výraz nebo konstanta k vyhodnocení při vyhodnocení obsahujícího `when` příkazu zásad. | Yes      |

### <a name="usage"></a><a name="ChooseUsage"></a> Využívání

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="forward-request"></a><a name="ForwardRequest"></a> Dopředný požadavek

`forward-request`Zásada předává příchozí požadavek do back-end služby zadané v [kontextu](api-management-policy-expressions.md#ContextVariables)požadavku. Adresa URL back-end služby je zadaná v [Nastavení](./import-and-publish.md) rozhraní API a dá se změnit pomocí [nastavení zásady back-end služby](api-management-transformation-policies.md) .

> [!NOTE]
> Výsledkem odebrání této zásady je, že žádost není předávána do back-endové služby a zásady v odchozím oddílu se vyhodnocují okamžitě po úspěšném dokončení zásad v části příchozí.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<forward-request timeout="time in seconds" follow-redirects="false | true" buffer-request-body="false | true" fail-on-error-status-code="false | true"/>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

Následující zásada úrovně rozhraní API přepošle všechny požadavky rozhraní API do služby back-endu s intervalem časového limitu 60 sekund.

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

Tato zásada na úrovni operace používá `base` element k dědění zásad back-endu z nadřazeného rozsahu úrovně rozhraní API.

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

Tato zásada na úrovni operace explicitně přepošle všechny požadavky na back-end službu s časovým limitem 120 a nedědí zásady nadřazené úrovně rozhraní API. Pokud back-end služba odpoví chybovým kódem 400 až 599 včetně, aktivuje se [Chyba v oddílu On-Error](api-management-error-handling-policies.md) .

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

Tato zásada na úrovni operace nepředávají požadavky do back-endové služby.

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

| Element         | Popis   | Povinné |
| --------------- | ------------- | -------- |
| dopředné žádosti | Kořenový element. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut                                     | Popis                                                                                                                                                                                                                                                                                                    | Povinné | Výchozí |
| --------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| timeout = "Integer"                             | Doba v sekundách, po kterou se má čekat na vrácení hlaviček odpovědí HTTP službou back-end, než dojde k vygenerování chyby časového limitu. Minimální hodnota je 0 sekund. Hodnoty větší než 240 sekund nemusí být dodrženy, protože podkladová síťová infrastruktura může po uplynutí této doby zrušit nečinné připojení. | No       | Žádné    |
| následné přesměrování = "false &#124; true"          | Určuje, jestli je následováno přesměrování ze služby back-end, nebo se vrátí volajícímu.                                                                                                                                                                                                    | No       | false (nepravda)   |
| buffer-Request-body = "false &#124; true"       | Pokud je hodnota nastavená na "true", uloží se do vyrovnávací paměti a při [opakovaném pokusu](api-management-advanced-policies.md#Retry)se znovu použije.                                                                                                                                                                                               | No       | false (nepravda)   |
| selhání-On-Error-Status-Code = "false &#124; true" | Pokud je nastavená hodnota true Triggers [On-Error](api-management-error-handling-policies.md) , pro kódy odpovědí v rozsahu od 400 do 599 včetně.                                                                                                                                                                      | No       | false (nepravda)   |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** back-end
-   **Obory zásad:** všechny rozsahy

## <a name="limit-concurrency"></a><a name="LimitConcurrency"></a> Omezení souběžnosti

`limit-concurrency`Zásada zabraňuje tomu, aby se uzavřené zásady prováděly ve více než zadaném počtu žádostí. Po překročení tohoto počtu budou nové požadavky okamžitě úspěšné a 429 příliš mnoho stavových kódů požadavků.

### <a name="policy-statement"></a><a name="LimitConcurrencyStatement"></a> Prohlášení o zásadách

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
    </limit-concurrency>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementy

| Element           | Popis   | Povinné |
| ----------------- | ------------- | -------- |
| limit – souběžnost | Kořenový element. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                                                        | Povinné | Výchozí |
| --------- | -------------------------------------------------------------------------------------------------- | -------- | ------- |
| key       | Řetězec. Výraz je povolený. Určuje rozsah souběžnosti. Může být sdíleno více zásadami. | Yes      | Není k dispozici     |
| max – počet | Celé číslo Určuje maximální počet požadavků, které mají povolené zadání těchto zásad.           | Yes      | Není k dispozici     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="log-to-event-hub"></a><a name="log-to-eventhub"></a> Přihlášení do centra událostí

`log-to-eventhub`Zásada odesílá zprávy v zadaném formátu do centra událostí, které definuje entita protokolovacího nástroje. Jak název naznačuje, zásada se používá k uložení vybraných informací o kontextu požadavku nebo odpovědi pro online nebo offline analýzu.

> [!NOTE]
> Podrobný průvodce konfigurací centra událostí a událostí protokolování najdete v tématu [postup protokolu API Managementch událostí pomocí Azure Event Hubs](./api-management-howto-log-event-hubs.md).

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Příklad

Libovolný řetězec se dá použít jako hodnota, která se má Event Hubs přihlásit. V tomto příkladu se protokoluje datum a čas, název služby nasazení, ID žádosti, IP adresa a název operace pro všechna příchozí volání, která se zapisují do protokolovacího nástroje centra událostí zaregistrovaného s `contoso-logger` ID.

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

| Element         | Popis                                                                     | Povinné |
| --------------- | ------------------------------------------------------------------------------- | -------- |
| přihlášení k centru událostí | Kořenový element. Hodnota tohoto prvku je řetězec, který se má protokolovat do centra událostí. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut     | Popis                                                               | Povinné                                                             |
| ------------- | ------------------------------------------------------------------------- | -------------------------------------------------------------------- |
| protokolovací nástroj – ID     | ID protokolovacího nástroje zaregistrovaného ve vaší API Management službě.         | Yes                                                                  |
| identifikátor oddílu  | Určuje index oddílu, ve kterém jsou odesílány zprávy.             | Nepovinný parametr. Tento atribut se nedá použít, pokud `partition-key` se používá. |
| Partition – klíč | Určuje hodnotu použitou při přiřazování oddílů při posílání zpráv. | Nepovinný parametr. Tento atribut se nedá použít, pokud `partition-id` se používá.  |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="mock-response"></a><a name="mock-response"></a> Přípravou odezva

`mock-response`Jak název naznačuje, se používá k napodobování rozhraní API a operací. Přerušuje normální spuštění kanálu a vrátí napodobnou odpověď volajícímu. Zásada se vždycky pokusí vrátit odpovědi nejvyšší přesnosti. V případě, že jsou k dispozici, preferuje příklady obsahu odpovědi. Generuje ukázkové odpovědi ze schémat, pokud jsou k dispozici schémata a příklady nejsou. Nejsou-li nalezeny žádné příklady ani schémata, budou vráceny odpovědi bez obsahu.

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

| Element       | Popis   | Povinné |
| ------------- | ------------- | -------- |
| Maketa – odezva | Kořenový element. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut    | Popis                                                                                           | Povinné | Výchozí |
| ------------ | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Stavový kód  | Určuje kód stavu odpovědi a používá se k výběru odpovídajícího příkladu nebo schématu.                 | No       | 200     |
| typ obsahu | Určuje `Content-Type` hodnotu hlavičky odpovědi a používá se k výběru odpovídajícího příkladu nebo schématu. | No       | Žádné    |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="retry"></a><a name="Retry"></a> Opakujte

`retry`Zásady spustí své podřízené zásady jednou a potom se znovu pokusí o jejich spuštění, dokud se znovu nespustí `condition` `false` nebo `count` se znovu vyčerpá.

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

V následujícím příkladu se znovu pokusí o předávání požadavků až desetkrát pomocí algoritmu exponenciálního opakování. Vzhledem `first-fast-retry` k tomu, že je nastavena hodnota false, budou všechny pokusy o opakování platit algoritmem exponenciálního opakování.

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

| Element | Popis                                                         | Povinné |
| ------- | ------------------------------------------------------------------- | -------- |
| retry   | Kořenový element. Může obsahovat jakékoli jiné zásady jako své podřízené prvky. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut        | Popis                                                                                                                                           | Povinné | Výchozí |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| pomocné        | Logický literál nebo [výraz](api-management-policy-expressions.md) určující, zda mají být pokusy o opakování zastaveny ( `false` ) nebo pokračování ( `true` ).      | Yes      | Není k dispozici     |
| count            | Kladné číslo určující maximální počet opakovaných pokusů o opakování.                                                                                | Yes      | Není k dispozici     |
| interval         | Kladné číslo v sekundách, které určuje interval čekání mezi pokusy o opakování.                                                                 | Yes      | Není k dispozici     |
| Max – interval     | Kladné číslo v sekundách, které určuje maximální interval čekání mezi pokusy o opakování. Slouží k implementaci algoritmu exponenciálního opakování. | No       | Není k dispozici     |
| Delta            | Kladné číslo v sekundách, které určuje přírůstek intervalu čekání. Slouží k implementaci algoritmů lineárního a exponenciálního opakování.             | No       | Není k dispozici     |
| First – Fast – opakování | Pokud je nastavená na `true` , první pokus o opakování proběhne okamžitě.                                                                                  | No       | `false` |

> [!NOTE]
> Je-li `interval` zadán pouze parametr, jsou provedeny pokusy s **pevným** intervalem.
> Pokud `interval` `delta` jsou zadány pouze a, je použit algoritmus **lineárního** opakování intervalu, kde je vypočítána doba čekání mezi opakovanými pokusy podle následujícího vzorce `interval + (count - 1)*delta` .
> Pokud `interval` `max-interval` `delta` jsou zadány, a, je použit algoritmus **exponenciálního** opakování intervalu, kde doba čekání mezi opakovanými pokusy roste exponenciálně od hodnoty `interval` k hodnotě `max-interval` podle následujícího vzorce: `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)` .

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes) zásad. Všimněte si, že tato zásada zdědí omezení použití podřízených zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="return-response"></a><a name="ReturnResponse"></a> Návratová odpověď

`return-response`Zásady přeruší spuštění kanálu a vrátí buď výchozí nebo vlastní odpověď volajícímu. Výchozí odezva není `200 OK` bez těla. Vlastní odpověď lze zadat prostřednictvím kontextové proměnné nebo příkazů zásad. V případě, že jsou k dispozici obě, je odpověď obsažená v kontextové proměnné upravena pomocí příkazů zásad před jejich vrácením volajícímu.

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

| Element         | Popis                                                                               | Povinné |
| --------------- | ----------------------------------------------------------------------------------------- | -------- |
| návrat – odpověď | Kořenový element.                                                                             | Yes      |
| Set – hlavička      | Příkaz zásad [pro záhlaví sady](api-management-transformation-policies.md#SetHTTPheader) | No       |
| Set – tělo        | Příkaz zásad [pro tělo množiny](api-management-transformation-policies.md#SetBody) .         | No       |
| nastavit stav      | Příkaz zásady [stavu](api-management-advanced-policies.md#SetStatus) .           | No       |

### <a name="attributes"></a>Atributy

| Atribut              | Popis                                                                                                                                                                          | Povinné  |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| Response – proměnná – Název | Název kontextové proměnné, na kterou se odkazuje, například zásada pro [odesílání požadavků pro odesílání](api-management-advanced-policies.md#SendRequest) dat a obsahující `Response` objekt | Nepovinný parametr. |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="send-one-way-request"></a><a name="SendOneWayRequest"></a> Poslat jednosměrnou žádost

`send-one-way-request`Zásada odešle zadaný požadavek na zadanou adresu URL bez čekání na odpověď.

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

Tato ukázková zásada ukazuje příklad použití `send-one-way-request` zásady k odeslání zprávy do chatovací místnosti časové rezervy, pokud je kód odpovědi HTTP větší nebo roven 500. Další informace o této ukázce najdete v tématu [použití externích služeb ze služby Azure API Management](./api-management-sample-send-request.md).

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

| Element                    | Popis                                                                                                 | Povinné                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| Send – One-Way – požadavek       | Kořenový element.                                                                                               | Yes                             |
| url                        | Adresa URL požadavku                                                                                     | Žádný režim if = kopírovat; v opačném případě ano. |
| method                     | Metoda HTTP pro požadavek.                                                                            | Žádný režim if = kopírovat; v opačném případě ano. |
| header                     | Hlavička žádosti Pro více hlaviček požadavku použijte více elementů záhlaví.                                  | No                              |
| text                       | Text žádosti                                                                                           | No                              |
| ověřování – certifikát | [Certifikát, který se má použít pro ověření klienta](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>Atributy

| Atribut     | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Povinné | Výchozí  |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "řetězec" | Určuje, zda se jedná o nový požadavek nebo o kopii aktuálního požadavku. V režimu odchozího připojení neinicializuje režim = Copy Text žádosti.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | Nová      |
| name          | Určuje název hlavičky, který se má nastavit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | Není k dispozici      |
| Existuje – akce | Určuje akci, která se má provést, když je hlavička již zadána. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override – nahradí hodnotu existujícího záhlaví.<br />-Skip – nenahradí stávající hodnotu záhlaví.<br />-Append – připojí hodnotu k existující hodnotě záhlaví.<br />-Delete – Odebere hlavičku z požadavku.<br /><br /> Pokud je nastavená na `override` zařazení více položek se stejným názvem, v záhlaví se nastaví podle všech záznamů (které se budou zobrazovat víckrát). ve výsledku se nastaví jenom uvedené hodnoty. | No       | override |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="send-request"></a><a name="SendRequest"></a> Odeslat žádost

`send-request`Zásada odešle poskytnutý požadavek na zadanou adresu URL, která čeká na uplynutí doby, než je nastavená hodnota časového limitu.

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

Tento příklad ukazuje jeden ze způsobů, jak ověřit token odkazu pomocí autorizačního serveru. Další informace o této ukázce najdete v tématu [použití externích služeb ze služby Azure API Management](./api-management-sample-send-request.md).

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

| Element                    | Popis                                                                                                 | Povinné                        |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------- |
| Odeslat požadavek               | Kořenový element.                                                                                               | Yes                             |
| url                        | Adresa URL požadavku                                                                                     | Žádný režim if = kopírovat; v opačném případě ano. |
| method                     | Metoda HTTP pro požadavek.                                                                            | Žádný režim if = kopírovat; v opačném případě ano. |
| header                     | Hlavička žádosti Pro více hlaviček požadavku použijte více elementů záhlaví.                                  | No                              |
| text                       | Text žádosti                                                                                           | No                              |
| ověřování – certifikát | [Certifikát, který se má použít pro ověření klienta](api-management-authentication-policies.md#ClientCertificate) | No                              |

### <a name="attributes"></a>Atributy

| Atribut                       | Popis                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Povinné | Výchozí  |
| ------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | -------- |
| Mode = "řetězec"                   | Určuje, zda se jedná o nový požadavek nebo o kopii aktuálního požadavku. V režimu odchozího připojení neinicializuje režim = Copy Text žádosti.                                                                                                                                                                                                                                                                                                                                                                                                                                                                | No       | Nová      |
| Response-Variable-Name = "String" | Název kontextové proměnné, která bude přijímat objekt Response. Pokud proměnná neexistuje, vytvoří se po úspěšném spuštění zásady a zpřístupní se prostřednictvím [`context.Variable`](api-management-policy-expressions.md#ContextVariables) kolekce.                                                                                                                                                                                                                                                                                                                          | Yes      | Není k dispozici      |
| timeout = "Integer"               | Časový limit v sekundách, po kterém se volání adresy URL nezdařila.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | No       | 60       |
| ignorovat – chyba                    | V případě hodnoty true a výsledkem požadavku dojde k chybě:<br /><br /> -Pokud byla zadána hodnota Response-Variable-Name, bude obsahovat hodnotu null.<br />-Pokud nebyl zadán parametr Response-Variable-Name, je to kontext. Požadavek nebude aktualizován.                                                                                                                                                                                                                                                                                                                                                                                   | No       | false (nepravda)    |
| name                            | Určuje název hlavičky, který se má nastavit.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Yes      | Není k dispozici      |
| Existuje – akce                   | Určuje akci, která se má provést, když je hlavička již zadána. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override – nahradí hodnotu existujícího záhlaví.<br />-Skip – nenahradí stávající hodnotu záhlaví.<br />-Append – připojí hodnotu k existující hodnotě záhlaví.<br />-Delete – Odebere hlavičku z požadavku.<br /><br /> Pokud je nastavená na `override` zařazení více položek se stejným názvem, v záhlaví se nastaví podle všech záznamů (které se budou zobrazovat víckrát). ve výsledku se nastaví jenom uvedené hodnoty. | No       | override |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="set-http-proxy"></a><a name="SetHttpProxy"></a> Nastavit proxy server HTTP

`proxy`Zásady vám umožní směrovat požadavky předané do back-endu prostřednictvím proxy serveru http. Mezi bránou a proxy serverem se podporuje jenom HTTP (ne HTTPS). Pouze ověřování typu Basic a NTLM.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Příklad

Poznamenejte si použití [vlastností](api-management-howto-properties.md) jako hodnot uživatelského jména a hesla, abyste se vyhnuli ukládání citlivých informací v dokumentu zásad.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

| Element | Popis  | Povinné |
| ------- | ------------ | -------- |
| proxy   | Kořenový element | Yes      |

### <a name="attributes"></a>Atributy

| Atribut         | Popis                                            | Povinné | Výchozí |
| ----------------- | ------------------------------------------------------ | -------- | ------- |
| URL = "řetězec"      | Adresa URL proxy serveru ve formátu http://host:port .             | Yes      | Není k dispozici     |
| username = "řetězec" | Uživatelské jméno, které se má použít k ověřování pomocí proxy serveru. | No       | Není k dispozici     |
| Password = "řetězec" | Heslo, které se má použít pro ověřování u proxy serveru. | No       | Není k dispozici     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny rozsahy

## <a name="set-request-method"></a><a name="SetRequestMethod"></a> Nastavit metodu požadavku

Tato `set-method` zásada umožňuje změnit metodu požadavku HTTP pro požadavek.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Příklad

Tato ukázková zásada, která používá `set-method` zásadu, zobrazuje příklad odeslání zprávy do chatovací místnosti časové rezervy, pokud je kód odpovědi HTTP větší nebo roven 500. Další informace o této ukázce najdete v tématu [použití externích služeb ze služby Azure API Management](./api-management-sample-send-request.md).

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

| Element    | Popis                                                       | Povinné |
| ---------- | ----------------------------------------------------------------- | -------- |
| Set – metoda | Kořenový element. Hodnota elementu určuje metodu HTTP. | Yes      |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="set-status-code"></a><a name="SetStatus"></a> Nastavit stavový kód

`set-status`Zásada nastaví stavový kód HTTP na zadanou hodnotu.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Příklad

Tento příklad ukazuje, jak vrátit odpověď 401, pokud autorizační token není platný. Další informace najdete v tématu [použití externích služeb ze služby Azure API Management](./api-management-sample-send-request.md) .

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

| Element    | Popis   | Povinné |
| ---------- | ------------- | -------- |
| nastavit stav | Kořenový element. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut       | Popis                                                | Povinné | Výchozí |
| --------------- | ---------------------------------------------------------- | -------- | ------- |
| Code = "Integer"  | Stavový kód protokolu HTTP, který se má vrátit.                            | Yes      | Není k dispozici     |
| důvod = řetězec | Popis důvodu vrácení stavového kódu. | Yes      | Není k dispozici     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** odchozí, back-end, on-error
-   **Obory zásad:** všechny rozsahy

## <a name="set-variable"></a><a name="set-variable"></a> Nastavit proměnnou

`set-variable`Zásada deklaruje [kontextovou](api-management-policy-expressions.md#ContextVariables) proměnnou a přiřadí jí hodnotu zadanou prostřednictvím [výrazu](api-management-policy-expressions.md) nebo řetězcového literálu. Pokud výraz obsahuje literál, bude převeden na řetězec a typ hodnoty bude `System.String` .

### <a name="policy-statement"></a><a name="set-variablePolicyStatement"></a> Prohlášení o zásadách

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

### <a name="example"></a><a name="set-variableExample"></a> Případě

Následující příklad ukazuje zásadu sady proměnných v oddílu příchozí. Tato sada zásad proměnné vytvoří `isMobile` logickou [kontextovou](api-management-policy-expressions.md#ContextVariables) proměnnou, která je nastavena na hodnotu true, pokud `User-Agent` Hlavička požadavku obsahuje text `iPad` nebo `iPhone` .

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

| Element      | Popis   | Povinné |
| ------------ | ------------- | -------- |
| SET – proměnná | Kořenový element. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                              | Povinné |
| --------- | ------------------------------------------------------------------------ | -------- |
| name      | Název proměnné.                                                | Yes      |
| value     | Hodnota proměnné. Může to být výraz nebo hodnota literálu. | Yes      |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba
-   **Obory zásad:** všechny rozsahy

### <a name="allowed-types"></a><a name="set-variableAllowedTypes"></a> Povolené typy

Výrazy používané v `set-variable` zásadách musí vracet jeden z následujících základních typů.

-   System. Boolean
-   System. SByte
-   System. Byte
-   System. UInt16
-   System. UInt32
-   System. UInt64
-   System. Int16
-   System. Int32
-   System. Int64
-   System. Decimal
-   System. Single
-   System. Double
-   System. GUID
-   System. String
-   System. Char
-   System. DateTime
-   System. TimeSpan
-   System. Byte?
-   System. UInt16?
-   System. UInt32?
-   System. UInt64?
-   System. Int16?
-   System. Int32?
-   System. Int64?
-   System. Decimal?
-   System. Single?
-   System. Double?
-   Identifikátor System. GUID?
-   Řetězec System. String?
-   System. Char?
-   System. DateTime?

## <a name="trace"></a><a name="Trace"></a> Přehled

`trace`Zásada přidá vlastní trasování do výstupu kontroly rozhraní API, Application Insights telemetrií a/nebo protokoly prostředků.

-   Zásada přidá vlastní trasování do výstupu [inspektoru rozhraní API](./api-management-howto-api-inspector.md) , když se aktivuje trasování, tj. `Ocp-Apim-Trace` je přítomná hlavička Request, která má nastavenou hodnotu true a `Ocp-Apim-Subscription-Key` je k dispozici Hlavička požadavku a obsahuje platný klíč, který umožňuje trasování.
-   Zásada vytvoří telemetrii [trasování](../azure-monitor/app/data-model-trace-telemetry.md) v Application Insights, pokud je povolená [integrace Application Insights](./api-management-howto-app-insights.md) a `severity` úroveň zadaná v zásadách je vyšší nebo rovna `verbosity` úrovni určené v nastavení diagnostiky.
-   Zásada přidá do položky protokolu vlastnost, pokud je povolená možnost [protokoly prostředků](./api-management-howto-use-azure-monitor.md#activity-logs) a úroveň závažnosti zadaná v zásadách je na nebo vyšší než úroveň podrobností uvedená v nastavení diagnostiky.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<trace source="arbitrary string literal" severity="verbose|information|error">
    <message>String literal or expressions</message>
    <metadata name="string literal or expressions" value="string literal or expressions"/>
</trace>

```

### <a name="example"></a><a name="traceExample"></a> Případě

```xml
<trace source="PetStore API" severity="verbose">
    <message>@((string)context.Variables["clientConnectionID"])</message>
    <metadata name="Operation Name" value="New-Order"/>
</trace>
```

### <a name="elements"></a>Elementy

| Element  | Popis                                                                                                                                          | Povinné |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| trasování    | Kořenový element.                                                                                                                                        | Yes      |
| zpráva  | Řetězec nebo výraz, který se má zaznamenat.                                                                                                                 | Yes      |
| zprostředkovatele identity | Přidá do telemetrie [trasování](../azure-monitor/app/data-model-trace-telemetry.md) Application Insights vlastní vlastnost. | No       |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                                                                               | Povinné | Výchozí |
| --------- | ------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| source    | Řetězcové literály smysluplné pro prohlížeč trasování a určení zdroje zprávy.                                   | Yes      | Není k dispozici     |
| severity  | Určuje úroveň závažnosti trasování. Povolené hodnoty jsou `verbose` , `information` , `error` (od nejnižší po nejvyšší). | No       | Verbose |
| name      | Název vlastnosti.                                                                                                     | Yes      | Není k dispozici     |
| value     | Hodnota vlastnosti                                                                                                    | Yes      | Není k dispozici     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes) zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

## <a name="wait"></a><a name="Wait"></a> Počkej

`wait`Zásady spustí paralelně své bezprostřední podřízené zásady a počká, až se všechny nebo jedna z jejích bezprostředně podřízených zásad dokončí, než se dokončí. Zásady čekání můžou mít jako své bezprostřední podřízené zásady [odesílat požadavky](api-management-advanced-policies.md#SendRequest), [získávat hodnoty z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey)a zásady [toku řízení](api-management-advanced-policies.md#choose) .

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Příklad

V následujícím příkladu jsou k dispozici dvě `choose` zásady jako přímá podřízená `wait` zásada zásady. Každá z těchto `choose` zásad se spouští paralelně. Každá `choose` zásada se pokusí načíst hodnotu uloženou v mezipaměti. V případě neúspěšného vytvoření mezipaměti se zavolá služba back-end, která tuto hodnotu poskytne. V tomto příkladu se `wait` zásada nedokončila, dokud se nedokončí všechny zásady bezprostřední podřízenosti, protože `for` atribut je nastavený na `all` . V tomto příkladu jsou kontextové proměnné ( `execute-branch-one` , `value-one` , `execute-branch-two` a `value-two` ) deklarovány mimo obor tohoto ukázkového pravidla.

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

| Element | Popis                                                                                                   | Povinné |
| ------- | ------------------------------------------------------------------------------------------------------------- | -------- |
| Počkej    | Kořenový element. Může obsahovat pouze podřízené prvky `send-request` `cache-lookup-value` a `choose` zásady. | Yes      |

### <a name="attributes"></a>Atributy

| Atribut | Popis                                                                                                                                                                                                                                                                                                                                                                                                            | Povinné | Výchozí |
| --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| pro       | Určuje, jestli `wait` zásady čekají na dokončení všech bezprostředních podřízených zásad, nebo jenom jeden. Povolené hodnoty jsou následující:<br /><br /> - `all` – Počkejte na dokončení všech okamžitých podřízených zásad<br />-Any – počkejte na dokončení všech bezprostředně podřízených zásad. Jakmile se dokončí první bezprostředně podřízená zásada, `wait` zásada se dokončí a provede se ukončení všech ostatních okamžitých podřízených zásad. | No       | Vše     |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end
-   **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

-   [Zásady v API Management](api-management-howto-policies.md)
-   [Výrazy zásad](api-management-policy-expressions.md)
-   [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
-   [Ukázky zásad](policy-samples.md)
