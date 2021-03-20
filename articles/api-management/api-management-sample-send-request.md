---
title: Generování požadavků HTTP pomocí služby API Management
description: Naučte se používat zásady požadavků a odpovědí v API Management k volání externích služeb z rozhraní API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2f4bd040d7e5858fd561444f56dbce7b3f940d9a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92742399"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Použití externích služeb ze služby Azure API Management
Zásady, které jsou dostupné ve službě Azure API Management, můžou provádět nejrůznější práci založenou čistě na příchozím požadavku, odchozí odpovědi a základní informace o konfiguraci. Je ale možné pracovat s externími službami ze zásad API Management otevírá mnoho dalších příležitostí.

Dříve jste viděli, jak komunikovat se [službou centra událostí Azure za účelem protokolování, monitorování a analýzy](api-management-log-to-eventhub-sample.md). Tento článek popisuje zásady, které umožňují interakci s libovolnou externí službou založenou na protokolu HTTP. Tyto zásady se dají použít k aktivaci vzdálených událostí nebo k získání informací, které se používají k manipulaci s původním požadavkem a odpovědí v nějakým způsobem.

## <a name="send-one-way-request"></a>Send – One-Way – požadavek
Nejjednodušší vnější interakce je stylem požáru a zapomenutí požadavku, který umožňuje externí službě upozorňovat na určitý druh důležité události. Zásady toku řízení se `choose` dají použít k detekci libovolného druhu stavu, který vás zajímá.  Pokud je podmínka splněná, můžete vytvořit externí požadavek HTTP pomocí zásady [Odeslat a jednosměrné žádosti](./api-management-advanced-policies.md#SendOneWayRequest) . Může se jednat o požadavek na systém zasílání zpráv, jako je HipChat nebo časová rezerva nebo poštovní rozhraní API, jako je SendGrid nebo MailChimp, nebo pro kritické incidenty podpory, jako je PagerDuty. Všechny tyto systémy zasílání zpráv mají jednoduchá rozhraní API HTTP, která lze vyvolat.

### <a name="alerting-with-slack"></a>Upozorňování s časovou rezervou
Následující příklad ukazuje, jak odeslat zprávu do chatovací místnosti s časovou rezervou, pokud je stavový kód odpovědi HTTP větší nebo roven 500. Chyba rozsahu 500 indikuje problém s rozhraním API back-end, které klient rozhraní API nemůže vyřešit sami. Obvykle vyžaduje určitý druh zásahu v API Management část.  

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

Časová rezerva má fiktivní vstupní Webhooky. Při konfiguraci příchozího webového zavěšení vygeneruje časová rezerva speciální adresu URL, která umožňuje provést jednoduchou žádost POST a předat zprávu do kanálu časové rezervy. Text JSON, který vytvoříte, je založen na formátu definovaném časovou rezervou.

![Webový zavěšení časové rezervy](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Je požár a zapomenout dostatečně dobrý?
Při použití stylu požáru a zapomenutí žádosti se používají určité kompromisy. Pokud z nějakého důvodu požadavek selže, nebude chyba hlášena. V této konkrétní situaci není zaručena složitá náročnost systému generování sestav sekundárního selhání a dodatečné náklady na výkon při čekání na odpověď. V případě scénářů, kde je důležité pro kontrolu odpovědi, je lepší volbou zásada [Odeslat požadavek](./api-management-advanced-policies.md#SendRequest) .

## <a name="send-request"></a>Send-Request
Tato `send-request` zásada umožňuje použití externí služby k provádění složitých funkcí zpracování a vrácení dat do služby API Management, která se dá použít k dalšímu zpracování zásad.

### <a name="authorizing-reference-tokens"></a>Autorizace tokenů reference
Hlavní funkcí API Management je ochrana back-end prostředků. Pokud autorizační Server používaný vaším rozhraním API vytvoří [tokeny JWT](https://jwt.io/) jako součást toku OAuth2, jak [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) , pak můžete použít `validate-jwt` zásadu k ověření platnosti tokenu. Některé autorizační servery vytvoří, co se nazývá [referenční tokeny](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) , které se nedají ověřit bez zpětného volání na autorizační Server.

### <a name="standardized-introspection"></a>Standardizované introspekce
V minulosti neexistuje standardizovaný způsob ověření tokenu reference pomocí autorizačního serveru. Nedávno navržené standardní [dokumenty RFC 7662](https://tools.ietf.org/html/rfc7662) byly PUBLIKOVÁNy sdružením IETF, které definují, jak může server prostředků ověřit platnost tokenu.

### <a name="extracting-the-token"></a>Extrahuje se token.
Prvním krokem je extrakce tokenu z autorizační hlavičky. Hodnota hlavičky by měla být naformátována pomocí `Bearer` autorizačního schématu, jednoho prostoru a potom autorizačního tokenu podle [RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Existují však případy, kdy je autorizační schéma vynecháno. Aby se při analýze zohlednila hodnota, API Management rozdělí hodnotu hlavičky v prostoru a vybere poslední řetězec ze vráceného pole řetězců. To poskytuje alternativní řešení pro chybné formátování autorizačních hlaviček.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Provádění žádosti o ověření
Jakmile API Management má autorizační token, API Management může vytvořit žádost o ověření tokenu. Dokument RFC 7662 volá tento proces introspekce a vyžaduje, aby `POST` byl formulář HTML pro prostředek introspekce. Formulář HTML musí obsahovat alespoň dvojici klíč/hodnota s klíčem `token` . Tento požadavek na autorizační Server musí být také ověřený, aby bylo zajištěno, že se zlomyslní klienti nebudou moci dostat do sítě pro platné tokeny.

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

### <a name="checking-the-response"></a>Kontroluje se odpověď.
`response-variable-name`Atribut slouží k udělení přístupu k vrácené odpovědi. Název definovaný v této vlastnosti lze použít jako klíč do `context.Variables` slovníku pro přístup k `IResponse` objektu.

Z objektu Response můžete načíst tělo a RFC 7622 říká API Management, že odpověď musí být objekt JSON a musí obsahovat alespoň vlastnost s názvem `active` , která je logická hodnota. Pokud `active` je hodnota true, token se považuje za platný.

Případně, pokud autorizační Server neobsahuje pole "aktivní", aby označoval, zda je token platný, použijte nástroj, jako je například post, a určete, jaké vlastnosti jsou nastaveny v platném tokenu. Například pokud platná odpověď tokenu obsahuje vlastnost s názvem "expires_in", ověřte, zda tento název vlastnosti existuje v odpovědi autorizačního serveru tímto způsobem:

<when podmínka = "@ (((IResponse) Context. Proměnné ["tokenstate"]). Body.As <JObject> (). Property ("expires_in") = = null) ">

### <a name="reporting-failure"></a>Selhání generování sestav
Pomocí `<choose>` zásad můžete zjistit, jestli je token neplatný, a pokud ano, vrátit odpověď 401.

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

V závislosti na [specifikaci RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) , která popisuje `bearer` , jak se mají tokeny používat, API Management také vrátí `WWW-Authenticate` hlavičku s odpovědí 401. WWW-Authenticate má dát klientovi pokyn, jak vytvořit řádně autorizovaný požadavek. Vzhledem k široké škále dostupných přístupů s OAuth2 Framework je obtížné sdělit všechny potřebné informace. Naštěstí je snaha pomáhat klientům, [kteří zjistí, jak správně autorizovat požadavky na server prostředků](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

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

Toto je jenom jeden z mnoha příkladů, jak se `send-request` dá zásady použít k integraci užitečných externích služeb do procesu požadavků a odpovědí, které přecházejí přes službu API Management.

## <a name="response-composition"></a>Kompozice odpovědí
`send-request`Zásady je možné použít k vylepšení primárního požadavku na back-end systém, jak jste viděli v předchozím příkladu, nebo ho můžete použít jako kompletní náhradu za back-end volání. Pomocí této techniky můžete snadno vytvářet složené prostředky, které jsou agregované z více různých systémů.

### <a name="building-a-dashboard"></a>Vytvoření řídicího panelu
Někdy budete chtít vystavovat informace, které existují v různých systémech back-end, například pro řízení řídicího panelu. Klíčové ukazatele výkonu pocházejí ze všech různých back-endu, ale nebudete jim dávat přímý přístup, ale v případě, že by se daly načíst všechny informace v jediném požadavku, je skvělé. Některé informace o back-endu vyžadují několik analyzování a jejich navýšení. Možnost ukládání do mezipaměti tohoto složeného prostředku by byla užitečná, aby se snížilo zatížení back-endu, protože uživatelé mají k dispozici možnost kladiva klávesy F5, aby bylo možné zjistit, zda se jejich nefunkční metrika může změnit.    

### <a name="faking-the-resource"></a>Faking prostředek
Prvním krokem při sestavování prostředku řídicího panelu je konfigurace nové operace v Azure Portal. Toto je zástupná operace, která se používá ke konfiguraci zásad kompozice k sestavení dynamického prostředku.

![Operace řídicího panelu](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Vytváření žádostí
Po vytvoření operace můžete nakonfigurovat zásadu specifickou pro tuto operaci. 

![Snímek obrazovky zobrazující obrazovku oboru zásad](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Prvním krokem je extrakce parametrů dotazů z příchozího požadavku, abyste je mohli přeslat do back-endu. V tomto příkladu řídicí panel zobrazuje informace na základě časové prodlevy, a proto má `fromDate` `toDate` parametr a. Tuto zásadu můžete použít `set-variable` k extrakci informací z adresy URL požadavku.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Po zadání těchto informací můžete provést požadavky na všechny back-endové systémy. Každý požadavek vytvoří novou adresu URL s informacemi o parametrech a zavolá svůj příslušný server a uloží odpověď do kontextové proměnné.

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

Tyto požadavky jsou spouštěny v pořadí, což není ideální. 

### <a name="responding"></a>Attendee
Chcete-li vytvořit složenou odpověď, můžete použít zásady [vrácení odpovědi](./api-management-advanced-policies.md#ReturnResponse) . `set-body`Element může použít výraz k vytvoření nového `JObject` se všemi reprezentacemi součástí, které jsou vloženy jako vlastnosti.

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

Kompletní zásada vypadá následovně:

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

V konfiguraci operace zástupných znaků můžete nakonfigurovat prostředek řídicího panelu tak, aby se ukládal do mezipaměti alespoň po dobu 1 hodiny. 

## <a name="summary"></a>Souhrn
Služba Azure API Management poskytuje flexibilní zásady, které je možné selektivně použít pro přenosy HTTP, a umožňuje složení back-end služeb. Bez ohledu na to, jestli chcete bránu API vylepšit pomocí funkcí upozorňování, ověřování, možností ověřování nebo vytvářet nové složené prostředky založené na více službách back-end, se v `send-request` souvisejících zásadách otevře celosvětový svět možností.
