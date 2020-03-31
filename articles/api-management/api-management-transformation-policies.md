---
title: Zásady transformace správy rozhraní Azure API | Dokumenty společnosti Microsoft
description: Přečtěte si o zásadách transformace, které jsou k dispozici pro použití ve správě rozhraní Azure API.
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
ms.openlocfilehash: 34a70a4698b69881a06cfb7a7017fa0c30647197
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047682"
---
# <a name="api-management-transformation-policies"></a>Zásady transformace ve službě API Management
Toto téma obsahuje odkaz na následující zásady správy rozhraní API. Informace o přidávání a konfiguraci zásad naleznete [v tématu Zásady ve správě rozhraní API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="transformation-policies"></a><a name="TransformationPolicies"></a>Transformační politiky

-   [Převést JSON na XML](api-management-transformation-policies.md#ConvertJSONtoXML) - Převede tělo požadavku nebo odpovědi z JSON na XML.

-   [Převést XML na JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - Převede tělo požadavku nebo odpovědi z XML na JSON.

-   [Najít a nahradit řetězec v těle](api-management-transformation-policies.md#Findandreplacestringinbody) - Najde podřetězec požadavku nebo odpovědi a nahradí jej jiným podřetězcem.

-   [Adresy URL masky v obsahu](api-management-transformation-policies.md#MaskURLSContent) – přepíše (masky) odkazy v těle odezvy tak, aby ukazovaly na ekvivalentní odkaz přes bránu.

-   [Set back-endové služby](api-management-transformation-policies.md#SetBackendService) - změní back-endovou službu pro příchozí požadavek.

-   [Set body](api-management-transformation-policies.md#SetBody) - Nastaví text zprávy pro příchozí a odchozí požadavky.

-   [Nastavit hlavičku HTTP](api-management-transformation-policies.md#SetHTTPheader) – přiřadí hodnotu existující odpovědi nebo záhlaví požadavku nebo přidá novou hlavičku odpovědi nebo požadavku.

-   [Nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) - Přidá, nahradí hodnotu nebo odstraní parametr řetězce dotazu požadavku.

-   [Přepsat adresu URL](api-management-transformation-policies.md#RewriteURL) – převede adresu URL požadavku z veřejného formuláře do formuláře očekávaného webovou službou.

-   [Transformace XML pomocí XSLT](api-management-transformation-policies.md#XSLTransform) - Použije transformaci XSL na XML v těle požadavku nebo odpovědi.

##  <a name="convert-json-to-xml"></a><a name="ConvertJSONtoXML"></a>Převést JSON na XML
 Zásada `json-to-xml` převede tělo požadavku nebo odpovědi z JSON na XML.

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

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|json-xml|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|apply|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> - vždy - vždy aplikujte konverzi.<br />- content-type-json - převést pouze v případě, že odpověď Obsah-Typ záhlaví označuje přítomnost JSON.|Ano|Není dostupné.|
|zvážit-přijmout-záhlaví|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> - true - použít převod, pokud je xml požadováno v hlavičce request Accept.<br />- false -vždy použít převod.|Ne|true|
|datum analýzy|Při nastavení `false` na datum hodnoty jsou jednoduše zkopírovány během transformace|Ne|true|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, při chybě

-   **Obory zásad:** všechny obory

##  <a name="convert-xml-to-json"></a><a name="ConvertXMLtoJSON"></a>Převést XML na JSON
 Zásada `xml-to-json` převede tělo požadavku nebo odpovědi z XML na JSON. Tuto zásadu lze použít k modernizaci rozhraní API na základě back-endových webových služeb pouze xml.

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

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|xml-to-json|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|Druhu|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> - javascript-friendly - převedený JSON má formu přátelskou k vývojářům JavaScriptu.<br />- direct - převedený JSON odráží strukturu původního dokumentu XML.|Ano|Není dostupné.|
|apply|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> - vždy - vždy převést.<br />- content-type-xml - převést pouze v případě, že hlavička Content-Type odpovědi označuje přítomnost XML.|Ano|Není dostupné.|
|zvážit-přijmout-záhlaví|Atribut musí být nastaven na jednu z následujících hodnot.<br /><br /> - true - použít převod, pokud je JSON požadováno v hlavičce request Accept.<br />- false -vždy použít převod.|Ne|true|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, při chybě

-   **Obory zásad:** všechny obory

##  <a name="find-and-replace-string-in-body"></a><a name="Findandreplacestringinbody"></a>Najít a nahradit řetězec v těle
 Zásada `find-and-replace` vyhledá podřetězec požadavku nebo odpovědi a nahradí jej jiným podřetězcem.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<find-and-replace from="what to replace" to="replacement" />
```

### <a name="example"></a>Příklad

```xml
<find-and-replace from="notebook" to="laptop" />
```

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|najít a nahradit|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|Z|Řetězec, který se má vyhledat.|Ano|Není dostupné.|
|na|Náhradní řetězec Zadejte náhradní řetězec nulové délky, který odstraní hledaný řetězec.|Ano|Není dostupné.|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

##  <a name="mask-urls-in-content"></a><a name="MaskURLSContent"></a>Adresy URL masky v obsahu
 Zásady `redirect-content-urls` přepíše (masky) odkazy v těle odpovědi tak, aby odkazovat na ekvivalentní odkaz přes bránu. Pomocí v části odchozí přepište textové odkazy odpovědí, aby přecškbolukazovaly na bránu. Použijte v příchozí části pro opačný efekt.

> [!NOTE]
>  Tato zásada nezmění žádné hodnoty `Location` záhlaví, jako jsou záhlaví. Chcete-li změnit hodnoty záhlaví, použijte [zásadu set-header.](api-management-transformation-policies.md#SetHTTPheader)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<redirect-content-urls />
```

### <a name="example"></a>Příklad

```xml
<redirect-content-urls />
```

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|přesměrování obsahu-url|Kořenový prvek.|Ano|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí

-   **Obory zásad:** všechny obory

##  <a name="set-backend-service"></a><a name="SetBackendService"></a>Nastavit back-endovou službu
 Pomocí `set-backend-service` této zásady můžete příchozí požadavek přesměrovat na jiný back-end, než který je určen v nastavení rozhraní API pro danou operaci. Tato zásada změní základní adresu URL back-endové služby příchozího požadavku na adresu zadanou v zásadě.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-backend-service base-url="base URL of the backend service" />
```

– nebo –

```xml
<set-backend-service backend-id="identifier of the backend entity specifying base URL of the backend service" />
```

> [!NOTE]
> Back-endové entity lze spravovat pomocí [rozhraní API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) pro správu a [prostředí PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).

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
V tomto příkladu sada back-end zásady služby směruje požadavky na základě hodnoty verze předané v řetězci dotazu na jinou back-endovou službu, než je zadána v rozhraní API.

Zpočátku back-endová adresa URL služby je odvozena z nastavení rozhraní API. Adresa URL `https://contoso.azure-api.net/api/partners/15?version=2013-05&subscription-key=abcdef` požadavku `http://contoso.com/api/10.4/partners/15?version=2013-05&subscription-key=abcdef` `http://contoso.com/api/10.4/` se tedy změní na místo, kde je adresa URL back-endové služby zadaná v nastavení rozhraní API.

Při [použití\> příkazu](api-management-advanced-policies.md#choose) zásad<choose může být adresa `http://contoso.com/api/8.2` URL `http://contoso.com/api/9.1`základní back-endové služby znovu změnit na nebo v závislosti na hodnotě parametru dotazu požadavku na verzi. Například pokud je `"2013-15"` hodnota konečný požadavek URL stane `http://contoso.com/api/8.2/partners/15?version=2013-05&subscription-key=abcdef`.

Pokud je požadována další transformace požadavku, lze použít jiné [zásady transformace.](api-management-transformation-policies.md#TransformationPolicies) Chcete-li například odebrat parametr dotazu verze nyní, když je požadavek směrován do back-endu specifického pro verzi, lze zásadu [parametru nastavit řetězec dotazu](api-management-transformation-policies.md#SetQueryStringParameter) použít k odebrání nyní redundantního atributu verze.

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
V tomto příkladu zásady směruje požadavek na back-end service fabric pomocí řetězce dotazu userId jako klíče oddílu a pomocí primární repliky oddílu.

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-backend-service|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|základní adresa URL|Nová adresa URL back-endové služby.|Jeden `base-url` z `backend-id` nich nebo musí být přítomen.|Není dostupné.|
|backend-id|Identifikátor back-endu, do které chcete směrovat. (Back-endové entity se spravují přes [rozhraní API](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/backend) a [PowerShell](https://www.powershellgallery.com/packages?q=apimanagement).)|Jeden `base-url` z `backend-id` nich nebo musí být přítomen.|Není dostupné.|
|sf-partition-key|Použitelné pouze v případě, že back-end je služba Service Fabric a je zadán pomocí 'backend-id'. Slouží k překladu konkrétního oddílu ze služby překladu názvů.|Ne|Není dostupné.|
|sf-replika typu|Použitelné pouze v případě, že back-end je služba Service Fabric a je zadán pomocí 'backend-id'. Ovládací prvky, pokud by měl požadavek přejít na primární nebo sekundární repliku oddílu. |Ne|Není dostupné.|
|sf-resolve-condition|Platí pouze v případě, že back-end je služba Service Fabric. Podmínka identifikující, pokud má být volání back-endu Service Fabric opakováno s novým rozlišením.|Ne|Není dostupné.|
|sf-service-instance-name|Platí pouze v případě, že back-end je služba Service Fabric. Umožňuje měnit instance služby za běhu. |Ne|Není dostupné.|
|sf-posluchač-jméno|Použitelné pouze v případě, že back-end je služba Service Fabric a je zadán pomocí 'backend-id'. Service Fabric Spolehlivé služby umožňuje vytvořit více naslouchacích procesy ve službě. Tento atribut se používá k výběru konkrétní naslouchací proces, pokud back-end spolehlivé služby má více než jeden naslouchací proces. Pokud tento atribut není zadán, správa rozhraní API se pokusí použít naslouchací proces bez názvu. Naslouchací proces bez názvu je typické pro spolehlivé služby, které mají pouze jeden naslouchací proces. |Ne|Není dostupné.|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, back-end

-   **Obory zásad:** všechny obory

##  <a name="set-body"></a><a name="SetBody"></a>Nastavit tělo
 Pomocí `set-body` zásady nastavte text zprávy pro příchozí a odchozí požadavky. Chcete-li získat přístup k `context.Request.Body` textu `context.Response.Body`zprávy, můžete použít vlastnost nebo , v závislosti na tom, zda je zásada v příchozí nebo odchozí části.

> [!IMPORTANT]
>  Všimněte si, že ve výchozím `context.Request.Body` `context.Response.Body`nastavení při přístupu k textu zprávy pomocí nebo , původní text zprávy je ztracena a musí být nastavena vrácením těla zpět ve výrazu. Chcete-li zachovat obsah `preserveContent` těla, nastavte parametr při `true` přístupu ke zprávě. Pokud `preserveContent` je `true` nastavena a jiné tělo je vrácena výrazem, vrácené tělo se používá.
>
>  Při používání `set-body` zásad mějte na vědomí následující skutečnosti.
>
> - Pokud používáte `set-body` zásadu k vrácení nového nebo aktualizovaného `preserveContent` těla, nemusíte nastavit, `true` protože explicitně dodáváte nový obsah těla.
>   -   Zachování obsahu odpovědi v příchozím kanálu nemá smysl, protože zatím neexistuje žádná odpověď.
>   -   Zachování obsahu požadavku v odchozím kanálu nedává smysl, protože požadavek již byl odeslán do back-endu v tomto okamžiku.
>   -   Pokud tato zásada se používá, když neexistuje žádné tělo zprávy, například v příchozí GET, je vyvolána výjimka.

 Další informace naleznete `context.Request.Body`v `context.Response.Body`části `IMessage` , a oddíly v [tabulce proměnných Kontext.](api-management-policy-expressions.md#ContextVariables)

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-body>new body value as text</set-body>
```

### <a name="examples"></a>Příklady

#### <a name="literal-text-example"></a>Příklad doslovného textu

```xml
<set-body>Hello world!</set-body>
```

#### <a name="example-accessing-the-body-as-a-string-note-that-we-are-preserving-the-original-request-body-so-that-we-can-access-it-later-in-the-pipeline"></a>Příklad přístupu k tělu jako řetězec. Všimněte si, že jsme zachování původní tělo požadavku, takže můžeme přistupovat později v kanálu.

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

#### <a name="example-accessing-the-body-as-a-jobject-note-that-since-we-are-not-reserving-the-original-request-body-accessing-it-later-in-the-pipeline-will-result-in-an-exception"></a>Příklad přístupu k tělu jako JObject. Všimněte si, že vzhledem k tomu, že nerezervujeme původní tělo požadavku, přístup k němu později v kanálu bude mít za následek výjimku.

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

#### <a name="filter-response-based-on-product"></a>Odpověď filtru na základě produktu
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

### <a name="using-liquid-templates-with-set-body"></a>Použití tekutých šablon se sadou těla
Zásadu `set-body` lze nakonfigurovat tak, aby [používala](https://shopify.github.io/liquid/basics/introduction/) jazyk Liquid templating k transformaci těla požadavku nebo odpovědi. To může být velmi efektivní, pokud potřebujete zcela změnit formát zprávy.

> [!IMPORTANT]
> Implementace Liquid použité v `set-body` zásadách je konfigurována v režimu C#. To je obzvláště důležité při provádění věcí, jako je filtrování. Například použití filtru data vyžaduje použití krytu Pascal a formátování data jazyka C#, např.:
>
> {{body.foo.startDateTime| Datum:"yyyyMMddTHH:mm:ddZ"}}

> [!IMPORTANT]
> Chcete-li správně vytvořit vazbu na tělo XML `set-header` pomocí šablony Liquid, použijte zásadu k nastavení typu Content-Type buď na aplikaci/xml, text/xml (nebo na libovolný typ končící na +xml); pro tělo JSON, musí být application/json, text/json (nebo jakýkoli typ končící +json).

#### <a name="convert-json-to-soap-using-a-liquid-template"></a>Převést JSON na SOAP pomocí tekuté šablony
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

#### <a name="transform-json-using-a-liquid-template"></a>Transformace JSON pomocí šablony Liquid
```xml
{
"order": {
    "id": "{{body.customer.purchase.identifier}}",
    "summary": "{{body.customer.purchase.orderShortDesc}}"
    }
}
```

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-tělo|Kořenový prvek. Obsahuje základní text nebo výrazy, které vrací text.|Ano|

### <a name="properties"></a>Vlastnosti

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|šablona|Slouží ke změně režimu šablonování, ve které bude spuštěna zásada nastavené tělo. V současné době je jedinou podporovanou hodnotou:<br /><br />- kapalina - nastavená politika karoserie bude používat kapalinový motor |Ne||

Pro přístup k informacím o požadavku a odpovědi může být šablona Liquid svázána s objektem kontextu s následujícími vlastnostmi: <br />
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
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end

-   **Obory zásad:** všechny obory

##  <a name="set-http-header"></a><a name="SetHTTPheader"></a>Nastavení hlavičky PROTOKOLU HTTP
 Zásada `set-header` přiřadí hodnotu existující odpovědi nebo záhlaví požadavku nebo přidá novou hlavičku odpovědi nebo požadavku.

 Vloží seznam hlaviček PROTOKOLU HTTP do zprávy HTTP. Při umístění do příchozího kanálu tato zásada nastaví hlavičky HTTP pro požadavek předávaný cílové službě. Při umístění do odchozího kanálu tato zásada nastaví hlavičky HTTP pro odpověď odesílanou klientovi brány.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-header name="header name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple headers with the same name add additional value elements-->
</set-header>
```

### <a name="examples"></a>Příklady

#### <a name="example---adding-header-override-existing"></a>Příklad - přidání záhlaví, přepsání existujícího

```xml
<set-header name="some header name" exists-action="override">
    <value>20</value>
</set-header>
```
#### <a name="example---removing-header"></a>Příklad - odebrání záhlaví

```xml
 <set-header name="some header name" exists-action="delete" />
```



#### <a name="forward-context-information-to-the-backend-service"></a>Předání kontextových informací back-endové službě
 Tento příklad ukazuje, jak použít zásady na úrovni rozhraní API pro poskytování kontextové informace back-endové služby. Ukázka konfigurace a používání těchto zásad najdete v [tématu Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 10:30. V 12:10 je ukázka volání operace na portálu pro vývojáře, kde můžete vidět zásady v práci.

```xml
<!-- Copy this snippet into the inbound element to forward some context information, user id and the region the gateway is hosted in, to the backend service for logging or evaluation -->
<set-header name="x-request-context-data" exists-action="override">
  <value>@(context.User.Id)</value>
  <value>@(context.Deployment.Region)</value>
</set-header>
```

 Další informace naleznete [v tématu Výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

> [!NOTE]
> Více hodnot záhlaví je zřetězeno s řetězcem CSV, například:`headerName: value1,value2,value3`
>
> Výjimky zahrnují standardizovaná záhlaví, která mají hodnoty:
> - mohou obsahovat čárky `WWW-Authenticate` `Proxy-Authenticate`(`User-Agent`, , ),
> - může obsahovat`Cookie` `Set-Cookie`datum `Warning`( , , ),
> - obsahovat`Date`datum `Expires` `If-Modified-Since`( `If-Unmodified-Since` `Last-Modified`, `Retry-After`, , . ).
>
> V případě těchto výjimek nebude více hodnot záhlaví zřetězeno do jednoho řetězce a bude předáno jako samostatná záhlaví, například:`User-Agent: value1`
>`User-Agent: value2`
>`User-Agent: value3`

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-header|Kořenový prvek.|Ano|
|value|Určuje hodnotu hlavičky, která se má nastavit. Pro více záhlaví se stejným `value` názvem přidejte další prvky.|Ne|

### <a name="properties"></a>Vlastnosti

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|existuje-akce|Určuje, jakou akci je třeba provést, když je záhlaví již zadáno. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> - přepsání - nahradí hodnotu existující hlavičky.<br />- přeskočit - nenahradí existující hodnotu záhlaví.<br />- append - připojí hodnotu k existující hodnotě záhlaví.<br />- delete - odstraní hlavičku z požadavku.<br /><br /> Pokud je `override` nastaveno zařazení více položek se stejným názvem, bude záhlaví nastaveno podle všech položek (které budou uvedeny vícekrát); ve výsledku budou nastaveny pouze uvedené hodnoty.|Ne|override|
|jméno|Určuje název hlavičky, která má být nastavena.|Ano|Není dostupné.|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí, back-end, při chybě

-   **Obory zásad:** všechny obory

##  <a name="set-query-string-parameter"></a><a name="SetQueryStringParameter"></a>Nastavit parametr řetězce dotazu
 Zásada `set-query-parameter` přidá, nahradí hodnotu nebo odstraní parametr řetězce dotazu požadavku. Lze použít k předání parametrů dotazu očekávaných back-endovou službou, které jsou volitelné nebo nikdy nejsou v požadavku k dispozici.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-query-parameter name="param name" exists-action="override | skip | append | delete">
    <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
</set-query-parameter>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

```xml

<set-query-parameter>
  <parameter name="api-key" exists-action="skip">
    <value>12345678901</value>
  </parameter>
  <!-- for multiple parameters with the same name add additional value elements -->
</set-query-parameter>

```

#### <a name="forward-context-information-to-the-backend-service"></a>Předání kontextových informací back-endové službě
 Tento příklad ukazuje, jak použít zásady na úrovni rozhraní API pro poskytování kontextové informace back-endové služby. Ukázka konfigurace a používání těchto zásad najdete v [tématu Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 10:30. V 12:10 je ukázka volání operace na portálu pro vývojáře, kde můžete vidět zásady v práci.

```xml
<!-- Copy this snippet into the inbound element to forward a piece of context, product name in this example, to the backend service for logging or evaluation -->
<set-query-parameter name="x-product-name" exists-action="override">
  <value>@(context.Product.Name)</value>
</set-query-parameter>

```

 Další informace naleznete [v tématu Výrazy zásad](api-management-policy-expressions.md) a [kontextová proměnná](api-management-policy-expressions.md#ContextVariables).

### <a name="elements"></a>Elementy

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|set-query-parametr|Kořenový prvek.|Ano|
|value|Určuje hodnotu parametru dotazu, která se má nastavit. Pro více parametrů dotazu se `value` stejným názvem přidejte další prvky.|Ano|

### <a name="properties"></a>Vlastnosti

|Name (Název)|Popis|Požaduje se|Výchozí|
|----------|-----------------|--------------|-------------|
|existuje-akce|Určuje akci, která se má provést, pokud je parametr dotazu už zadaný. Tento atribut musí mít jednu z následujících hodnot.<br /><br /> - přepsání - nahradí hodnotu stávajícího parametru.<br />- skip - nenahrazuje existující hodnotu parametru dotazu.<br />- append - připojí hodnotu k existující hodnotě parametru dotazu.<br />- delete - odebere parametr dotazu z požadavku.<br /><br /> Pokud je `override` nastaveno zařazení více položek se stejným názvem, bude parametr dotazu nastaven podle všech položek (které budou uvedeny vícekrát); ve výsledku budou nastaveny pouze uvedené hodnoty.|Ne|override|
|jméno|Určuje název parametru dotazu, který má být nastaven.|Ano|Není dostupné.|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, back-end

-   **Obory zásad:** všechny obory

##  <a name="rewrite-url"></a><a name="RewriteURL"></a>Přepsat adresu URL
 Zásada `rewrite-uri` převede adresu URL požadavku z veřejného formuláře na formulář očekávaný webovou službou, jak je znázorněno v následujícím příkladu.

- Veřejná adresa URL -`http://api.example.com/storenumber/ordernumber`

- Adresa URL požadavku -`http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State`

  Tuto zásadu lze použít v případě, že by měla být adresa URL pro člověka nebo prohlížeč transformována do formátu adresy URL očekávaného webovou službou. Tuto zásadu je třeba použít pouze při vystavení alternativního formátu adresy URL, jako jsou čisté adresy URL, adresy URL RESTful, uživatelsky přívětivé adresy URL nebo adresy URL vhodné pro SEO, které jsou čistě strukturální adresy URL, které neobsahují řetězec dotazu a místo toho obsahují pouze cestu k prostředku ( po režimu a orgánu). To se často provádí pro estetické, použitelnost, nebo optimalizace pro vyhledávače (SEO) účely.

> [!NOTE]
>  Pomocí zásady můžete přidat pouze parametry řetězce dotazu. Do adresy URL pro přepsání nelze přidat další parametry cesty šablony.

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

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|přepis-uri|Kořenový prvek.|Ano|

### <a name="attributes"></a>Atributy

|Atribut|Popis|Požaduje se|Výchozí|
|---------------|-----------------|--------------|-------------|
|šablona|Adresa URL skutečné webové služby s libovolnými parametry řetězce dotazu. Při použití výrazů musí být celá hodnota výrazem.|Ano|Není dostupné.|
|kopírování-bezkonkurenční-params|Určuje, zda jsou parametry dotazu v příchozím požadavku, který není k dispozici v původní šabloně adresy URL, přidány do adresy URL definované šablonou pro přepsání.|Ne|true|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí

-   **Obory zásad:** všechny obory

##  <a name="transform-xml-using-an-xslt"></a><a name="XSLTransform"></a>Transformace XML pomocí xslt
 Zásada `Transform XML using an XSLT` použije transformaci XSL na XML v těle požadavku nebo odpovědi.

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

|Name (Název)|Popis|Požaduje se|
|----------|-----------------|--------------|
|xsl transformace|Kořenový prvek.|Ano|
|parametr|Slouží k definování proměnných použitých v transformaci.|Ne|
|xsl:šablona stylů|Kořenový prvek šablony stylů. Všechny prvky a atributy definované v rámci standardní [specifikace XSLT](https://www.w3.org/TR/xslt)|Ano|

### <a name="usage"></a>Využití
 Tuto zásadu lze použít v následujících [částech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) zásad a [oborech](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Oddíly zásad:** příchozí, odchozí

-   **Obory zásad:** všechny obory

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících tématech:

+ [Zásady ve správě rozhraní API](api-management-howto-policies.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
