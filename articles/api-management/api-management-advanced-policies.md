---
title: Pokročilé zásady Azure API Management | Dokumentace Microsoftu
description: Další informace o pokročilé zásady, které jsou k dispozici pro použití ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: cc327695a91a39c4d910a4f2421b22cc3b100627
ms.sourcegitcommit: 6678e16c4b273acd3eaf45af310de77090137fa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50747877"
---
# <a name="api-management-advanced-policies"></a>Pokročilé zásady API managementu
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AdvancedPolicies"></a> Pokročilé zásady

-   [Řízení toku](api-management-advanced-policies.md#choose) – podmíněně platí prohlášení zásad na základě výsledků vyhodnocení datový typ Boolean [výrazy](api-management-policy-expressions.md).
-   [Dopředné požadavek](#ForwardRequest) -předá požadavek back-end službu.
-   [Omezení souběžnosti](#LimitConcurrency) – brání uzavřen zásady spuštění více než určitý počet požadavků současně.
-   [Protokolování do Eventhubu](#log-to-eventhub) -odesílá zprávy do centra událostí, který je definován entity protokolovací nástroj v zadaném formátu.
-   [Napodobení odpovědi](#mock-response) -kanálu přeruší provádění a imitaci odpovědi přímo k volajícímu.
-   [Zkuste](#Retry) -opakuje spouštění příkazů uzavřených zásady, pokud a dokud je podmínka splněna. Spuštění se opakovaly s zadaným časovým intervalům a až po zadaný počet opakování.
-   [Vrací odpověď](#ReturnResponse) -kanálu přeruší provádění a vrátí zadanou odpověď přímo na volajícího.
-   [Odeslat jednosměrný požadavek](#SendOneWayRequest) – odešle požadavek na zadanou adresu URL bez čekání na odpověď.
-   [Poslat žádost o](#SendRequest) – odešle požadavek na zadanou adresu URL.
-   [Nastavit proxy server HTTP](#SetHttpProxy) – umožňuje trasy předávají požadavky přes proxy server HTTP.
-   [Nastaví metodu požadavku](#SetRequestMethod) -vám umožní změnit metodu HTTP pro žádost.
-   [Nastavené kódy stavu](#SetStatus) – změní stavový kód HTTP se zadanou hodnotou.
-   [Nastavit proměnnou](api-management-advanced-policies.md#set-variable) -nevyřeší hodnotu v pojmenovaná [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné pro pozdější přístup.
-   [Trasování](#Trace) – přidá řetězec do [pro inspekci API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) výstup.
-   [Počkejte](#Wait) – čeká uzavřen [požadavků na odeslání](api-management-advanced-policies.md#SendRequest), [získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey), nebo [řízení toku](api-management-advanced-policies.md#choose) zásady než budete pokračovat, dokončete.

##  <a name="choose"></a> Tok řízení
 `choose` Zásad použije uzavřené zásady vytvořit příkazy na základě výsledku vyhodnocení logické výrazy, podobně jako if-then-else nebo přepínač v programovacím jazyce.

###  <a name="ChoosePolicyStatement"></a> Prohlášení o zásadách

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

 Zásada řízení toku musí obsahovat alespoň jeden `<when/>` elementu. `<otherwise/>` Element je volitelné. Podmínky v `<when/>` prvky jsou vyhodnocovány v pořadí jejich výskytu v rámci zásady. Zásady příkazů uzavřených v rámci první `<when/>` elementem equals atributu podmínky `true` se použijí. Zásady uzavřený do složených závorek `<otherwise/>` element, pokud jsou k dispozici, bude použito v případě všech z `<when/>` atributy prvků podmínky jsou `false`.

### <a name="examples"></a>Příklady

####  <a name="ChooseExample"></a> Příklad
 Následující příklad ukazuje [nastavená proměnná](api-management-advanced-policies.md#set-variable) zásad a dvě zásady řízení toku.

 Nastavení zásad pro proměnné v části příchozí a vytvoří `isMobile` logickou [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné, která je nastavena na hodnotu true, pokud `User-Agent` záhlaví obsahuje text požadavku `iPad` nebo `iPhone`.

 První zásady toku řízení je také v části příchozí a podmíněně platí jednoho ze dvou [nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) zásady v závislosti na hodnotu `isMobile` kontextové proměnné.

 Druhá zásada tok řízení v části outbound a podmíněně použije [převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) zásad při `isMobile` je nastavena na `true`.

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

#### <a name="example"></a>Příklad:
 Tento příklad ukazuje, jak provádět filtrování obsahu odebráním datové prvky z odpovědi přijaté z back-end službu, při použití `Starter` produktu. Ukázka konfigurace a používání této zásady, najdete v části [Cloud Cover epizodě 177: Další funkce API Management s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a přechod na libovolný krok k 34:30. Začátek 31:50 zobrazíte přehled [tmavě Sky prognózy API](https://developer.forecast.io/) použít pro tuto ukázku.

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|Zvolte|Kořenový element.|Ano|
|kdy|Podmínky použití pro `if` nebo `ifelse` části `choose` zásad. Pokud `choose` zásad má více `when` oddíly, jsou vyhodnocovány sekvenčně. Jednou `condition` z Pokud element vyhodnocen `true`, žádné další `when` vyhodnocení podmínek.|Ano|
|jinak|Obsahuje fragment zásad se použije, pokud žádná z `when` podmínky vyhodnotí na `true`.|Ne|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|podmínka = "logický výraz &#124; Logická – konstanta"|Logický výraz nebo konstantu, která vyhodnotí při obsahující `when` vyhodnocení prohlášení o zásadách.|Ano|

###  <a name="ChooseUsage"></a> Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="ForwardRequest"></a> Dopředné žádosti
 `forward-request` Zásad předá požadavek back-end službu v požadavku na příchozí [kontextu](api-management-policy-expressions.md#ContextVariables). Adresa URL back-end služby je zadaný v rozhraní API [nastavení](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) a lze změnit pomocí [nastavení back-end služby](api-management-transformation-policies.md) zásad.

> [!NOTE]
>  Odebrání výsledků této zásady v požadavku není předávají back-endu služby a v části outbound zásady jsou vyhodnocovány hned po úspěšném dokončení zásad v části příchozí.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false"/>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad:
 Následující úrovně zásad rozhraní API předává všechny požadavky na back-end službu s intervalem časového limitu 60 sekund.

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

#### <a name="example"></a>Příklad:
 Tato zásada na úrovni operace používá `base` – element pro dědění z nadřazeného oboru úrovně rozhraní API back-end zásady.

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

#### <a name="example"></a>Příklad:
 Tato zásada na úrovni operace explicitně předává všechny požadavky na back-end službu s časovým limitem 120 a nedědí nadřazené zásad úrovně back-endového rozhraní API.

```xml
<!-- operation level -->
<policies>
    <inbound>
        <base/>
    </inbound>
    <backend>
        <forward-request timeout="120"/>
        <!-- effective policy. note the absence of <base/> -->
    </backend>
    <outbound>
        <base/>
    </outbound>
</policies>

```

#### <a name="example"></a>Příklad:
 Tato zásada na úrovni operace nepředává požadavky na back-end službu.

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|předání požadavku|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|časový limit = "celočíselné"|Interval časového limitu v sekundách, než volání služby back-end se nezdaří.|Ne|300 sekund|
|postupujte podle přesměrování = "true &#124; false"|Určuje, zda přesměrování z back-end služby jsou za nímž následuje brány nebo vrátit zpět volajícímu.|Ne|false (nepravda)|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** back-endu
-   **Zásady obory:** všechny obory

##  <a name="LimitConcurrency"></a> Omezení souběžnosti
 `limit-concurrency` Zásada zabraňuje uzavřené zásady spouštění více než určitý počet požadavků v daném okamžiku. Při překročení tohoto čísla, nové požadavky selže okamžitě 429 příliš mnoho požadavků stavovým kódem.

###  <a name="LimitConcurrencyStatement"></a> Prohlášení o zásadách

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad:
 Následující příklad ukazuje, jak omezit počet požadavků, předán podle hodnoty kontextových proměnných back-endu.

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|omezení souběžnosti|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|--------------|
|key|Řetězec. Výrazu povolený. Určuje obor souběžnosti. Může být sdílen více zásad.|Ano|neuvedeno|
|maximální počet|Celé číslo. Určuje maximální počet požadavků, které se můžou zadat zásady.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="log-to-eventhub"></a> Protokolování do Eventhubu
 `log-to-eventhub` Zásady odesílá zprávy do centra událostí, který je definován entity protokolovací nástroj v zadaném formátu. Jak již název napovídá, zásady se používá pro uložení vybraného informace o kontextu požadavku nebo odpovědi pro online nebo offline analýzu.

> [!NOTE]
>  Podrobné pokyny ke konfiguraci centra událostí a protokolování událostí, naleznete v tématu [jak protokolovat události správy rozhraní API pomocí Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Příklad:
 Jakýkoli řetězec může sloužit jako hodnota má být zaznamenána ve službě Event Hubs. V tomto příkladu datum a čas, název služby pro nasazení, id požadavku, ip adresu a název operace pro všechny příchozí volání jsou protokolovány do centra událostí zaregistrované protokolovací nástroj `contoso-logger` id.

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|protokol do centra událostí|Kořenový element. Hodnota tohoto prvku je řetězec, který se přihlaste do vašeho centra událostí.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|id protokolovacího nástroje.|Id protokolovacího nástroje registraci ve službě API Management.|Ano|
|id oddílu|Určuje index oddílu, kam se mají odesílat zprávy.|Volitelné. Tento atribut nelze použít, pokud `partition-key` se používá.|
|klíč oddílu|Určuje hodnotu sloužící pro přiřazení k oddílu při odesílání zpráv.|Volitelné. Tento atribut nelze použít, pokud `partition-id` se používá.|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="mock-response"></a> Napodobení odpovědi
`mock-response`, Jako název znamená, slouží k napodobení rozhraní API a operace. Přeruší provádění běžných kanálu a volajícímu imitaci odpovědi. Zásady se vždy pokusí vrátit odpovědi nejvěrnějším. Upřednostňuje obsahu příklady odpovědi, pokaždé, když je k dispozici. Generuje ukázkové odpovědi ze schémat, když jsou k dispozici schémata a příklady nejsou. Pokud se nenajdou příklady ani schémata, jsou vráceny odpovědí bez obsahu.

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|model odpovědi|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|--------------|
|Stavový kód|Určuje kód stavu odpovědi a slouží k výběru příslušné příklad nebo schématu.|Ne|200|
|Typ obsahu|Určuje `Content-Type` hodnota hlavičky odpovědi a slouží k výběru příslušné příklad nebo schématu.|Ne|Žádný|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, odchozí, na chyby

-   **Zásady obory:** všechny obory

##  <a name="Retry"></a> Zkuste to znovu
 `retry` Zásady provede její podřízené zásady jednou a potom jejich provádění, dokud opakování pokusů `condition` stane `false` nebo to zkuste znovu `count` je byl vyčerpán.

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

### <a name="example"></a>Příklad:
 V následujícím příkladu je předávání žádost opakovat až desetkrát pomocí algoritmu exponenciální opakování. Protože `first-fast-retry` je nastavena na hodnotu false, všechny opakované pokusy se vztahují algoritmus exponenciální opakování.

```xml

<retry
    condition="@(context.Response.StatusCode == 500)"
    count="10"
    interval="10"
    max-interval="100"
    delta="10"
    first-fast-retry="false">
        <forward-request />
</retry>

```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|retry|Kořenový element. Jako jeho podřízené prvky mohou obsahovat jiné zásady.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|condition|Logický literál nebo [výraz](api-management-policy-expressions.md) zadání, pokud by se měla zastavit opakovaných pokusů (`false`) nebo pokračování (`true`).|Ano|neuvedeno|
|count|Kladné číslo určující maximální počet opakování pokusu.|Ano|neuvedeno|
|interval|Kladné číslo v sekundách čekání intervalem mezi opakování pokusů.|Ano|neuvedeno|
|maximální interval|Kladné číslo určující maximální počet sekund počkejte, než interval mezi opakovanými pokusy. Používá se k implementaci algoritmus exponenciální opakování.|Ne|neuvedeno|
|delta|Kladné číslo v sekundách zadání přírůstku intervalu čekání. Používá se k implementaci algoritmy lineární a exponenciální opakování.|Ne|neuvedeno|
|první – rychlé opakování|Pokud hodnotu `true` , první pokus o opakování se provádí ihned.|Ne|`false`|

> [!NOTE]
>  Když pouze `interval` není zadána, **oprava** interval opakování probíhají.
> Když pouze `interval` a `delta` jsou určené, **lineární** interval opakování algoritmus se používá, kde se počítá čekat mezi opakovanými pokusy podle následující vzorec – `interval + (count - 1)*delta`.
> Když `interval`, `max-interval` a `delta` nejsou zadány, **exponenciální** interval opakování algoritmus použije, pokud doba čekání mezi opakované pokusy řešení roste exponenciálně každým z hodnoty `interval` do Hodnota `max-interval` podle následujících forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Všimněte si, že podřízená omezení použití zásad zdědí tyto zásady.

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="ReturnResponse"></a> Vrací odpověď
 `return-response` Zásady přeruší spuštění kanálu a vrátí výchozí nebo vlastní odpověď na volajícího. Je výchozí odpověď `200 OK` s hlavní části. Odpověď se dá nastavit pomocí kontextové proměnné nebo zásady příkazů. Pokud obě jsou k dispozici, odpověď obsažené v rámci kontextové proměnné je změněna příkazům zásady před vrácením řízení volajícímu.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<return-response response-variable-name="existing context variable">
  <set-header/>
  <set-body/>
  <set-status/>
</return-response>

```

### <a name="example"></a>Příklad:

```xml
<return-response>
   <set-status code="401" reason="Unauthorized"/>
   <set-header name="WWW-Authenticate" exists-action="override">
      <value>Bearer error="invalid_token"</value>
   </set-header>
</return-response>

```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|vrácení odpovědi|Kořenový element.|Ano|
|set – hlavička|A [nastavení hlaviček](api-management-transformation-policies.md#SetHTTPheader) prohlášení o zásadách.|Ne|
|Nastavit text|A [nastavit text](api-management-transformation-policies.md#SetBody) prohlášení o zásadách.|Ne|
|Nastavit stav|A [nastavit stav](api-management-advanced-policies.md#SetStatus) prohlášení o zásadách.|Ne|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|Název proměnné odpovědi|Název proměnné kontextu odkazován z, například upstreamové [požadavků na odeslání](api-management-advanced-policies.md#SendRequest) zásad a který obsahuje `Response` objektu|Volitelné.|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="SendOneWayRequest"></a> Odešlete žádost o jeden ze způsobů
 `send-one-way-request` Zásad odešle zadaný požadavek na zadanou adresu URL bez čekání na odpověď.

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

### <a name="example"></a>Příklad:
 Tato ukázková zásada ukazuje příklad použití `send-one-way-request` zásadu odeslat zprávu na Slack chatovací místnosti, pokud kód odpovědi HTTP je větší než nebo rovna hodnotě 500. Další informace o této ukázce najdete v tématu [použití externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|Odeslat jeden způsob, jak žádosti|Kořenový element.|Ano|
|url|Adresa URL požadavku.|Žádné if režimu = kopii. v opačném případě Ano.|
|method|Metoda HTTP pro žádost.|Žádné if režimu = kopii. v opačném případě Ano.|
|záhlaví|Hlavička požadavku. Použití více elementů záhlaví pro více záhlaví požadavku.|Ne|
|hlavní část|Text žádosti|Ne|
|certifikát pro ověřování|[Certifikát se má použít pro ověřování klientů](api-management-authentication-policies.md#ClientCertificate)|Ne|


### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|režim = "řetězec"|Určuje, zda se jedná novou žádost o nebo kopii aktuálního požadavku. V odchozí režim režim = kopírování neinicializuje datovou část požadavku.|Ne|Nová|
|jméno|Určuje název hlavičky, která se má nastavit.|Ano|neuvedeno|
|existuje akce|Určuje, jaká akce se má provést, když je už zadaná hlavičce. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu existujícího hlavičky.<br />-skip - nenahrazuje existující hodnota hlavičky.<br />-Přidat – přidá hodnotu do existující hodnota hlavičky.<br />-delete - odstraní hlavičku ze žádosti.<br /><br /> Pokud je nastavena na `override` uvedení několik záznamů se stejným názvem výsledků v hlavičce nastavena podle všechny položky (které bude uveden více než jednou), nastaví se pouze uvedené hodnoty ve výsledku.|Ne|přepsání|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="SendRequest"></a> Odeslání požadavku
 `send-request` Zásad odešle požadavek zadaný na zadanou adresu URL déle než sada hodnotu časového limitu čekání.

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

### <a name="example"></a>Příklad:
 Tento příklad ukazuje jeden způsob, jak ověřit odkaz tokenu pomocí autorizačního serveru. Další informace o této ukázce najdete v tématu [použití externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|požadavek na odeslání|Kořenový element.|Ano|
|url|Adresa URL požadavku.|Žádné if režimu = kopii. v opačném případě Ano.|
|method|Metoda HTTP pro žádost.|Žádné if režimu = kopii. v opačném případě Ano.|
|záhlaví|Hlavička požadavku. Použití více elementů záhlaví pro více záhlaví požadavku.|Ne|
|hlavní část|Text žádosti|Ne|
|certifikát pro ověřování|[Certifikát se má použít pro ověřování klientů](api-management-authentication-policies.md#ClientCertificate)|Ne|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|režim = "řetězec"|Určuje, zda se jedná novou žádost o nebo kopii aktuálního požadavku. V odchozí režim režim = kopírování neinicializuje datovou část požadavku.|Ne|Nová|
|Název proměnné odpovědi = "řetězec"|Název kontextové proměnné, která bude dostávat objektu odpovědi. Pokud proměnnou neexistuje, vytvoří se po úspěšné spuštění zásad a bude přístupné prostřednictvím [ `context.Variable` ](api-management-policy-expressions.md#ContextVariables) kolekce.|Ano|neuvedeno|
|časový limit = "celočíselné"|Interval časového limitu v sekundách, než volání adresy URL se nezdaří.|Ne|60|
|Ignorovat chybu|Pokud true a výsledky požadavku k chybě:<br /><br /> – Pokud je název proměnné odpovědi byla zadána, bude obsahovat hodnotu null.<br />– Pokud nebyl zadán název proměnné odpovědi, kontext. Požadavek se nebude aktualizovat.|Ne|false (nepravda)|
|jméno|Určuje název hlavičky, která se má nastavit.|Ano|neuvedeno|
|existuje akce|Určuje, jaká akce se má provést, když je už zadaná hlavičce. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu existujícího hlavičky.<br />-skip - nenahrazuje existující hodnota hlavičky.<br />-Přidat – přidá hodnotu do existující hodnota hlavičky.<br />-delete - odstraní hlavičku ze žádosti.<br /><br /> Pokud je nastavena na `override` uvedení několik záznamů se stejným názvem výsledků v hlavičce nastavena podle všechny položky (které bude uveden více než jednou), nastaví se pouze uvedené hodnoty ve výsledku.|Ne|přepsání|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="SetHttpProxy"></a> Nastavení proxy serveru HTTP
 `proxy` Zásad vám umožní pro směrování požadavků předávají do back-EndY přes proxy server HTTP. Mezi bránou a proxy server se podporuje jen protokol HTTP (nikoli HTTPS). Pouze ověřování NTLM a Basic.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Příklad:
Všimněte si použití [vlastnosti](api-management-howto-properties.md) jako hodnoty uživatelského jména a hesla, aby se zabránilo ukládání citlivých informací v dokumentu zásad.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|Proxy server|Kořenový element|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|Adresa URL = "řetězec"|Adresa URL proxy serveru ve formě http://host:port.|Ano|neuvedeno|
|uživatelské jméno = "řetězec"|Uživatelské jméno má být použit pro ověřování s proxy serverem.|Ne|neuvedeno|
|heslo = "řetězec"|Heslo má být použit pro ověřování s proxy serverem.|Ne|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí

-   **Zásady obory:** všechny obory

##  <a name="SetRequestMethod"></a> Požadavek set – metoda
 `set-method` Zásad vám umožní změnit metoda požadavku HTTP pro žádost.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Příklad:
 Tyto ukázkové zásady, která používá `set-method` zásad ukazuje příklad odeslat zprávu na Slack chatovací místnosti, pokud kód odpovědi HTTP je větší než nebo rovna hodnotě 500. Další informace o této ukázce najdete v tématu [použití externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|set – metoda|Kořenový element. Hodnota elementu, který určuje metodu HTTP.|Ano|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, při chybě

-   **Zásady obory:** všechny obory

##  <a name="SetStatus"></a> Sada stavový kód
 `set-status` Zásady nastaví stavový kód HTTP se zadanou hodnotou.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Příklad:
 Tento příklad ukazuje, jak vrátit skončí odpovědí 401, pokud autorizační token je neplatný. Další informace najdete v tématu [použití externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|Nastavit stav|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|kód = "celočíselné"|Stavový kód HTTP se vraťte.|Ano|neuvedeno|
|z důvodu = "řetězec"|Popis důvod vrátí stavový kód.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** odchozí, back-endu na chyby
-   **Zásady obory:** všechny obory

##  <a name="set-variable"></a> Nastavit proměnnou
 `set-variable` Deklaruje zásad [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné a přiřazuje jí hodnotu určené pomocí [výraz](api-management-policy-expressions.md) nebo textový literál. Pokud výraz obsahuje literál se převedou na řetězec a typ hodnoty budou `System.String`.

###  <a name="set-variablePolicyStatement"></a> Prohlášení o zásadách

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

###  <a name="set-variableExample"></a> Příklad
 Následující příklad ukazuje nastavení proměnné zásad v části příchozí. Vytvoří proměnné toto nastavení zásad `isMobile` logická [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné, která je nastavena na hodnotu true, pokud `User-Agent` záhlaví obsahuje text požadavku `iPad` nebo `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|nastavit proměnnou|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|jméno|Název proměnné.|Ano|
|hodnota|Hodnota proměnné. To může být výraz nebo literálovou hodnotou.|Ano|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby
-   **Zásady obory:** všechny obory

###  <a name="set-variableAllowedTypes"></a> Povolené typy
 Výrazy použité v `set-variable` zásad musí vracet instanci jednoho z následujících základních typů.

-   System.Boolean
-   System.SByte
-   System.Byte
-   System.UInt16
-   System.UInt32
-   System.UInt64
-   System.Int16
-   System.Int32
-   System.Int64
-   System.Decimal
-   System.Single
-   System.Double
-   System.Guid
-   System.String
-   System.Char
-   System.DateTime
-   Hodnota System.TimeSpan
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
-   System.String?
-   System.Char?
-   System.DateTime?

##  <a name="Trace"></a> Trasování
 `trace` Zásad přidá řetězec do [pro inspekci API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) výstup. Zásady spustí, jenom když trasování se aktivuje, to znamená `Ocp-Apim-Trace` hlavička požadavku je k dispozici a nastavené na `true` a `Ocp-Apim-Subscription-Key` hlavička požadavku je k dispozici a má platný klíč spojený s účtem správce.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<trace source="arbitrary string literal"/>
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|Trasování|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|source|Řetězcový literál smysl v daném prohlížeči trasování a zadáte zdroj zprávy.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** všechny obory

##  <a name="Wait"></a> Počkej
 `wait` Zásady jeho bezprostřední podřízený zásady spustí paralelně a čeká na všechny nebo jeden z jeho bezprostřední podřízený zásady k dokončení, než bude dokončeno. Počkejte zásad může mít jako zásady ochrany před jeho bezprostřední podřízený [požadavků na odeslání](api-management-advanced-policies.md#SendRequest), [získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey), a [řízení toku](api-management-advanced-policies.md#choose) zásady.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Příklad:
 V následujícím příkladu jsou dva `choose` zásady jako zásady bezprostřední podřízený `wait` zásad. Každá z těchto `choose` zásady spustí paralelně. Každý `choose` zásad se pokusí načíst hodnotu uloženou v mezipaměti. Pokud dojde k neúspěšnému přístupu do mezipaměti, back-end službu je volána k zadejte hodnotu. V tomto příkladu `wait` zásad dokončena, dokud se všechny jeho bezprostřední podřízený zásady dokončit, protože `for` atribut je nastaven na `all`.   V tomto příkladu kontextové proměnné (`execute-branch-one`, `value-one`, `execute-branch-two`, a `value-two`) jsou deklarované mimo rozsah tento příklad zásady.

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

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|Počkej|Kořenový element. Může obsahovat jako podřízené prvky pouze `send-request`, `cache-lookup-value`, a `choose` zásady.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|pro|Určuje, zda `wait` zásady vyčká pro všechny zásady bezprostřední podřízený jako dokončené nebo jen jeden. Povolené hodnoty jsou:<br /><br /> -   `all` -čekat na dokončení všech zásad bezprostřední podřízený<br />-žádné – počkejte jakékoli bezprostřední podřízený zásadu pro dokončení. Po dokončení první zásady bezprostřední podřízený `wait` dokončení zásad a provádění dalších zásad bezprostřední podřízený se ukončí.|Ne|all|

### <a name="usage"></a>Využití

Tyto zásady můžete použít v následujících zásad [oddíly](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu
-   **Zásady obory:** všechny obory

## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:
+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Výrazy zásad](api-management-policy-expressions.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
