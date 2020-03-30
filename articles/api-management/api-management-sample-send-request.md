---
title: Použití služby API Management ke generování požadavků HTTP
description: Naučte se používat zásady požadavků a odpovědí ve správě rozhraní API k volání externích služeb z vašeho rozhraní API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 1c86570850894a47f57a2d3587811411cc9a76eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190013"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Používání externích služeb ze služby Azure API Management
Zásady dostupné ve službě Azure API Management můžete provádět širokou škálu užitečné práce založené čistě na příchozí požadavek, odchozí odpověď a základní informace o konfiguraci. Možnost interakce s externími službami ze zásad správy rozhraní API však otevírá mnoho dalších příležitostí.

Dříve jste viděli, jak pracovat se [službou Azure Event Hub pro protokolování, monitorování a analýzy](api-management-log-to-eventhub-sample.md). Tento článek ukazuje zásady, které umožňují interakci s externí službou založenou na protokolu HTTP. Tyto zásady lze použít pro aktivaci vzdálených událostí nebo pro načítání informací, které se používají k manipulaci s původním požadavkem a odpovědí nějakým způsobem.

## <a name="send-one-way-request"></a>Send-one-way-request
Možná nejjednodušší vnější interakce je fire-and-forget styl požadavku, který umožňuje externí služby, které mají být oznámeny nějaké důležité události. Zásady `choose` toku řízení lze zjistit jakýkoli druh podmínky, které vás zajímají.  Pokud je podmínka splněna, můžete provést externí požadavek HTTP pomocí [zásady odesílání jednosměrně.](/azure/api-management/api-management-advanced-policies#SendOneWayRequest) To by mohlo být požadavek na systém zasílání zpráv, jako je Hipchat nebo Slack, nebo mail API jako SendGrid nebo MailChimp, nebo pro kritické incidenty podpory něco jako PagerDuty. Všechny tyto systémy zasílání zpráv mají jednoduché http api, které lze vyvolat.

### <a name="alerting-with-slack"></a>Upozorňování pomocí časovosti
Následující příklad ukazuje, jak odeslat zprávu do chatovací místnosti Slack, pokud je stavový kód odpovědi HTTP větší nebo roven 500. Chyba rozsahu 500 označuje problém s rozhraním API back-endu, který klient rozhraní API nemůže vyřešit sami. Obvykle vyžaduje nějaký druh zásahu na část API Management.  

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

Slack má pojem příchozí web háčky. Při konfiguraci příchozího webového háčku Slack generuje speciální adresu URL, která vám umožní provést jednoduchý požadavek POST a předat zprávu do kanálu Slack. Tělo JSON, které vytvoříte, je založeno na formátu definovaném Slackem.

![Webový hák Slack](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Je oheň a zapomenout dost dobrý?
Existují určité kompromisy při použití fire-and-zapomenout styl žádosti. Pokud z nějakého důvodu požadavek selže, pak selhání není hlášena. V této konkrétní situaci není odůvodněna složitost sekundárního systému hlášení selhání a dodatečné náklady na výkon čekání na odpověď. Pro scénáře, kde je nezbytné zkontrolovat odpověď, pak je lepší volbou [zásady odeslání požadavku.](/azure/api-management/api-management-advanced-policies#SendRequest)

## <a name="send-request"></a>Odeslat žádost
Zásada `send-request` umožňuje pomocí externí služby provádět složité funkce zpracování a vracet data do služby pro správu rozhraní API, kterou lze použít pro další zpracování zásad.

### <a name="authorizing-reference-tokens"></a>Autorizace referenčních tokenů
Hlavní funkcí správy rozhraní API je ochrana back-endových prostředků. Pokud autorizační server používaný vaším rozhraním API vytvoří [tokeny JWT](https://jwt.io/) jako součást svého toku OAuth2, jako to dělá [Azure Active Directory,](../active-directory/hybrid/whatis-hybrid-identity.md) můžete použít `validate-jwt` zásadu k ověření platnosti tokenu. Některé autorizační servery vytvářejí takzvané [referenční tokeny,](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) které nelze ověřit bez zpětného volání autorizačního serveru.

### <a name="standardized-introspection"></a>Standardizovaná introspekce
V minulosti neexistoval žádný standardizovaný způsob ověření referenčního tokenu pomocí autorizačního serveru. Nicméně nedávno navržený standard [RFC 7662](https://tools.ietf.org/html/rfc7662) byl publikován IETF, který definuje, jak server prostředků můžete ověřit platnost tokenu.

### <a name="extracting-the-token"></a>Extrahování tokenu
Prvním krokem je extrahovat token z hlavičky autorizace. Hodnota záhlaví by měla být `Bearer` formátována pomocí autorizačního schématu, jedné mezery a pak tokenu autorizace podle [rfc 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Bohužel existují případy, kdy je schéma autorizace vynecháno. Chcete-li účet pro tuto analýzu, API Management rozdělí hodnotu záhlaví na mezeru a vybere poslední řetězec z vráceného pole řetězců. To poskytuje řešení pro chybně formátované hlavičky autorizace.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Podání žádosti o ověření
Jakmile má správa rozhraní API autorizační token, může správa rozhraní API požádat o ověření tokenu. RFC 7662 volá tento proces introspekce a vyžaduje, abyste `POST` html formulář introspekční prostředek. Formulář HTML musí obsahovat alespoň dvojici klíč/hodnota s klíčem `token`. Tento požadavek na autorizační server musí být také ověřen, aby bylo zajištěno, že klienti se zlými úmysly nemohou přejít na vlečnou trasu pro platné tokeny.

```xml
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
```

### <a name="checking-the-response"></a>Kontrola odpovědi
Atribut `response-variable-name` se používá k poskytnutí přístupu vrácené odpovědi. Název definovaný v této vlastnosti lze použít `context.Variables` jako klíč `IResponse` do slovníku pro přístup k objektu.

Z objektu odpovědi můžete načíst tělo a RFC 7622 říká API Management, že odpověď musí být `active` objekt JSON a musí obsahovat alespoň vlastnost s názvem, která je logická hodnota. Když `active` je true pak token je považován za platný.

Případně pokud autorizační server neobsahuje pole "aktivní" k označení, zda je token platný, použijte nástroj, jako je Postman, k určení, jaké vlastnosti jsou nastaveny v platném tokenu. Pokud například platná odpověď tokenu obsahuje vlastnost nazvanou "expires_in", zkontrolujte, zda tento název vlastnosti existuje v odpovědi autorizačního serveru tímto způsobem:

<když condition="@(((IResponse)context. Proměnné["tokenstate"]). Body.As<JObject>(). Vlastnost("expires_in") == null)">

### <a name="reporting-failure"></a>Hlášení selhání
Zásadu `<choose>` můžete použít ke zjištění, zda je token neplatný, a pokud ano, vrátíte odpověď 401.

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

Podle [RFC 6750,](https://tools.ietf.org/html/rfc6750#section-3) který `bearer` popisuje, jak by měly `WWW-Authenticate` být použity tokeny, api management také vrátí záhlaví s odpovědí 401. Www-Authenticate je určen k instrukce klienta o tom, jak vytvořit správně autorizovaný požadavek. Vzhledem k široké škále přístupů, které jsou možné s rámcem OAuth2, je obtížné sdělit všechny potřebné informace. Naštěstí probíhají snahy pomoci [klientům zjistit, jak správně autorizovat požadavky na server prostředků](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Konečné řešení
Na konci získáte následující zásady:

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
              <return-response response-variable-name="existing response variable">
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

Toto je pouze jeden z `send-request` mnoha příkladů, jak lze zásadu použít k integraci užitečných externích služeb do procesu požadavků a odpovědí, které procházejí službou API Management.

## <a name="response-composition"></a>Složení odpovědi
Zásadu `send-request` lze použít k vylepšení primárního požadavku na back-endový systém, jak jste viděli v předchozím příkladu, nebo ji lze použít jako úplnou náhradu pro back-endové volání. Pomocí této techniky můžete snadno vytvořit složené prostředky, které jsou agregovány z více různých systémů.

### <a name="building-a-dashboard"></a>Vytvoření řídicího panelu
Někdy chcete mít možnost vystavit informace, které existují ve více back-endových systémech, například k řízení řídicího panelu. Hlavní směrové spojení jsou pocházet ze všech různých back-endů, ale nechcete k nim poskytovat přímý přístup a bylo by hezké, kdyby všechny informace mohly být načteny v jedné žádosti. Možná, že některé z backend informace potřebuje nějaké krájení a krájení a trochu dezinfikovat první! Možnost ukládat do mezipaměti tento složený prostředek by bylo užitečné snížit zatížení back-endu, jak víte, že uživatelé mají ve zvyku bušení klávesy F5, aby zjistili, zda jejich nedostatečně výkonné metriky se mohou změnit.    

### <a name="faking-the-resource"></a>Falšování zdroje
Prvním krokem k vytvoření prostředku řídicího panelu je konfigurace nové operace na webu Azure Portal. Toto je zástupná operace používaná ke konfiguraci zásad složení k vytvoření dynamického prostředku.

![Operace řídicího panelu](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Podání žádostí
Po vytvoření operace můžete nakonfigurovat zásady speciálně pro tuto operaci. 

![Operace řídicího panelu](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Prvním krokem je extrahovat všechny parametry dotazu z příchozí ho požadavku, takže je můžete předat do back-endu. V tomto příkladu řídicí panel zobrazuje informace založené na `fromDate` časovém `toDate` období a proto má a parametr. Zásadu `set-variable` můžete použít k extrahování informací z adresy URL požadavku.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Jakmile budete mít tyto informace, můžete požádat o všechny back-endové systémy. Každý požadavek vytvoří novou adresu URL s informacemi o parametru a zavolá příslušný server a uloží odpověď do kontextové proměnné.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Tyto požadavky spustit v pořadí, což není ideální. 

### <a name="responding"></a>Reagovat
Chcete-li vytvořit složenou odpověď, můžete použít zásadu [zpětné odpovědi.](/azure/api-management/api-management-advanced-policies#ReturnResponse) Prvek `set-body` můžete použít výraz k `JObject` vytvoření nového se všemi reprezentacemi komponent vložené jako vlastnosti.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Kompletní zásady vypadá takto:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

V konfiguraci zástupné operace můžete nakonfigurovat prostředek řídicího panelu tak, aby byl uložen do mezipaměti alespoň jednu hodinu. 

## <a name="summary"></a>Souhrn
Služba Azure API Management poskytuje flexibilní zásady, které lze selektivně použít pro provoz HTTP a umožňuje složení back-endových služeb. Ať už chcete vylepšit bránu `send-request` rozhraní API pomocí funkcí upozorňování, ověřování, možností ověřování nebo vytvářet nové složené prostředky založené na více back-endových službách, zásady a související zásady otevírají svět možností.

