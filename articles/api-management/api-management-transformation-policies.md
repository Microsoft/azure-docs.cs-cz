---
title: Transformace zásad služby Azure API Management | Dokumentace Microsoftu
description: Další informace o zásady transformace, která je k dispozici pro použití ve službě Azure API Management.
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
ms.openlocfilehash: 87d2ebcf6a12f51775b15e906ea7813962e3630f
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52965372"
---
# <a name="api-management-transformation-policies"></a>Transformace zásady služby API Management
Toto téma obsahuje odkaz pro následující zásady služby API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady ve službě API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="TransformationPolicies"></a> Zásady transformace

-   [Převést JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) – převede požadavku nebo odpovědi zprávy z formátu JSON, který se XML.

-   [Převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – převede požadavku nebo odpovědi text ze souboru XML na JSON.

-   [Najít a nahradit řetězec v těle](api-management-transformation-policies.md#Findandreplacestringinbody) – najde dílčí řetězec požadavku nebo odpovědi a nahradí jiný dílčí řetězec.

-   [Maskovat adresy URL v obsahu](api-management-transformation-policies.md#MaskURLSContent) – přepíše (masky) odkazy v odpovědi body tak, aby ukazovaly na ekvivalentní propojení prostřednictvím brány.

-   [Nastavte Back-endová služba](api-management-transformation-policies.md#SetBackendService) – změní back-end službu pro příchozí požadavek.

-   [Nastavit text](api-management-transformation-policies.md#SetBody) – nastaví text zprávy pro příchozí a odchozí požadavky.

-   [Set – hlavička protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) – přiřadí hodnotu existující odpověď a/nebo hlavička požadavku nebo přidá hlavičku odpovědi a/nebo žádost o nový.

-   [Nastavte parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) – přidá, nahradí hodnotu nebo odstraní parametru řetězce dotazu požadavku.

-   [Přepisu adresy URL](api-management-transformation-policies.md#RewriteURL) – převede adrese URL žádosti z jeho veřejné formuláře do formuláře, byl očekáván webová služba.

-   [Transformace XML pomocí XSLT](api-management-transformation-policies.md#XSLTransform) -transformace XSL se vztahuje na XML v textu požadavku nebo odpovědi.

##  <a name="ConvertJSONtoXML"></a> Převést JSON na XML
 `json-to-xml` Zásad převádí tělo požadavku nebo odpovědi z JSON do XML.

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
|použít|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -vždy – vždy použijte převod.<br />obsah json typ - převodu pouze v případě, že hlavičku odpovědi Content-Type indikuje přítomnost JSON.|Ano|neuvedeno|
|Vezměte v úvahu přijmout – hlavička|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> Pokud je požadováno JSON v požadavku hlavičku Accept - true – použijte převod.<br />-false - vždy použít převod.|Ne|true (pravda)|
|Datum analýzy|Pokud je nastavena na `false` hodnot data zkopírují jednoduše při transformaci|Ne|true (pravda)|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, odchozí, na chyby

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="ConvertXMLtoJSON"></a> Převést XML na JSON
 `xml-to-json` Zásad převede tělo požadavku nebo odpovědi ze souboru XML na JSON. Tuto zásadu lze použít k modernizaci rozhraní API založené na back-endu jen XML webových služeb.

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
|XML na json|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|Typ|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> javascript – vhodných - JSON převedeného obsahuje formulář popisný pro vývojáře v JavaScriptu.<br />-direct - JSON převedeného strukturu původního dokumentu XML.|Ano|neuvedeno|
|použít|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -vždy - převeďte vždy.<br />convert – obsah xml typu – pouze v případě, že hlavičku odpovědi Content-Type indikuje přítomnost XML.|Ano|neuvedeno|
|Vezměte v úvahu přijmout – hlavička|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> Pokud XML je požadováno v žádosti o hlavičku Accept - true – použijte převod.<br />-false - vždy použít převod.|Ne|true (pravda)|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, odchozí, na chyby

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="Findandreplacestringinbody"></a> Najít a nahradit řetězec v textu
 `find-and-replace` Zásady najde dílčí řetězec požadavku nebo odpovědi a nahradí jiný dílčí řetězec.

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
|Najít a nahradit|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|od|Hledaný řetězec.|Ano|neuvedeno|
|na|Náhradní řetězec. Zadejte řetězec nulové délky nahrazení odebrat hledaný řetězec.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="MaskURLSContent"></a> Maska adresy URL v obsahu
 `redirect-content-urls` Zásady přepíše (masky) odkazy v těle odpovědi tak, aby ukazovaly na ekvivalentní propojení prostřednictvím brány. Použití v části outbound znovu zapsat odkazy tělo odpovědi tak, aby byly přejděte k bráně. Použijte v oddíle příchozí opačné efektu.

> [!NOTE]
>  Tato zásada nezmění žádné hodnoty hlavičky, jako `Location` záhlaví. Chcete-li změnit hodnoty hlavičky, použijte [nastavení hlaviček](api-management-transformation-policies.md#SetHTTPheader) zásad.

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
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, odchozí

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetBackendService"></a> Nastavení back-end služby
 Použití `set-backend-service` zásady pro přesměrování příchozí žádosti na jiný back-end než verze zadaná v nastavení rozhraní API pro danou operaci. Tato zásada změní back-end služba základní adresu URL příchozího požadavku je uveden v zásadách.

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
V tomto příkladu nastavení zásad služby back-endu směruje žádosti na základě verze hodnoty předané v řetězci dotazu na různé back-end službu, než je zadaný v rozhraní API.

Zpočátku back-end služba základní adresa URL je odvozen z nastavení rozhraní API. Takže adresu URL požadavku `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` stane `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` kde `http://contoso.com/api/10.4/` je adresa URL back-end služby zadaný v nastavení rozhraní API.

Když [< zvolte\> ](api-management-advanced-policies.md#choose) platí prohlášení o zásadách back-end služba základní adresu URL mohou změnit znovu buď `http://contoso.com/api/8.2` nebo `http://contoso.com/api/9.1`, v závislosti na hodnotě parametru dotazu žádosti o verzi. Například, pokud je hodnota `"2013-15"` konečné požadavku adresa URL bude `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Pokud transformace požadavku je další požadované, v jiných [zásad transformace](api-management-transformation-policies.md#TransformationPolicies) lze použít. Například odeberte parametr dotazu verze teď, když požadavek se směruje na konkrétní back-end verze [nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) zásady je možné odebrat atribut nyní redundantní verze.

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
V tomto příkladu zásady přesměruje požadavek na service fabric back-endu, pomocí řetězce dotazu ID uživatele jako klíč oddílu a pomocí primární repliky oddílu.

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-backend-service|Kořenový element.|Ano|

### <a name="attributes"></a>Atributy

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|Základní adresa url|Nový back-endu základní adresa URL služby.|Ne|neuvedeno|
|id back-endu|Identifikátor směrovat do back-endu.|Ne|neuvedeno|
|klíč oddílu SF|Platí jenom při back-end služba Service Fabric a je určen pomocí "backend-id". Použít k vyřešení konkrétního oddílu ze služby rozlišování názvů.|Ne|neuvedeno|
|Typ SF repliky|Platí jenom při back-end služba Service Fabric a je určen pomocí "backend-id". Určuje, zda požadavek by měl přejít na primární nebo sekundární repliky oddílu. |Ne|neuvedeno|
|SF vyřešit podmínku|Platí jenom při back-end služby Service Fabric. Podmínka, identifikace, pokud je volání do back-endu Service Fabric se opakuje se nové řešení.|Ne|neuvedeno|
|SF-service-instance-name|Platí jenom při back-end služby Service Fabric. Umožňuje změnit instancí služby za běhu. |Ne|neuvedeno|
|Název naslouchacího procesu SF|Platí jenom při back-end služba Service Fabric a je určen pomocí "backend-id". Service Fabric Reliable Services umožňuje vytvořit několik naslouchacích procesů ve službě. Tento atribut slouží k výběru konkrétním posluchačem, když má více než jeden naslouchací proces back-endem spolehlivé služby. Pokud tento atribut není zadán, API Management se pokusí použít naslouchací proces bez názvu. Naslouchací proces bez názvu je typický pro spolehlivé služby, které mají pouze jeden naslouchací proces. |Ne|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, back-endu

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetBody"></a> Nastavit text
 Použití `set-body` zásadu nastavit text zprávy pro příchozí a odchozí požadavky. Pro přístup k textu zprávy, můžete použít `context.Request.Body` vlastnost nebo `context.Response.Body`, v závislosti na tom, jestli zásady je v části příchozí nebo odchozí.

> [!IMPORTANT]
>  Všimněte si, že ve výchozím nastavení, když se dostanete zprávu textu pomocí `context.Request.Body` nebo `context.Response.Body`, původní text zprávy je ztracené a musí být nastavena tak, že vrací text zpět ve výrazu. Chcete-li zachovat obsah textu, nastavte `preserveContent` parametr `true` při přístupu ke zprávě. Pokud `preserveContent` je nastavena na `true` a jiný subjekt se vracenému výrazem, používají vráceného text.
>
>  Mějte prosím na paměti následující aspekty při používání `set-body` zásad.
>
>  -   Pokud používáte `set-body` zásady vrácení nové nebo aktualizované text není nutné nastavit `preserveContent` k `true` vzhledem k tomu, že jsou explicitně poskytuje nový obsah.
> -   Zachování obsahu odpovědi v příchozí kanál nemá smysl, protože nepřijde žádná odpověď ještě.
> -   Zachování obsahu požadavku na výstupní kanál nemá smysl, protože žádost již byl odeslán do back-endu v tomto okamžiku.
> -   Pokud tato zásada se používá, pokud neexistuje žádný text zprávy, třeba v příchozí GET, je vyvolána výjimka.

 Další informace najdete v tématu `context.Request.Body`, `context.Response.Body`a `IMessage` oddíly v [kontextovou proměnnou](api-management-policy-expressions.md#ContextVariables) tabulky.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Příklady

#### <a name="literal-text-example"></a>Příklad textového literálu

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Příklad přístup k text jako řetězec. Všimněte si, že jsme se tak, že jsme k němu přístup později v kanálu zachování původního textu požadavku.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accesing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Příklad přístup k textu jako JObject. Všimněte si, že přepínač, protože jsme se rezervace původní text požadavku accesing ho později v kanálu způsobí výjimku.

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

#### <a name="filter-response-based-on-product"></a>Filtrovat podle produktu odpovědi
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

### <a name="using-liquid-templates-with-set-body"></a>Použití šablon Liquid se nastavit text
`set-body` Zásad může být nakonfigurován pro použití [Liquid](https://shopify.github.io/liquid/basics/introduction/) šablon jazyka transfom tělo požadavku nebo odpovědi. To může být velmi účinné, pokud je potřeba zcela změnit tvar formát zprávy.

> [!IMPORTANT]
> Při provádění kapaliny `set-body` zásady nakonfigurované v "režimu C#". To je zvlášť důležité při věci, jako je například filtrování. Jako příklad použití Filtr kalendářních dat vyžaduje použití Pascal velká a malá písmena a C# data, například formátování:
>
> {{body.foo.startDateTime| Datum: "yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Chcete-li správně vytvoří vazbu textu XML pomocí Liquid šablony, použijte `set-header` zásady nastavení Content-Type buď application/xml, text/xml (nebo libovolný typ končí + xml); pro text JSON musí být application/json, text/json (nebo libovolný typ končí + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Převést JSON na SOAP pomocí šablony Liquid.
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

#### <a name="tranform-json-using-a-liquid-template"></a>Tranform JSON pomocí šablony Liquid.
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
|Nastavit text|Kořenový element. Obsahuje základní text nebo výrazy, které vrací text.|Ano|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|šablona|Umožňuje změnit režim šablon, které nastavení zásad textu se spustí v. Momentálně je jediná podporovaná hodnota:<br /><br />-liquid – nastavení těla zásad bude používat modul šablon liquid. |Ne|kapaliny|

Pro přístup k informacím o žádost a odpověď, Liquid šablony lze svázat objekt kontextu s následujícími vlastnostmi: <br />
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
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetHTTPheader"></a> Set – hlavička protokolu HTTP
 `set-header` Zásady přiřadí hodnotu existující odpověď a/nebo hlavička požadavku nebo přidá hlavičku odpovědi a/nebo žádost o nový.

 Vloží seznam hlaviček protokolu HTTP do zprávy HTTP. Při umístění v příchozí kanál, tyto zásady nastaví hlavičky protokolu HTTP pro žádost předávaný do cílové služby. Umístění do výstupní kanál, tyto zásady nastaví hlavičky protokolu HTTP pro odpověď odesílanou brány klienta.

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

#### <a name="forward-context-information-to-the-backend-service"></a>Předat informace o kontextu, ve službě back-endu
 Tento příklad ukazuje způsob použití zásady na úrovni rozhraní API slouží k poskytování kontextové informace ve službě back-endu. Ukázka konfigurace a používání této zásady, najdete v části [Cloud Cover epizodě 177: Další funkce API Management s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a přechod na libovolný krok 10:30. Ve 12:10 je ukázka volání operace na portálu pro vývojáře, ve kterém uvidíte zásad v práci.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextovou proměnnou](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Více hodnot hlavičky jsou zřetězeny do sdíleného svazku clusteru řetězec, například:  
> `headerName: value1,value2,value3`
>
> Výjimky patří standardizované Hlavičky, které hodnoty:
> - může obsahovat čárky (`User-Agent`, `WWW-Authenticate`, `Proxy-Authenticate`),
> - může obsahovat datum (`Cookie`, `Set-Cookie`, `Warning`),
> - obsahovat datum (`Date`, `Expires`, `If-Modified-Since`, `If-Unmodified-Since`, `Last-Modified`, `Retry-After`).
>
> V případě těchto výjimek více hodnot hlavičky nesmí být spojeny do jednoho řetězce a budou předány jako samostatné záhlaví, například:  
>`User-Agent: value1`  
>`User-Agent: value2`  
>`User-Agent: value3`

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|set – hlavička|Kořenový element.|Ano|
|hodnota|Určuje hodnotu hlavičky, která se má nastavit. Pro více záhlaví s názvem přidejte další `value` elementy.|Ano|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|existuje akce|Určuje, jaká akce se má provést, když je už zadaná hlavičce. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu existujícího hlavičky.<br />-skip - nenahrazuje existující hodnota hlavičky.<br />-Přidat – přidá hodnotu do existující hodnota hlavičky.<br />-delete - odstraní hlavičku ze žádosti.<br /><br /> Pokud je nastavena na `override` uvedení několik záznamů se stejným názvem výsledků v hlavičce nastavena podle všechny položky (které bude uveden více než jednou), nastaví se pouze uvedené hodnoty ve výsledku.|Ne|přepsání|
|jméno|Určuje název hlavičky, která se má nastavit.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** vstupní, výstupní a back-endu na chyby

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="SetQueryStringParameter"></a> Nastavení parametru řetězce dotazu
 `set-query-parameter` Přidá zásad, nahradí hodnotu, nebo odstraní požádat o parametru řetězce dotazu. Je možné předat očekává back-end službu, která jsou volitelné parametry dotazu nebo nikdy přítomné v žádosti.

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

#### <a name="forward-context-information-to-the-backend-service"></a>Předat informace o kontextu, ve službě back-endu
 Tento příklad ukazuje způsob použití zásady na úrovni rozhraní API slouží k poskytování kontextové informace ve službě back-endu. Ukázka konfigurace a používání této zásady, najdete v části [Cloud Cover epizodě 177: Další funkce API Management s Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a přechod na libovolný krok 10:30. Ve 12:10 je ukázka volání operace na portálu pro vývojáře, ve kterém uvidíte zásad v práci.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextovou proměnnou](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Název|Popis|Požaduje se|
|----------|-----------------|--------------|
|nastavení parametru dotazu|Kořenový element.|Ano|
|hodnota|Určuje hodnotu parametru dotazu, která se má nastavit. Pro více parametrů dotazu se stejným názvem, přidejte další `value` elementy.|Ano|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|existuje akce|Určuje, jaká akce se má provést, když je již zadán parametr dotazu. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override - nahradí hodnotu parametru existující.<br />-skip - nenahrazuje existující hodnota parametru dotazu.<br />-Přidat – přidá hodnotu do existující hodnota parametru dotazu.<br />-delete - Odstraní z požadavku parametr dotazu.<br /><br /> Pokud je nastavena na `override` uvedení několik záznamů se stejným názvem v parametru dotazu nastavena podle všechny položky (které bude uveden více než jednou) výsledky, nastaví se pouze uvedené hodnoty ve výsledku.|Ne|přepsání|
|jméno|Určuje název parametru dotazu, která se má nastavit.|Ano|neuvedeno|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, back-endu

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="RewriteURL"></a> Přepisování adres URL
 `rewrite-uri` Zásad převede adrese URL žádosti z jeho veřejné formuláře do formuláře, byl očekáván webová služba, jak je znázorněno v následujícím příkladu.

-   Veřejná adresa URL – `http://api.example.com/storenumber/ordernumber`

-   Adresa URL požadavku – `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

 Tuto zásadu lze použít, když lidské a/nebo prohlížeč přívětivá adresy URL by měla transformuje na formát adresy URL byl očekáván webová služba. Tyto zásady se použijí při zpřístupňování alternativní formátu adresy URL, například čisté adresy URL, rozhraní RESTful adresy URL, srozumitelné adresy URL optimalizace pro vyhledávací weby přátelské adresy URL, které jsou čistě strukturální adresy URL, které nebude obsahovat řetězec dotazu a místo toho obsahují pouze cestu k prostředku (pouze musí Po schéma a autoritu). To se často provádí aesthetic, použitelnost nebo vyhledávacího webu účelem optimalizace (vyhledávací weby SEO).

> [!NOTE]
>  Pouze můžete přidat parametry řetězce dotazu, která zásadu používá. Přepisování adres URL nelze přidat parametry cesty další šablony.

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
|šablona|Skutečná adresa URL webové služby s žádné parametry řetězce dotazu. Při použití výrazů, musí být na celočíselnou hodnotu výrazu.|Ano|neuvedeno|
|kopírování bezkonkurenční parametry|Určuje, zda jsou parametry dotazu v příchozím požadavku není k dispozici v původní šabloně URL přidána k adrese URL definované šablonou přepisu|Ne|true (pravda)|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí

-   **Zásady obory:** globální, produktu, rozhraní API, operace

##  <a name="XSLTransform"></a> Transformace XML pomocí XSLT
 `Transform XML using an XSLT` Zásady platí transformace XSL pro XML v textu požadavku nebo odpovědi.

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
|parametr|Slouží k definování proměnné, používat v transformaci|Ne|
|: stylesheet|Kořenovém prvku šablony stylů. Všechny elementy a atributy definované v rámci dodržovat standardní [specifikaci XSLT](https://www.w3.org/TR/xslt)|Ano|

### <a name="usage"></a>Využití
 Tyto zásady můžete použít v následujících zásad [oddíly](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) a [obory](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Části zásad:** příchozí, odchozí

-   **Zásady obory:** globální, produktu, rozhraní API, operace

## <a name="next-steps"></a>Další postup

Další informace najdete v následujících tématech:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
