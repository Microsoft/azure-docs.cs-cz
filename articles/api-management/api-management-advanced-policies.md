---
title: Pokročilé zásady Azure API Management | Microsoft Docs
description: Další informace o pokročilé zásady, které jsou k dispozici pro použití v Azure API Management.
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
ms.openlocfilehash: dcd4d28341e766baeaf6d581a69312cc33a0282a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="api-management-advanced-policies"></a>Pokročilé zásady API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AdvancedPolicies"></a> Rozšířené zásady

-   [Řízení toku](api-management-advanced-policies.md#choose) – podmíněně platí příkazy zásad na základě výsledků vyhodnocení logická hodnota [výrazy](api-management-policy-expressions.md).
-   [Předat dál žádost](#ForwardRequest) -předá požadavek back-end službu.
-   [Omezit souběžnosti](#LimitConcurrency) -brání uzavřena zásady z provádění více než určitý počet požadavků současně.
-   [Protokol do centra událostí](#log-to-eventhub) -odešle zprávy v zadaném formátu do centra událostí definované entity protokolovacího nástroje.
-   [Model odpovědi](#mock-response) -přerušení způsobených kanálu provádění a vrátí mocked odpověď přímo na volajícího.
-   [Opakujte](#Retry) -opakování provádění příkazů závorkách zásad, pokud a dokud nebude splněna podmínka. Spuštění se opakovaly zadaným časovým intervalům a až zadaný počet opakování.
-   [Vrátí odpověď](#ReturnResponse) -přerušení způsobených kanálu provádění a vrátí zadanou odpověď přímo na volajícího.
-   [Odeslání žádosti o jednorázové jednoduché](#SendOneWayRequest) -odešle požadavek na zadanou adresu URL bez čekání na odpověď.
-   [Odeslání požadavku](#SendRequest) -odešle požadavek na zadanou adresu URL.
-   [Nastavení proxy serveru HTTP](#SetHttpProxy) -umožňuje trasy předané požadavky prostřednictvím proxy serveru HTTP.
-   [Nastaví metodu požadavku](#SetRequestMethod) -vám umožní změnit metodu protokolu HTTP pro žádost.
-   [Nastavit stavový kód](#SetStatus) -změní stavový kód protokolu HTTP se zadanou hodnotou.
-   [Nastavená proměnná](api-management-advanced-policies.md#set-variable) -potrvají hodnotu v pojmenovaná [kontextu](api-management-policy-expressions.md#ContextVariables) proměnná pro pozdější přístup.
-   [Trasování](#Trace) -přidá řetězec do [rozhraní API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) výstup.
-   [Počkejte](#Wait) -čeká pro uzavřené [odeslán požadavek na](api-management-advanced-policies.md#SendRequest), [získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey), nebo [řízení toku](api-management-advanced-policies.md#choose) zásady k dokončení než budete pokračovat.

##  <a name="choose"></a> Tok řízení
 `choose` Zásada závorkách zásady vytvořit na základě výsledku vyhodnocení logické výrazy, podobně jako if pak else nebo přepínač příkazy v programovacím jazyce.

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

 Zásada řízení toku musí obsahovat alespoň jeden `<when/>` elementu. `<otherwise/>` Prvek je volitelný. Podmínky v `<when/>` elementy jsou vyhodnocovány v pořadí podle jejich vzhled v rámci zásady. Příkazy zásad v rámci první uzavřené `<when/>` element s podmínku atributu rovná `true` se použijí. Zásady v rámci uzavřené `<otherwise/>` elementu, pokud existuje, bude použito v případě všech z `<when/>` element podmínku atributy jsou `false`.

### <a name="examples"></a>Příklady

####  <a name="ChooseExample"></a> Příklad
 Následující příklad ukazuje [nastavená proměnná](api-management-advanced-policies.md#set-variable) zásady a dvě zásady řízení toku.

 Nastavit proměnnou zásady je v části příchozí a vytvoří `isMobile` Boolean [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné, která je nastavena na hodnotu true, pokud `User-Agent` žádosti záhlaví obsahuje text `iPad` nebo `iPhone`.

 První zásada toku řízení je taky v části příchozí a podmíněně použije jednu ze dvou [nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) zásady v závislosti na hodnotě `isMobile` kontextové proměnné.

 Druhá zásada toku řízení je v části odchozí a podmíněně se vztahuje [XML převést na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) zásady při `isMobile` je nastaven na `true`.

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
 Tento příklad ukazuje, jak provést filtrování obsahu odebráním datové prvky. z odpovědi přijal od služby back-end při použití `Starter` produktu. Ukázka konfiguraci a použití této zásady, najdete v části [cloudu zahrnují díl 177: rozhraní API funkce správy více s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převíjení vpřed na 34:30. Spuštění na 31:50 zobrazíte přehled [rozhraní tmavý Sky prognózy API](https://developer.forecast.io/) použít v této ukázce.

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
|Kdy|Podmínku, která má použít pro `if` nebo `ifelse` části `choose` zásad. Pokud `choose` zásad obsahuje více `when` oddíly, jsou vyhodnocovány postupně. Jednou `condition` z když se vyhodnocuje element `true`, žádné další `when` vyhodnocení podmínek.|Ano|
|v opačném případě|Obsahuje fragmentu zásad, který se má použít, pokud žádná z `when` vyhodnocení podmínky `true`.|Ne|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|podmínka = "logický výraz &#124; Boolean konstanta"|Logický výraz nebo konstantu, která vyhodnotí při obsahující `when` zásad vyhodnotí.|Ano|

###  <a name="ChooseUsage"></a> Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="ForwardRequest"></a> Předat dál požadavku
 `forward-request` Zásada předá příchozí požadavek na back-end službu zadanou v žádosti [kontextu](api-management-policy-expressions.md#ContextVariables). Adresa URL back-end služby je zadaný v rozhraní API [nastavení](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) a můžete změnit pomocí [nastavení back-end služby](api-management-transformation-policies.md) zásad.

> [!NOTE]
>  Odebrání výsledků této zásady v požadavku není předávaných do back-end služby a zásady v části odchozí vyhodnocují hned po úspěšném dokončení zásad v části příchozí.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<forward-request timeout="time in seconds" follow-redirects="true | false"/>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad:
 Tyto zásady úrovně rozhraní API předává všechny požadavky na back-end služby s časový limit na 60 sekund.

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
 Tato zásada na úrovni operace používá `base` element dědí zásady back-end z nadřazeného oboru úrovně rozhraní API.

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
 Tato zásada na úrovni operaci explicitně předává všechny požadavky na back-end službu s časovým limitem 120 a nedědí nadřazené úrovně back-end zásada rozhraní API.

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
 Tato zásada na úrovni operaci nepředává požadavky na back-end službu.

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
|časový limit = "celé číslo"|Interval vypršení časového limitu v sekundách, než volání službě back-end se nezdaří.|Ne|300 sekund|
|postupujte podle přesměrování = "true &#124; false"|Určuje, zda jsou přesměrování z back-end službu následuje bránu nebo vrácen volajícímu.|Ne|false (nepravda)|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** back-end
-   **Zásady obory:** všechny obory

##  <a name="LimitConcurrency"></a> Limit souběžnosti
 `limit-concurrency` Zásady zabrání závorkách zásady provádění více než určitý počet požadavků v daném okamžiku. Při překročení toto číslo, selže nové žádosti o okamžitě s kódem stavu 429 – příliš mnoho požadavků.

###  <a name="LimitConcurrencyStatement"></a> Prohlášení o zásadách

```xml
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->
</limit-concurrency>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad:
 Následující příklad ukazuje, jak omezit počet požadavků, které jsou předávány back-end na základě hodnoty proměnné kontextu.

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
|limit souběžnosti|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|--------------|
|key|Řetězec. Výraz povoleny. Určuje obor souběžnosti. Může být sdílen více zásad.|Ano|neuvedeno|
|maximální počet|Celé číslo. Určuje maximální počet požadavků, které jsou k zadání zásad.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="log-to-eventhub"></a> Protokol do centra událostí
 `log-to-eventhub` Zásad odešle zprávy v zadaném formátu do centra událostí definované entity protokolovacího nástroje. Jak již název napovídá, zásady se používá pro uložení vybraného požadavku nebo odpovědi kontextové informace pro analýzu online nebo offline.

> [!NOTE]
>  Podrobné informace o konfiguraci centra událostí a protokolování událostí najdete v tématu [protokolování událostí správy rozhraní API pro Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">
  Expression returning a string to be logged
</log-to-eventhub>

```

### <a name="example"></a>Příklad:
 Libovolný řetězec slouží jako hodnota má být přihlášen Event Hubs. V tomto příkladu datum a čas, název služby pro nasazení, id žádosti, ip adresu a název operace pro všechny příchozí volání se protokolují do centra událostí protokolovacího nástroje zaregistrována `contoso-logger` id.

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
|log-to-eventhub|Kořenový element. Hodnota tohoto elementu je řetězec k přihlášení do vašeho centra událostí.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|id protokolovacího nástroje|Id protokolovacího nástroje registrován u služby API Management.|Ano|
|id oddílu|Určuje index oddílu, které jsou odesílány zprávy.|Volitelné. Tento atribut nemusí být použit, pokud `partition-key` se používá.|
|klíč oddílu|Určuje hodnotu sloužící pro přiřazení k oddílu, odesílání zpráv.|Volitelné. Tento atribut nemusí být použit, pokud `partition-id` se používá.|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="mock-response"></a> Imitované odpovědi
`mock-response`, Jako název znamená, že se používá model rozhraní API a operace. To zruší provádění běžný kanál a vrátí mocked odpověď na volajícího. Zásady se vždy pokusí vrátit odpovědí nejvyšší přesnosti. Dává přednost obsahu příklady odpovědi, vždy, když je k dispozici. Generuje ukázka odpovědí z schémata, když jsou k dispozici schémata a příklady nejsou. Pokud ani příklady nebo schémata jsou nalezena, vrátí se odpovědi s žádný obsah.

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
|mock-response|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|--------------|
|status-code|Určuje stavový kód odpovědi a slouží k výběru odpovídající příklad nebo schéma.|Ne|200|
|content-type|Určuje `Content-Type` hodnota hlavičky odpovědi a slouží k výběru odpovídající příklad nebo schéma.|Ne|Žádný|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozích, odchozích, při chybě

-   **Zásady obory:** všechny obory

##  <a name="Retry"></a> Opakování
 `retry` Zásady provede jeho podřízených zásady jednou a pak opakuje jejich spuštění, dokud opakovaném `condition` stane `false` nebo opakujte `count` je vyčerpání.

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
 V následujícím příkladu se pokus o žádost o přesměrování až desetkrát pomocí algoritmu exponenciální opakování. Vzhledem k tomu `first-fast-retry` není nastaven na hodnotu false, všechny pokusy podléhají algoritmus exponenciální opakování.

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
|retry|Kořenový element. Může obsahovat další zásady jako jeho podřízených elementů.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|podmínka|Logická hodnota literál nebo [výraz](api-management-policy-expressions.md) zadání, pokud by se měla zastavit opakovaných pokusů (`false`) nebo pokračování (`true`).|Ano|neuvedeno|
|počet|Kladné číslo určující maximální počet opakování pokusu.|Ano|neuvedeno|
|interval|Pokusí se v sekundách zadání intervalu čekání mezi opakovaném kladné číslo.|Ano|neuvedeno|
|max-interval|Kladné číslo v sekundách určení maximální Počkejte, než interval mezi pokusy o opakování. Slouží k implementaci algoritmu exponenciální opakování.|Ne|neuvedeno|
|delta|Kladné číslo v sekundách zadání přírůstek intervalu čekání. Slouží k implementaci algoritmy lineární a exponenciální opakování.|Ne|neuvedeno|
|Zkuste zopakovat první fast|Pokud nastavena na `true` , první pokus o opakování provádí okamžitě.|Ne|`false`|

> [!NOTE]
>  Když pouze `interval` není zadaný, **pevné** jsou prováděny interval opakování.
> Když pouze `interval` a `delta` nejsou zadány, **lineární** se použije algoritmus interval opakování, kde se počítá doba čekání mezi opakovanými pokusy podle následující vzorec - `interval + (count - 1)*delta`.
> Když `interval`, `max-interval` a `delta` jsou nastaveny, **exponenciální** interval opakování algoritmus se použije, kde je doba čekání mezi jednotlivými pokusy o odeslání zvětšování exponenciálně od hodnoty `interval` k Hodnota `max-interval` podle následující forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Všimněte si, že podřízená omezení použití zásad zdědí tuto zásadu.

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="ReturnResponse"></a> Vrátí odpověď
 `return-response` Zásady zruší provádění zřetězeného příkazu a vrací výchozí nebo vlastní odpověď na volajícího. Výchozí odpověď je `200 OK` se žádné textem. Vlastní odpovědi lze zadat pomocí kontextu proměnné nebo zásady příkazy. Pokud jsou obě k dispozici, odpověď obsažené v kontextové proměnné je upravit příkazy zásad před vrácením volajícímu.

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
|vrátí odpověď|Kořenový element.|Ano|
|set-header|A [sadu hlaviček](api-management-transformation-policies.md#SetHTTPheader) prohlášení o zásadách.|Ne|
|Sada textu|A [set textu](api-management-transformation-policies.md#SetBody) prohlášení o zásadách.|Ne|
|set-status|A [nastavit stav](api-management-advanced-policies.md#SetStatus) prohlášení o zásadách.|Ne|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|response-variable-name|Název kontextové proměnné na něj odkazovat z, například předcházejícího [odeslán požadavek](api-management-advanced-policies.md#SendRequest) zásady a obsahující `Response` objektu|Volitelné.|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="SendOneWayRequest"></a> Odeslání žádosti o jednorázové jednoduché
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
 Tato ukázková zásada ukazuje příklad použití `send-one-way-request` zásad k odeslání zprávy do Slack chatovací místnosti, pokud kód odpovědi HTTP je větší než nebo rovna hodnotě 500. Další informace o této ukázky najdete v tématu [pomocí externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
|send-one-way-request|Kořenový element.|Ano|
|Adresa URL|Adresa URL požadavku.|Pokud žádné režimu = kopie; v opačném případě Ano.|
|metoda|Metoda HTTP pro žádost.|Pokud žádné režimu = kopie; v opačném případě Ano.|
|záhlaví|Hlavička požadavku. Použijte více prvky záhlaví pro více hlavičky žádosti.|Ne|
|hlavní část|Text žádosti|Ne|
|ověřovací certifikát|[Certifikát se má použít pro ověřování klientů](api-management-authentication-policies.md#ClientCertificate)|Ne|


### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|režim = "řetězec"|Určuje, jestli je nový požadavek nebo kopii aktuálního požadavku. V odchozí režim režim = kopie neinicializuje textu požadavku.|Ne|Nová|
|jméno|Určuje název záhlaví nastavit.|Ano|neuvedeno|
|existuje akce|Určuje, jaká opatření se mají provést, pokud hlavička byl již zadán. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu existující záhlaví.<br />-skip - nenahrazuje existující hodnotu hlavičky.<br />-připojit - připojí hodnotu pro existující hodnotu hlavičky.<br />-delete - odstraní hlavičku ze žádosti.<br /><br /> Pokud nastavíte hodnotu `override` uvedení více položek se stejným názvem výsledků v hlavičce je nastavena podle všech položek (které budou uvedeny vícekrát); pouze uvedené hodnoty budou nastaveny ve výsledku.|Ne|přepsání|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="SendRequest"></a> Odeslání požadavku
 `send-request` Zásad odešle zadaný požadavek na zadanou adresu URL, čekání déle než nastavte hodnotu časového limitu.

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
 Tento příklad ukazuje jeden způsob, jak ověřit odkaz tokenu se serverem ověřování. Další informace o této ukázky najdete v tématu [pomocí externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
|odeslání žádosti|Kořenový element.|Ano|
|Adresa URL|Adresa URL požadavku.|Pokud žádné režimu = kopie; v opačném případě Ano.|
|metoda|Metoda HTTP pro žádost.|Pokud žádné režimu = kopie; v opačném případě Ano.|
|záhlaví|Hlavička požadavku. Použijte více prvky záhlaví pro více hlavičky žádosti.|Ne|
|hlavní část|Text žádosti|Ne|
|ověřovací certifikát|[Certifikát se má použít pro ověřování klientů](api-management-authentication-policies.md#ClientCertificate)|Ne|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|režim = "řetězec"|Určuje, jestli je nový požadavek nebo kopii aktuálního požadavku. V odchozí režim režim = kopie neinicializuje textu požadavku.|Ne|Nová|
|response-variable-name="string"|Pokud není přítomný, `context.Response` se používá.|Ne|neuvedeno|
|časový limit = "celé číslo"|Interval vypršení časového limitu v sekundách, než volání na adresu URL se nezdaří.|Ne|60|
|Ignorovat chybu|Pokud hodnotu true a dojde k chybě žádost:<br /><br /> – Pokud je název proměnné odpověď byla zadána, bude obsahovat hodnotu null.<br />– Pokud nebyl zadán název proměnné odpovědi, kontextu. Požadavek nebude aktualizován.|Ne|false (nepravda)|
|jméno|Určuje název záhlaví nastavit.|Ano|neuvedeno|
|existuje akce|Určuje, jaká opatření se mají provést, pokud hlavička byl již zadán. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu existující záhlaví.<br />-skip - nenahrazuje existující hodnotu hlavičky.<br />-připojit - připojí hodnotu pro existující hodnotu hlavičky.<br />-delete - odstraní hlavičku ze žádosti.<br /><br /> Pokud nastavíte hodnotu `override` uvedení více položek se stejným názvem výsledků v hlavičce je nastavena podle všech položek (které budou uvedeny vícekrát); pouze uvedené hodnoty budou nastaveny ve výsledku.|Ne|přepsání|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="SetHttpProxy"></a> Server proxy protokolu HTTP sady
 `proxy` Zásad umožňuje směrování požadavků předávaných do back-EndY prostřednictvím proxy serveru HTTP. Mezi brány a proxy je podporována jen protokol HTTP (nikoli HTTPS). Pouze ověřování NTLM a Basic.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<proxy url="http://hostname-or-ip:port" username="username" password="password" />

```

### <a name="example"></a>Příklad:
Všimněte si použití [vlastnosti](api-management-howto-properties.md) jako hodnoty uživatelské jméno a heslo, aby se zabránilo ukládání citlivých informací v dokumentu zásad.

```xml
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />

```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|proxy|Kořenový element|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|Adresa URL = "řetězec"|Adresa URL proxy serveru ve formě http://host:port.|Ano|neuvedeno|
|username="string"|Uživatelské jméno má být použit pro ověřování s proxy serverem.|Ne|neuvedeno|
|heslo = "řetězec"|Heslo má být použit pro ověřování s proxy serverem.|Ne|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozí

-   **Zásady obory:** všechny obory

##  <a name="SetRequestMethod"></a> Žádost o set – metoda
 `set-method` Zásad vám umožní změnit metodu požadavku HTTP pro žádost.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-method>METHOD</set-method>

```

### <a name="example"></a>Příklad:
 Tato ukázka zásadu, která používá `set-method` zásad ukazuje příklad odesílání zprávy Slack chatovací místnosti, pokud kód odpovědi HTTP je větší než nebo rovna hodnotě 500. Další informace o této ukázky najdete v tématu [pomocí externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).

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
|set – metoda|Kořenový element. Hodnota elementu určuje metodu HTTP.|Ano|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozí, při chybě

-   **Zásady obory:** všechny obory

##  <a name="SetStatus"></a> Sada stavový kód
 `set-status` Zásady nastaví stavový kód protokolu HTTP se zadanou hodnotou.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-status code="" reason=""/>

```

### <a name="example"></a>Příklad:
 Tento příklad ukazuje, jak vracet odpovědi 401, pokud autorizační token je neplatný. Další informace najdete v tématu [pomocí externích služeb ze služby Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)

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
|set-status|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|kód = "celé číslo"|Stavový kód HTTP vrátit.|Ano|neuvedeno|
|důvod = "řetězec"|Popis důvod pro vrácení stavový kód.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** odchozí, back-end, při chybě
-   **Zásady obory:** všechny obory

##  <a name="set-variable"></a> Nastavená proměnná
 `set-variable` Deklaruje zásad [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné a přiřadí ji má hodnotu prostřednictvím [výraz](api-management-policy-expressions.md) nebo řetězcový literál. Pokud výraz obsahuje literál jej bude možné převést na řetězec a typ hodnoty bude `System.String`.

###  <a name="set-variablePolicyStatement"></a> Prohlášení o zásadách

```xml
<set-variable name="variable name" value="Expression | String literal" />
```

###  <a name="set-variableExample"></a> Příklad
 Následující příklad ukazuje nastavení proměnné zásad v části příchozí. Toto nastavení zásad proměnné vytvoří `isMobile` logickou [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné, která je nastavena na hodnotu true, pokud `User-Agent` žádosti záhlaví obsahuje text `iPad` nebo `iPhone`.

```xml
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />
```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|nastavená proměnná|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|
|---------------|-----------------|--------------|
|jméno|Název proměnné.|Ano|
|hodnota|Hodnota proměnné. To může být výraz nebo hodnota literálu.|Ano|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě
-   **Zásady obory:** všechny obory

###  <a name="set-variableAllowedTypes"></a> Povolené typy
 Výrazy použité v `set-variable` zásady musí vrátit jednu z následujících základních typů.

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
-   System.String?
-   System.Char?
-   System.DateTime?

##  <a name="Trace"></a> trasování
 `trace` Zásad přidá řetězec do [rozhraní API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) výstup. Zásady, které budou spuštěny pouze při trasování je aktivaci, tj. `Ocp-Apim-Trace` hlavička požadavku je přítomen a nastavené na `true` a `Ocp-Apim-Subscription-Key` hlavička požadavku je k dispozici a obsahuje platný klíč přidružený k účtu správce.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml

<trace source="arbitrary string literal"/>
    <!-- string expression or literal -->
</trace>

```

### <a name="elements"></a>Elementy

|Element|Popis|Požaduje se|
|-------------|-----------------|--------------|
|trasování|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|zdroj|Řetězcový literál smysl prohlížeče trasování a zadání zdroj zprávy.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** všechny obory

##  <a name="Wait"></a> Počkej
 `wait` Zásady spustí své zásady bezprostředně podřízené paralelně a čeká na všech nebo jednoho z jeho bezprostředně podřízené zásady pro provedení úkolu před jeho dokončení. Počkejte zásad může mít jako jeho bezprostředně podřízené [odeslán požadavek na](api-management-advanced-policies.md#SendRequest), [získat hodnotu z mezipaměti](api-management-caching-policies.md#GetFromCacheByKey), a [řízení toku](api-management-advanced-policies.md#choose) zásady.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<wait for="all|any">
  <!--Wait policy can contain send-request, cache-lookup-value,
        and choose policies as child elements -->
</wait>

```

### <a name="example"></a>Příklad:
 V následujícím příkladu jsou uvedeny dvě `choose` zásady jako bezprostředně podřízené zásady `wait` zásad. Každá z těchto `choose` zásady spustí paralelně. Každý `choose` zásady, pokusí se načíst hodnotu uloženou v mezipaměti. Pokud dojde k neúspěšnému přístupu do mezipaměti, se nazývá back-end službu do zadejte hodnotu. V tomto příkladu `wait` zásad dokončena, dokud se všechny jeho podřízené okamžitou zásady dokončit, protože `for` je nastavena na hodnotu `all`.   V tomto příkladu kontextu proměnné (`execute-branch-one`, `value-one`, `execute-branch-two`, a `value-two`) jsou deklarované mimo obor Tento příklad zásady.

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
|Počkej|Kořenový element. Může obsahovat pouze podřízené elementy `send-request`, `cache-lookup-value`, a `choose` zásady.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|for|Určuje, zda `wait` zásad čeká na všechny zásady bezprostředně podřízené jako dokončená nebo jenom jeden. Povolené hodnoty jsou:<br /><br /> -   `all` -čekat na všechny zásady bezprostředně podřízené k dokončení<br />-všechny - počkejte všechny bezprostředně podřízené zásadu pro dokončení. Po dokončení prvního bezprostředně podřízené zásad `wait` zásad dokončí a provádění dalších zásad bezprostředně podřízené je ukončen.|Ne|all|

### <a name="usage"></a>Využití

Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end
-   **Zásady obory:** všechny obory

## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:
+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Výrazy zásad](api-management-policy-expressions.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
