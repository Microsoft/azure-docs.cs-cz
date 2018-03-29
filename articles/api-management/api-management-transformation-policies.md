---
title: Zásad transformace Azure API Management | Microsoft Docs
description: Další informace o zásad transformace, která je k dispozici pro použití v Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 3eb9d6851c30f11980d47d4e48b158217e41995d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="api-management-transformation-policies"></a>Transformace zásady služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](http://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Zásad transformace

-   [Převést JSON do XML](api-management-transformation-policies.md#ConvertJSONtoXML) – převede požadavku nebo odpovědi textu z JSON do XML.

-   [Převést na JSON XML](api-management-transformation-policies.md#ConvertXMLtoJSON) – převede požadavku nebo odpovědi textu z XML do formátu JSON.

-   [Najít a nahradit řetězec v textu](api-management-transformation-policies.md#Findandreplacestringinbody) – najde dílčí řetězec požadavku nebo odpovědi a nahradí je jiný dílčí řetězec.

-   [Maskování adresy URL v obsahu](api-management-transformation-policies.md#MaskURLSContent) -znovu zapíše (masky) odkazy v odpovědi body tak, aby ukazovaly na ekvivalentní propojení prostřednictvím brány.

-   [Nastavení back-end služby](api-management-transformation-policies.md#SetBackendService) -změny službě back-end pro příchozí žádosti.

-   [Tělo nastavit](api-management-transformation-policies.md#SetBody) -nastaví obsah zprávy pro příchozí a odchozí požadavky.

-   [Set – hlavička protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) – přiřazuje hodnotu existující odpovědi nebo hlavička požadavku nebo přidá hlavičku odpovědi nebo žádost o nový.

-   [Nastavte parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) – přidá, nahradí hodnotu nebo odstraní parametr řetězce dotazu požadavku.

-   [Přepsání adresy URL](api-management-transformation-policies.md#RewriteURL) – převede adresu URL požadavku z jeho veřejné formuláře do formuláře očekává webovou službou.

-   [Transformace XML pomocí transformaci XSLT](api-management-transformation-policies.md#XSLTransform) -použije transformaci XSL na XML v textu požadavku nebo odpovědi.

##  <a name="ConvertJSONtoXML"></a> Převést JSON do XML
 `json-to-xml` Zásad převede text požadavku nebo odpovědi JSON do XML.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Příklad:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <json-to-xml apply="always" consider-accept-header="false" parse-date="false"/>
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|json-to-xml|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|použít|Musí být nastaven na jednu z následujících hodnot.<br /><br /> -vždy - vždy použít převod.<br />obsah json typ – převod pouze v případě, že přítomnost JSON určuje hlavičku odpovědi Content-Type.|Ano|neuvedeno|
|consider-accept-header|Musí být nastaven na jednu z následujících hodnot.<br /><br /> Pokud se vyžaduje JSON v požadavku hlavička Accept - true – použít převod.<br />-false – vždy použít převod.|Ne|true (pravda)|
|parse-date|Pokud nastavíte hodnotu `false` hodnot data zkopírují jednoduše během transformace|Ne|true (pravda)|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozích, odchozích, při chybě

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="ConvertXMLtoJSON"></a> Převod XML do formátu JSON
 `xml-to-json` Zásad převede text požadavku nebo odpovědi z XML do formátu JSON. Tato zásada umožňuje modernizovat rozhraní API podle jen XML back-end webové služby.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Příklad:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <outbound>
        <base />
        <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
    </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|xml-to-json|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|Typ|Musí být nastaven na jednu z následujících hodnot.<br /><br /> -javascript-friendly - převedený JSON má popisný JavaScript vývojářům formuláře.<br />převedený JSON - direct - odráží struktura původního dokumentu XML.|Ano|neuvedeno|
|použít|Musí být nastaven na jednu z následujících hodnot.<br /><br /> Převeďte – vždy - vždy.<br />obsah typu xml - převést pouze v případě, že hlavičku odpovědi Content-Type označuje přítomnost XML.|Ano|neuvedeno|
|consider-accept-header|Musí být nastaven na jednu z následujících hodnot.<br /><br /> Pokud se vyžaduje XML v žádosti o hlavička Accept - true – použít převod.<br />-false – vždy použít převod.|Ne|true (pravda)|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozích, odchozích, při chybě

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="Findandreplacestringinbody"></a> Najít a nahradit řetězec v textu
 `find-and-replace` Zásady najde dílčí řetězec požadavku nebo odpovědi a nahradí je jiný dílčí řetězec.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Příklad:

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|hledání a nahrazování|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|od|Hledaný řetězec.|Ano|neuvedeno|
|na|Náhradní řetězec. Zadejte řetězec nulové délky nahrazení odebrat hledaný řetězec.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="MaskURLSContent"></a> Maska adresy URL v obsahu
 `redirect-content-urls` Zásad znovu zapíše odkazy (masky) v textu odpovědi tak, aby ukazovaly na ekvivalentní propojení prostřednictvím brány. Použijte v části odchozí znovu zapsat odkazy text odpovědi, aby byly bodu k bráně. Použijte v části příchozí pro opačný efekt.

> [!NOTE]
>  Tato zásada nezmění žádné hodnoty hlavičky, jako `Location` hlavičky. Chcete-li změnit hodnoty hlavičky, použijte [set-– hlavička](api-management-transformation-policies.md#SetHTTPheader) zásad.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<redirect-content-urls />
```

### <a name="example"></a>Příklad:

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|redirect-content-urls|Kořenový element.|Ano|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetBackendService"></a> Nastavení back-end služby
 Použití `set-backend-service` zásady pro přesměrování příchozí žádosti na jiný back-end než verze zadaná v nastavení rozhraní API pro tuto operaci. Tato zásada změny back-end základní adresa URL služby příchozích požadavků verze zadaná v zásadách.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-backend-service base-url="base URL of the backend service" />
```

### <a name="example"></a>Příklad:

```xml
<policies>
    <inbound>
        <choose>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2013-05")">
                <set-backend-service base-url="http://contoso.com/api/8.2/" />
            </when>
            <when condition="@(context.Request.Url.Query.GetValueOrDefault("version") == "2014-03")">
                <set-backend-service base-url="http://contoso.com/api/9.1/" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
V tomto příkladu nastavení zásad služby back-end směruje požadavky na základě hodnoty verze předaný služby back-end jiný než ten, který je zadaný v rozhraní API v řetězci dotazu.

Původně adresu URL základní služby back-end je odvozená od nastavení rozhraní API. Proto adrese URL žádosti `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` stane `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` kde `http://contoso.com/api/10.4/` je adresa URL back-end služby zadaný v nastavení rozhraní API.

Když [< zvolte\> ](api-management-advanced-policies.md#choose) platí prohlášení o zásadách adresu URL základní služby back-end může změnit znovu buď `http://contoso.com/api/8.2` nebo `http://contoso.com/api/9.1`, v závislosti na základě hodnoty parametru dotazu požadavku verze. Například, pokud je hodnota `"2013-15"` poslední žádosti se změní na adresu URL `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Pokud transformace požadavku je další požadované, ostatní [zásad transformace](api-management-transformation-policies.md#TransformationPolicies) lze použít. Chcete-li například odeberte parametr dotazu verze teď, když se směrováním požadavku na konkrétní back-end verze [nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) zásadu lze použít k odebrání atributu teď redundantní verze.

### <a name="example"></a>Příklad:

```xml
<policies>
    <inbound>
        <set-backend-service backend-id="my-sf-service" sf-partition-key="@(context.Request.Url.Query.GetValueOrDefault("userId","")" sf-replica-type="primary" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
V tomto příkladu zásady přesměruje požadavek na služby prostředků infrastruktury back-end, používání userId řetězec dotazu jako klíč oddílu a primární repliky oddílu.

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-backend-service|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|base-url|Nový back-end základní adresa URL služby.|Ne|neuvedeno|
|id back-end|Identifikátor back-end pro směrování.|Ne|neuvedeno|
|klíč oddílu SF|Platí jenom při back-end je služba Service Fabric a je určen pomocí back-end id. Používá k překladu na konkrétní oddíl z překládání adres.|Ne|neuvedeno|
|sf-replica-type|Platí jenom při back-end je služba Service Fabric a je určen pomocí back-end id. Ovládací prvky, pokud požadavek by měli přejít na primární nebo sekundární replice oddílu. |Ne|neuvedeno|
|sf-resolve-condition|Platí jenom při back-end je služba Service Fabric. Podmínka vyhodnocena jako identifikace, pokud volání back-end Service Fabric se musí opakovat s nové řešení.|Ne|neuvedeno|
|sf-service-instance-name|Platí jenom při back-end je služba Service Fabric. Umožňuje změnit instance služby za běhu. |Ne|neuvedeno|
|Název naslouchacího procesu SF|Platí jenom při back-end je služba Service Fabric a je určen pomocí back-end id. Spolehlivé služby prostředků infrastruktury služby vám umožní vytvořit více naslouchací procesy ve službě. Tento atribut slouží k výběru konkrétním posluchačem, když back-end spolehlivá služba má více než jeden naslouchací proces. Pokud se tento atribut nezadá, API Management se pokusí použít naslouchací proces bez názvu. Naslouchací proces bez názvu je typické pro spolehlivé služby, které mají jenom jeden naslouchací proces. |Ne|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozí back-end

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetBody"></a> Sada textu
 Použití `set-body` zásad nastavit text zprávy pro příchozí a odchozí požadavky. Pro přístup k tělo zprávy, můžete použít `context.Request.Body` vlastnost nebo `context.Response.Body`, v závislosti na tom, jestli zásady je v části příchozí nebo odchozí.

> [!IMPORTANT]
>  Všimněte si, že ve výchozím nastavení při přístupu k zprávu textu pomocí `context.Request.Body` nebo `context.Response.Body`, původní text zprávy dojde ke ztrátě a musí být nastavena vrácením text zpět ve výrazu. Chcete-li zachovat obsah textu, nastavte `preserveContent` parametru `true` při přístupu k zprávy. Pokud `preserveContent` je nastaven na `true` a jiný text je vrácený výrazem vrácené body se používá.
>
>  Upozorňujeme následující aspekty při používání `set-body` zásad.
>
>  -   Pokud používáte `set-body` zásad vrátit nové nebo aktualizované text nemusíte nastavit `preserveContent` k `true` vzhledem k tomu, že jsou explicitně poskytuje nový obsah textu.
> -   Zachování obsahu odpovědi v příchozí kanálu nebude mít smysl, protože nepřijde žádná odpověď ještě.
> -   Zachování obsahu žádosti v odchozí kanálu nebude mít smysl, protože žádost již byl odeslán do back-end v tomto okamžiku.
> -   Pokud tato zásada je použita, pokud neexistuje žádný text zprávy, například v příchozí GET, je vyvolána výjimka.

 Další informace najdete v tématu `context.Request.Body`, `context.Response.Body`a `IMessage` v částech [kontextové proměnné](api-management-policy-expressions.md#ContextVariables) tabulky.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Příklady

#### <a name="literal-text-example"></a>Příklad textového literálu

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Příklad přístup k text jako řetězec. Všimněte si, že jsme jsou, se kterým jsme můžete později v kanálu zachování původního textu požadavku.

```xml
<set-body>
@{ 
    string inBody = context.Request.Body.As<string>(preserveContent: true); 
    if (inBody[0] =='c') { 
        inBody[0] = 'm'; 
    } 
    return inBody; 
}
</set-body>
```

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accesing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Příklad přístup k text jako JObject. Pamatujte, že vzhledem k tomu, že jsme nejsou rezervování původního textu žádosti accesing ho později v kanálu budou mít za následek výjimku.

```xml
<set-body> 
@{ 
    JObject inBody = context.Request.Body.As<JObject>(); 
    if (inBody.attribute == <tag>) { 
        inBody[0] = 'm'; 
    } 
    return inBody.ToString(); 
} 
</set-body>

```

#### <a name="filter-response-based-on-product"></a>Filtr reakci na produktu
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

### <a name="using-liquid-templates-with-set-body"></a>Pomocí sady textu kapaliny šablony
`set-body` Zásad může být nakonfigurován pro použití [kapaliny](https://shopify.github.io/liquid/basics/introduction/) ukázka jazyka transfom textu požadavku nebo odpovědi. To může být velmi efektivní, pokud je nutné zcela změna tvaru formát zprávy.

> [!IMPORTANT]
> Implementace kapaliny používány `set-body` v "režimu C#, jsou nakonfigurované zásady. To je zvlášť důležité při provádění akcí, například filtrování. Jako příklad použití Filtr kalendářních dat vyžaduje použití Pascal velká a malá písmena a C# datum formátování např:
>
> {{body.foo.startDateTime| Datum: "yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Aby bylo možné správně vytvořit vazbu textu XML pomocí kapaliny šablony, používat `set-header` zásad nastavit Content-Type buď application/xml, text/xml (nebo libovolný typ konče + xml); text JSON, musí být application/json, text/json (nebo libovolný typ konče + json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Převést na protokolu SOAP pomocí kapaliny šablony JSON
```xml
<set-body template="liquid">
    <soap:Envelope xmlns="http://tempuri.org/" xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
        <soap:Body>
            <GetOpenOrders>
                <cust>{{body.getOpenOrders.cust}}</cust>
            </GetOpenOrders>
        </soap:Body>
    </soap:Envelope>
</set-body>
```

#### <a name="tranform-json-using-a-liquid-template"></a>JSON Tranform pomocí kapaliny šablony
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|Sada textu|Kořenový element. Obsahuje základní text nebo výrazy, které vrací text.|Ano|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|šablona|Použít ke změně který nastavit tělo zásady se spustí v režimu ukázka. Aktuálně je jediná podporovaná hodnota:<br /><br />-kapaliny - tělo zásady sada bude používat modul kapaliny ukázka |Ne|kapaliny|

Pro přístup k informacím o žádosti a odpovědi, kapaliny šablony lze vázat na objekt kontextu s následujícími vlastnostmi: <br />
<pre>context.
    Request.
        Url
        Method
        OriginalMethod
        OriginalUrl
        IpAddress
        MatchedParameters
        HasBody
        ClientCertificates
        Headers

    Response.
        StatusCode
        Method
        Headers
Url.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString

OriginalUrl.
    Scheme
    Host
    Port
    Path
    Query
    QueryString
    ToUri
    ToString
</pre>



### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetHTTPheader"></a> Set – hlavička protokolu HTTP
 `set-header` Zásady přiřazuje hodnotu existující odpovědi nebo hlavička požadavku nebo přidá hlavičku odpovědi nebo žádost o nový.

 Seznam hlaviček protokolu HTTP se vloží do zprávy HTTP. Při umístění v příchozí kanálu se tato zásada nastaví hlavičky protokolu HTTP pro žádost předávány cílové službě. Při umístění v odchozí kanálu se tato zásada nastaví hlavičky protokolu HTTP pro odpověď odesílanou brány na klientovi.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad:

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```

#### <a name="forward-context-information-to-the-backend-service"></a>Předávat informace o kontextu ke službě back-end
 Tento příklad ukazuje, jak použít zásady na úrovni rozhraní API a zadejte informace o kontextu ke službě back-end. Ukázka konfiguraci a použití této zásady, najdete v části [cloudu zahrnují díl 177: rozhraní API funkce správy více s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převíjení vpřed na 10:30. Na 12:10 je ukázku volání operace v portálu pro vývojáře, kde se můžete podívat zásad v práci.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextové proměnné](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-header|Kořenový element.|Ano|
|hodnota|Určuje hodnotu záhlaví nastavit. Pro více záhlaví se stejným názvem přidat další `value` elementy.|Ano|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|existuje akce|Určuje, jaká opatření se mají provést, pokud hlavička byl již zadán. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu existující záhlaví.<br />-skip - nenahrazuje existující hodnotu hlavičky.<br />-připojit - připojí hodnotu pro existující hodnotu hlavičky.<br />-delete - odstraní hlavičku ze žádosti.<br /><br /> Pokud nastavíte hodnotu `override` uvedení více položek se stejným názvem výsledků v hlavičce je nastavena podle všech položek (které budou uvedeny vícekrát); pouze uvedené hodnoty budou nastaveny ve výsledku.|Ne|přepsání|
|jméno|Určuje název záhlaví nastavit.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní a back-end, při chybě

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetQueryStringParameter"></a> Parametr řetězce dotazu sady
 `set-query-parameter` Přidá zásad, nahradí hodnotu, nebo odstranění požadavku parametr řetězce dotazu. Slouží k předání očekávanou back-end službu, která jsou volitelné parametry dotazu nebo nikdy přítomné v žádosti.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad:

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Předávat informace o kontextu ke službě back-end
 Tento příklad ukazuje, jak použít zásady na úrovni rozhraní API a zadejte informace o kontextu ke službě back-end. Ukázka konfiguraci a použití této zásady, najdete v části [cloudu zahrnují díl 177: rozhraní API funkce správy více s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převíjení vpřed na 10:30. Na 12:10 je ukázku volání operace v portálu pro vývojáře, kde se můžete podívat zásad v práci.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextové proměnné](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-query-parameter|Kořenový element.|Ano|
|hodnota|Určuje hodnotu parametru dotazu nastavit. Pro více parametry dotazu se stejným názvem přidat další `value` elementy.|Ano|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|existuje akce|Určuje, jaká opatření se mají provést, když už je zadaný parametr dotazu. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu parametru existující.<br />-skip - nenahrazuje existující hodnota parametru dotazu.<br />-připojit - připojí hodnotu pro existující hodnota parametru dotazu.<br />-delete - Odebere parametr dotazu z požadavku.<br /><br /> Pokud nastavíte hodnotu `override` uvedení více položek se stejným názvem výsledkem parametr dotazu je nastavena podle všech položek (které budou uvedeny vícekrát); pouze uvedené hodnoty budou nastaveny ve výsledku.|Ne|přepsání|
|jméno|Určuje název parametru dotazu nastavit.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozí back-end

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="RewriteURL"></a> Přepsání adresy URL
 `rewrite-uri` Zásad převede adresu URL požadavku z jeho veřejné formuláře do formuláře očekávanou webové služby, jak je znázorněno v následujícím příkladu.

-   Veřejnou adresu URL- `http://api.example.com/storenumber/ordernumber`

-   Adresa URL požadavku – `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

 Tuto zásadu lze použít, když lidského nebo prohlížeče friendly URL by měla transformuje se na formát adresy URL očekává webovou službou. Tato zásada stačí pro použití při vystavení alternativní formátu adresy URL, například vyčištění adresy URL, RESTful adresy URL, uživatelsky přívětivý adresy URL SEO přátelské adresy URL, které jsou čistě strukturální adresy URL, které nemají obsahovat řetězec dotazu a místo toho obsahují pouze cesty prostředku (po schéma a oprávnění). To se často provádí estetické, použitelnost nebo vyhledávací web pro účely optimalizace (vyhledávací weby SEO).

> [!NOTE]
>  Pouze můžete přidat pomocí zásad parametrů řetězce dotazu. Nelze přidat další šablony cestou parametry v adrese URL přepisování.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Příklad:

```xml
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put?c=d -->
```
```xml
<!-- Assuming incoming request is /get?a=b&c=d and operation template is set to /get?a={b} -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="/put" copy-unmatched-params="false" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
<!-- Resulting URL will be /put -->
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|Přepište uri|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|šablona|Skutečná adresa URL webové služby s všech parametrů řetězce dotazu. Pokud používáte výrazy, celou hodnota musí být výraz.|Ano|neuvedeno|
|kopírování neodpovídající parametry|Určuje, zda jsou parametry dotazu v příchozím požadavku nejsou k dispozici v původní šabloně adresy URL přidány na adresu URL definice šablony znovu zápisu|Ne|true (pravda)|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** příchozí

-   **Zásady obory:** produktu, rozhraní API, operace

##  <a name="XSLTransform"></a> Transformace XML pomocí transformaci XSLT
 `Transform XML using an XSLT` Zásad použije transformaci XSL na XML v textu požadavku nebo odpovědi.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<xsl-transform>
    <parameter name="User-Agent">@(context.Request.Headers.GetValueOrDefault("User-Agent","non-specified"))</parameter>
    <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
        <xsl:output method="xml" indent="yes" />
        <xsl:param name="User-Agent" />
        <xsl:template match="* | @* | node()">
            <xsl:copy>
                <xsl:if test="self::* and not(parent::*)">
                    <xsl:attribute name="User-Agent">
                        <xsl:value-of select="$User-Agent" />
                    </xsl:attribute>
                </xsl:if>
                <xsl:apply-templates select="* | @* | node()" />
            </xsl:copy>
        </xsl:template>
    </xsl:stylesheet>
  </xsl-transform>
```

### <a name="example"></a>Příklad:

```xml
<policies>
  <inbound>
      <base />
  </inbound>
  <outbound>
      <base />
      <xsl-transform>
        <xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
            <xsl:output omit-xml-declaration="yes" method="xml" indent="yes" />
            <!-- Copy all nodes directly-->
            <xsl:template match="node()| @*|*">
                <xsl:copy>
                    <xsl:apply-templates select="@* | node()|*" />
                </xsl:copy>
            </xsl:template>
        </xsl:stylesheet>
    </xsl-transform>
  </outbound>
</policies>
```

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|transformace XSL|Kořenový element.|Ano|
|parametr|Používá k definování proměnné používané v transformaci|Ne|
|: stylesheet|Kořenový element šablony stylů. Všechny elementy a atributy definované v rámci postupujte podle standardu [specifikace XSLT](http://www.w3.org/TR/xslt)|Ano|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v tyto zásady [části](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásady:** vstupní, výstupní

-   **Zásady obory:** globální, produktu, rozhraní API, operace

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících tématech:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
