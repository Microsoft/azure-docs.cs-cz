---
title: Zásady transformace pro Azure API Management | Microsoft Docs
description: Přečtěte si o zásadách transformace, které jsou k dispozici pro použití v Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 3097f7b0b6b69dc470877d4951efbcbd3c7482b1
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078489"
---
# <a name="api-management-transformation-policies"></a>Zásady transformace ve službě API Management
V tomto tématu najdete referenční informace pro následující zásady API Management. Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](./api-management-policies.md).

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a> Zásady transformace

-   Převede [JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) – převede požadavek nebo tělo odpovědi z JSON na XML.

-   [Převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – převede text žádosti nebo odpovědi z XML na JSON.

-   [Najít a nahradit řetězec v těle](api-management-transformation-policies.md#Findandreplacestringinbody) – najde požadavek nebo podřetězec odpovědi a nahradí ho jiným podřetězcem.

-   [Adresy URL masek v](api-management-transformation-policies.md#MaskURLSContent) odkazech Content-repisujecích (Masks) v těle odpovědi tak, aby odkazovaly na ekvivalentní odkaz přes bránu.

-   [Nastavit back-end službu](api-management-transformation-policies.md#SetBackendService) – změní back-end službu pro příchozí požadavek.

-   [Nastavit tělo](api-management-transformation-policies.md#SetBody) – nastaví tělo zprávy pro příchozí a odchozí požadavky.

-   [Nastavit HLAVIČKU http](api-management-transformation-policies.md#SetHTTPheader) – přiřadí hodnotu existující odpovědi nebo hlavičce požadavku nebo přidá novou odpověď nebo hlavičku požadavku.

-   [Nastavte parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) -přidat, nahradí hodnotu nebo odstraní parametr řetězce dotazu Request.

-   [Adresa URL pro přepsání](api-management-transformation-policies.md#RewriteURL) – převede adresu URL požadavku z veřejného formuláře na formulář, který tato webová služba očekává.

-   [Transformuje XML pomocí XSLT](api-management-transformation-policies.md#XSLTransform) – aplikuje transformaci XSL na XML v těle žádosti nebo odpovědi.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a> Převést JSON na XML
 `json-to-xml`Zásada převede požadavek nebo tělo odpovědi z formátu JSON na XML.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<json-to-xml apply="always | content-type-json" consider-accept-header="true | false" parse-date="true | false"/>
```

### <a name="example"></a>Příklad

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

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|JSON-to-XML|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|apply|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -Vždycky – vždy použít převod.<br />-Content-Type-JSON-Convert pouze v případě, že hlavička Content-Type odpovědi označuje přítomnost JSON.|Yes|Není k dispozici|
|vzít v úvahu – potvrdit – záhlaví|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -true – použije se převod, pokud se v hlavičce žádosti o přijetí vyžaduje XML.<br />-false – vždy použít převod.|No|true|
|Analýza – datum|Když se nastaví `false` hodnoty data, se během transformace jednoduše zkopírují.|No|true|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a> Převést XML na JSON
 `xml-to-json`Zásada převede tělo žádosti nebo odpovědi z XML na JSON. Tato zásada se dá použít k modernizovat rozhraní API na základě back-endu webových služeb, které jsou jenom v kódu XML.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>
```

### <a name="example"></a>Příklad

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

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|z kódu XML do formátu JSON|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|plnění|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -JavaScript-přívětivé – převedený JSON má formulář, který je uživatelsky přívětivý pro vývojáře v JavaScriptu.<br />-Direct – převedený kód JSON odráží původní strukturu dokumentu XML.|Yes|Není k dispozici|
|apply|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -Always-Convert Always.<br />-Content-Type-XML – převést pouze v případě, že hlavička Content-Type odpovědi označuje přítomnost XML.|Yes|Není k dispozici|
|vzít v úvahu – potvrdit – záhlaví|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> -true – použít převod, pokud se v hlavičce žádosti o přijetí vyžaduje JSON.<br />-false – vždy použít převod.|No|true|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a> Najít a nahradit řetězec v těle
 `find-and-replace`Zásada najde dílčí řetězec žádosti nebo odpovědi a nahradí ho jiným podřetězcem.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Příklad

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|najít a nahradit|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|Výsledkem|Řetězec, který se má vyhledat.|Yes|Není k dispozici|
|na|Náhradní řetězec Zadejte řetězec pro nahrazení nulové délky pro odebrání hledaného řetězce.|Yes|Není k dispozici|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a> Maskování adres URL v obsahu
 `redirect-content-urls`V těle odpovědi odkazují nové zápisy (masky) zásad tak, aby odkazovaly na ekvivalentní odkaz přes bránu. Použijte v části odchozí pro opětovné psaní odkazů na text odpovědi, aby odkazovaly na bránu. Použijte v části příchozí pro opakový efekt.

> [!NOTE]
>  Tato zásada nemění žádné hodnoty hlaviček, jako jsou `Location` hlavičky. Chcete-li změnit hodnoty hlaviček, použijte zásadu [set-Header](api-management-transformation-policies.md#SetHTTPheader) .

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<redirect-content-urls />
```

### <a name="example"></a>Příklad

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|přesměrování – obsah-adresy URL|Kořenový element.|Yes|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí

-   **Obory zásad:** všechny rozsahy

##  <a name="set-backend-service"></a><a name="SetBackendService"></a> Nastavit back-end službu
 Pomocí této `set-backend-service` zásady můžete přesměrovat příchozí požadavek na jiný back-end než ten, který je zadaný v nastavení rozhraní API pro tuto operaci. Tato zásada změní základní adresu URL služby back-end příchozího požadavku na službu zadanou v zásadě.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-backend-service base-url="base URL of the backend service" />
```

nebo

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Back-endové entity je možné spravovat prostřednictvím [rozhraní API](/rest/api/apimanagement/2019-12-01/backend) pro správu a [PowerShellu](https://www.powershellgallery.com/packages?q=apimanagement).

### <a name="example"></a>Příklad

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
V tomto příkladu nastavovaná zásada služby back-end směruje požadavky na základě hodnoty verze předané v řetězci dotazu do jiné back-endové služby, než je ta zadaná v rozhraní API.

Zpočátku je základní adresa URL služby back-end odvozená od nastavení rozhraní API. Adresa URL požadavku se ale na `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` to, kde `http://contoso.com/api/10.4/` je adresa URL back-end služby zadané v nastavení rozhraní API.

Pokud je použita [<\> ](api-management-advanced-policies.md#choose) příkaz zásady, základní adresa URL služby back-end se může změnit znovu buď na `http://contoso.com/api/8.2` nebo `http://contoso.com/api/9.1` , v závislosti na hodnotě parametru dotazu na žádost o verzi. Například pokud je hodnota `"2013-15"` konečná adresa URL požadavku `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef` , bude.

Pokud je požadována další transformace žádosti, lze použít další [zásady transformace](api-management-transformation-policies.md#TransformationPolicies) . Chcete-li například odebrat parametr dotazu verze nyní, že je požadavek směrován do specifického back-endu konkrétní verze, lze použít zásadu  [parametru řetězce dotazu set](api-management-transformation-policies.md#SetQueryStringParameter) , pomocí níž lze odebrat atribut Now redundantní verze.

### <a name="example"></a>Příklad

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
V tomto příkladu zásady směrují požadavek do back-endu Service Fabric pomocí řetězce dotazu userId jako klíče oddílu a pomocí primární repliky oddílu.

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|nastavení-back-endu – služba|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|základní-adresa URL|Nová základní adresa URL služby back-end.|`base-url` `backend-id` Musí být přítomen jeden z nebo.|Není k dispozici|
|back-end – ID|Identifikátor back-endu, na který se má směrovat (Entity back-endu se spravují přes [rozhraní API](/rest/api/apimanagement/2019-12-01/backend) a [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|`base-url` `backend-id` Musí být přítomen jeden z nebo.|Není k dispozici|
|SF-partition-klíč|Platí pouze v případě, že back-end je služba Service Fabric a je určena pomocí ' back-ID '. Používá se k překladu konkrétního oddílu ze služby překladu názvů.|No|Není k dispozici|
|SF – typ repliky|Platí pouze v případě, že back-end je služba Service Fabric a je určena pomocí ' back-ID '. Určuje, zda má požadavek přejít na primární nebo sekundární repliku oddílu. |No|Není k dispozici|
|SF – vyřešit podmínku|Platí pouze v případě, že back-end je služba Service Fabric. Podmínka, která určuje, jestli se volání Service Fabric back-endu musí opakovat s novým rozlišením.|No|Není k dispozici|
|SF-Service-instance-Name|Platí pouze v případě, že back-end je služba Service Fabric. Umožňuje změnit instance služby za běhu. |No|Není k dispozici|
|SF-naslouchacího procesu – název|Platí pouze v případě, že back-end je služba Service Fabric a je určena pomocí ' back-ID '. Service Fabric Reliable Services umožňuje vytvořit ve službě více posluchačů. Tento atribut se používá pro výběr konkrétního naslouchacího procesu, když má služba Reliable back-end více než jeden naslouchací proces. Pokud tento atribut není zadán, API Management se pokusí použít naslouchací proces bez názvu. Naslouchací proces bez názvu je typický pro Reliable Services, které mají jenom jeden naslouchací proces. |No|Není k dispozici|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, back-end

-   **Obory zásad:** všechny rozsahy

##  <a name="set-body"></a><a name="SetBody"></a> Nastavit tělo
 Pomocí `set-body` zásad nastavte text zprávy pro příchozí a odchozí požadavky. Chcete-li získat přístup k textu zprávy, můžete použít `context.Request.Body` vlastnost nebo v `context.Response.Body` závislosti na tom, zda je zásada v části příchozí nebo odchozí.

> [!IMPORTANT]
>  Všimněte si, že ve výchozím nastavení při přístupu k tělo zprávy pomocí `context.Request.Body` nebo dojde `context.Response.Body` ke ztrátě původního textu zprávy a musí se nastavit vrácením textu zpět ve výrazu. Chcete-li zachovat obsah těla, nastavte `preserveContent` parametr na hodnotu `true` při přístupu ke zprávě. Pokud `preserveContent` je nastaven na `true` a výraz vrací jiný text, je použit vrácený text.
>
>  Při používání zásad Pamatujte na následující skutečnosti `set-body` .
>
> - Pokud používáte `set-body` zásadu k vrácení nového nebo aktualizovaného textu, na který nemusíte nic nastavovat, `preserveContent` `true` protože explicitně poskytujete nový obsah těla.
>   -   Zachování obsahu odpovědi v příchozím kanálu nedává smysl, protože ještě neexistuje žádná odpověď.
>   -   Zachování obsahu požadavku v odchozím kanálu nedává smysl, protože tento požadavek již byl v tomto okamžiku odeslán do back-endu.
>   -   Pokud se tato zásada používá v případě, že není k dispozici tělo zprávy, například ve vstupním GET, je vyvolána výjimka.

 Další informace naleznete v `context.Request.Body` `context.Response.Body` částech, a `IMessage` v tabulce [kontextové proměnné](api-management-policy-expressions.md#ContextVariables) .

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Příklady

#### <a name="literal-text-example"></a>Příklad textu literálu

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Příklad přístupu k textu jako řetězce. Všimněte si, že zachováváme původní text žádosti, abyste k němu měli přístup později v kanálu.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Příklad přístupu k textu jako JObject. Vzhledem k tomu, že nespravujeme původní text žádosti a k němu přistupuje později v kanálu, dojde k výjimce.

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

#### <a name="filter-response-based-on-product"></a>Filtrovat odezvu na základě produktu
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

### <a name="using-liquid-templates-with-set-body"></a>Použití kapalinových šablon s nastaveným tělem
`set-body`Zásady je možné nakonfigurovat tak, aby k transformaci těla žádosti nebo odpovědi používaly šablonování jazyk [Liquid](https://shopify.github.io/liquid/basics/introduction/) . To může být velmi efektivní, pokud potřebujete úplně změnit tvar zprávy.

> [!IMPORTANT]
> Implementace tekutiny použité v `set-body` zásadách je nakonfigurovaná v režimu C#. To je důležité hlavně při provádění akcí, jako je například filtrování. Například použití filtru data vyžaduje použití velkých a malých písmen a formátování data jazyka C#, např.:
>
> {{tělo. foo. startDateTime | Datum: "yyyyMMddTHH: mm: ddZ"}}

> [!IMPORTANT]
> Aby bylo možné správně vytvořit vazby na tělo XML pomocí šablony kapalin, použijte `set-header` zásadu pro nastavení typu Content-Type buď na Application/XML, text/XML (nebo na jakýkoli typ končící na + XML); pro tělo JSON musí být Application/JSON, text/JSON (nebo jakýkoli typ končící na + JSON).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Převod JSON na SOAP pomocí šablony kapalin
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

#### <a name="transform-json-using-a-liquid-template"></a>Transformace JSON pomocí šablony kapalin
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|Set – tělo|Kořenový element. Obsahuje text těla nebo výrazy, které vrací tělo.|Yes|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|šablona|Slouží ke změně režimu šablonování, ve kterém se bude spouštět zásada pro tělo sady. V současné době je jediná podporovaná hodnota:<br /><br />-Liquid – zásada pro tělo sady bude používat šablonování modul Liquid. |No||

Pro přístup k informacím o žádosti a odpovědi může šablona kapalin vytvořit vazby k objektu kontextu s následujícími vlastnostmi: <br />
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
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end

-   **Obory zásad:** všechny rozsahy

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a> Nastavit hlavičku HTTP
 `set-header`Zásada přiřadí hodnotu existující odpovědi nebo hlavičce požadavku nebo přidá novou odpověď nebo hlavičku požadavku.

 Vloží do zprávy HTTP seznam hlaviček protokolu HTTP. Při umístění do příchozího kanálu tato zásada nastaví hlavičku protokolu HTTP pro požadavek předávané cílové službě. Při umístění do odchozího kanálu tato zásada nastaví hlavičku protokolu HTTP pro odpověď odeslanou klientovi brány.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Příklady

#### <a name="example---adding-header-override-existing"></a>Příklad – přidání hlavičky, přepsání existujících

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Příklad – odebrání záhlaví

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Dopředné informace o kontextu do back-endové služby
 Tento příklad ukazuje, jak použít zásady na úrovni rozhraní API k poskytnutí kontextové informace do back-endové služby. Ukázku konfigurace a používání těchto zásad najdete v tématu [cloudový krytový díl 177: další API Management funkce s využitím Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převinutí na 10:30. V 12:10 existuje ukázka volání operace na portálu pro vývojáře, kde můžete zobrazit zásady v práci.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Několik hodnot záhlaví je zřetězené do řetězce CSV, například: `headerName: value1,value2,value3`
>
> Výjimky zahrnují standardizované hlavičky, které hodnoty:
> - může obsahovat čárky ( `User-Agent` , `WWW-Authenticate` , `Proxy-Authenticate` ),
> - může obsahovat datum ( `Cookie` , `Set-Cookie` , `Warning` ),
> - obsahuje datum ( `Date` , `Expires` , `If-Modified-Since` , `If-Unmodified-Since` , `Last-Modified` , `Retry-After` ).
>
> V případě těchto výjimek nebudou více hodnot hlaviček zřetězeny do jednoho řetězce a budou předány jako samostatné hlavičky, například: `User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|Set – hlavička|Kořenový element.|Yes|
|hodnota|Určuje hodnotu hlavičky, která se má nastavit. Pro více hlaviček se stejným názvem přidejte další `value` prvky.|No|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|Existuje – akce|Určuje akci, která se má provést, když je hlavička již zadána. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override – nahradí hodnotu existujícího záhlaví.<br />-Skip – nenahradí stávající hodnotu záhlaví.<br />-Append – připojí hodnotu k existující hodnotě záhlaví.<br />-Delete – Odebere hlavičku z požadavku.<br /><br /> Pokud je nastavená na `override` zařazení více položek se stejným názvem, v záhlaví se nastaví podle všech záznamů (které se budou zobrazovat víckrát). ve výsledku se nastaví jenom uvedené hodnoty.|No|override|
|name|Určuje název záhlaví, které má být nastaveno.|Yes|Není k dispozici|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí, back-end, zapnutá chyba

-   **Obory zásad:** všechny rozsahy

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a> Nastavit parametr řetězce dotazu
 `set-query-parameter`Zásada přidá, nahradí hodnotu nebo odstraní parametr řetězce dotazu Request. Dá se použít k předání parametrů dotazu, které očekává služba back-end, která je v žádosti volitelná nebo se v ní nikdy nevyskytuje.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

#### <a name="example"></a>Příklad

```xml

<set-query-parameter name="api-key" exists-action="skip">
  <value>12345678901</value>
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Dopředné informace o kontextu do back-endové služby
 Tento příklad ukazuje, jak použít zásady na úrovni rozhraní API k poskytnutí kontextové informace do back-endové služby. Ukázku konfigurace a používání těchto zásad najdete v tématu [cloudový krytový díl 177: další API Management funkce s využitím Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) a rychlé převinutí na 10:30. V 12:10 existuje ukázka volání operace na portálu pro vývojáře, kde můžete zobrazit zásady v práci.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Další informace najdete v tématu [výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|Set – dotaz-parametr|Kořenový element.|Yes|
|hodnota|Určuje hodnotu parametru dotazu, která se má nastavit. Pro více parametrů dotazu se stejným názvem přidejte další `value` prvky.|Yes|

### <a name="properties"></a>Vlastnosti

|Název|Popis|Povinné|Výchozí|
|----------|-----------------|--------------|-------------|
|Existuje – akce|Určuje akci, která se má provést, pokud je parametr dotazu už zadaný. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> -override – nahradí hodnotu existujícího parametru.<br />-Skip – nenahradí existující hodnotu parametru dotazu.<br />-Append – připojí hodnotu k existující hodnotě parametru dotazu.<br />-Delete – Odstraní parametr dotazu z požadavku.<br /><br /> Pokud je nastavené `override` zařazení několika záznamů se stejným názvem, bude parametr dotazu nastaven podle všech záznamů (které budou uvedeny několikrát). ve výsledku budou nastaveny pouze uvedené hodnoty.|No|override|
|name|Určuje název parametru dotazu, který se má nastavit.|Yes|Není k dispozici|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, back-end

-   **Obory zásad:** všechny rozsahy

##  <a name="rewrite-url"></a><a name="RewriteURL"></a> Adresa URL pro přepsání
 `rewrite-uri`Zásada převede adresu URL požadavku z veřejného formuláře do formuláře očekávaného webovou službou, jak je znázorněno v následujícím příkladu.

- Veřejná adresa URL – `http://api.example.com/storenumber/ordernumber`

- Adresa URL žádosti – `http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Tato zásada se dá použít, když se má transformovat adresa URL v rámci prohlížeče nebo adresy URL, kterou očekává webová služba. Tato zásada se musí použít jenom v případě, že vystavuje alternativní formát adresy URL, třeba čisté adresy URL, adresy URL RESTful, uživatelsky přívětivé adresy URL nebo popisné adresy URL, které jsou čistě strukturální adresy URL, které neobsahují řetězec dotazu, a místo toho obsahují jenom cestu k prostředku (za schématem a autoritou). Tato operace se často provádí pro účely estetického, použitelnosti nebo optimalizace vyhledávače (SEO).

> [!NOTE]
>  Pomocí zásad můžete přidat jenom parametry řetězce dotazu. V adrese URL pro přepis nemůžete přidat další parametry cesty k šabloně.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<rewrite-uri template="uri template" copy-unmatched-params="true | false" />
```

### <a name="example"></a>Příklad

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

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|přepsat identifikátor URI|Kořenový element.|Yes|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Povinné|Výchozí|
|---------------|-----------------|--------------|-------------|
|šablona|Skutečná adresa URL webové služby s libovolnými parametry řetězce dotazu. Při použití výrazů musí být celá hodnota výrazem.|Yes|Není k dispozici|
|Copy – nespárované klíčové slovo|Určuje, jestli se do adresy URL definované šablonou opětovného zápisu přidají parametry dotazu v příchozím požadavku, které nejsou k dispozici v šabloně původní adresy URL.|No|true|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny rozsahy

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a> Transformace XML pomocí XSLT
 Tato `Transform XML using an XSLT` zásada aplikuje v těle žádosti nebo odpovědi na XML transformaci XSL.

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

### <a name="example"></a>Příklad

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

|Název|Popis|Povinné|
|----------|-----------------|--------------|
|transformace XSL|Kořenový element.|Yes|
|parameter|Slouží k definování proměnných použitých v transformaci.|No|
|XSL: Šablona stylů|Kořenový element šablony stylů Všechny elementy a atributy definované v rámci sledování standardní [specifikace XSLT](https://www.w3.org/TR/xslt)|Yes|

### <a name="usage"></a>Využití
 Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

-   **Oddíly zásad:** příchozí, odchozí

-   **Obory zásad:** všechny rozsahy

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](./policy-reference.md)