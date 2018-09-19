---
title: Použití služby API Management k vygenerování žádosti protokolu HTTP
description: Naučte se používat zásady požadavků a odpovědí pro volání externích služeb z vašeho rozhraní API ve službě API Management
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
ms.openlocfilehash: fdcc230171006c6388e75b947e10a73fb953001a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294672"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Použití externích služeb ze služby Azure API Management
Zásady, které jsou k dispozici ve službě Azure API Management můžete provádět řadu užitečnou práci čistě na základě příchozího požadavku, odchozí odpovědi a informace o základní konfiguraci. Ale nebudou moct komunikovat s externími službami ze služby API Management otevře zásad a mnoho více příležitostí.

Jste předtím viděli, jak pracovat [služby Azure Event Hubs pro protokolování, monitorování a analýze](api-management-log-to-eventhub-sample.md). Tento článek popisuje zásady, které umožňují pracovat s libovolnou externí službu založenou na protokolu HTTP. Tyto zásady je možné spustit vzdálené události nebo pro načtení informací, který se používá k manipulaci s původní žádost a odpověď nějakým způsobem.

## <a name="send-one-way-request"></a>Send-One-Way-Request
Případně je nejjednodušší externí interakce stylu ohně a zapomenout požadavku, který umožňuje externí služby upozornit nějaký druh důležité události. Zásada řízení toku `choose` lze použít k detekci jakýkoli druh podmínku, která vás zajímají.  Pokud je podmínka splněna, můžete vytvořit externí pomocí požadavku HTTP [odeslat jeden způsob, jak žádosti](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) zásad. Může to být požadavek na systému zasílání zpráv, jako je Hipchat nebo Slack nebo e-mailu rozhraní API SendGrid nebo MailChimp, nebo pro incidenty podpory se zásadním něco jako PagerDuty. Všechny tyto systémy zasílání zpráv mají jednoduchá rozhraní API HTTP, který lze vyvolat.

### <a name="alerting-with-slack"></a>Upozorňování s využitím Slack
Následující příklad ukazuje, jak odeslat zprávu na Slack chatovací místnosti, pokud stavového kódu odpovědi HTTP je větší než nebo rovna hodnotě 500. Chyba 500 rozsah indikuje problém s back-endové rozhraní API, která klientské rozhraní API nelze vyřešit samy. Obvykle vyžaduje určitý druh zásah v části správy rozhraní API.  

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

Slack je pojem příchozí webhooků. Při konfiguraci příchozí volané webhookem Slack generuje speciální adresu URL, která vám umožní provést jednoduchý požadavek POST a předejte zprávu do kanálu Slack. Text JSON, který vytvoříte, je založen na formátu definovaném Slack.

![Slack Webhook](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Je fire spustit a zapomenout dostatečně kvalitní?
Při použití stylu ohně a zapomenout požadavku se určitých kompromisů. Pokud pro z nějakého důvodu žádost selže, pak není hlášené chyby. V této konkrétní situaci není oprávněné složitost s selhání sekundárního objektu systému a další náklady na čekání na odezvu sestavy. Pro scénáře, kdy je velmi důležité zkontrolovat odpověď, a pak bude [požadavků na odeslání](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) zásad je lepší volbou.

## <a name="send-request"></a>Požadavek na odeslání
`send-request` Zásada umožňuje pomocí externí služby a provádět komplexní zpracování funkce vrátí data do API managementu služby, který lze použít pro další zpracování zásad.

### <a name="authorizing-reference-tokens"></a>Autorizace tokeny odkazů
Hlavní funkce služby API Management chrání back-endovým prostředkům. Pokud vytvoří autorizační server používá vaše rozhraní API [tokeny JWT](http://jwt.io/) jako součást tok OAuth2, jako [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) nemá, pak můžete použít `validate-jwt` zásady k ověření platnosti tokenu. Některé autorizace servery vytvořit, co se nazývají [odkazovat na tokeny](http://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) , který nelze ověřit bez provedení zpětné volání k autorizačnímu serveru.

### <a name="standardized-introspection"></a>Standardizované introspekce
V minulosti bylo standardizované nijak ověřit token odkazu pomocí autorizačního serveru. Ale nedávno navrhovaný standard [RFC 7662](https://tools.ietf.org/html/rfc7662) zveřejnila společnost IETF, který definuje, jak můžete zdrojový server ověřit platnost tokenu.

### <a name="extracting-the-token"></a>Extrahuje token
Prvním krokem je extrahovat token z autorizační hlavičky. Hodnota záhlaví musí být naformátovaná za použití `Bearer` schéma autorizace, jedna mezera a ověřovací token, který podle [RFC 6750](http://tools.ietf.org/html/rfc6750#section-2.1). Bohužel se případech, kdy je vynechán schéma autorizace. Aby se zohlednily to při analýze, API Management rozdělí hodnotu hlavičky v prostoru a vybere poslední řetězec z vráceného pole řetězců. To poskytuje řešení pro chybně formátovaná autorizační hlavičky.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Provádění ověření požadavku
Jakmile služba API Management má autorizačním tokenem, API Management můžete provést ověření tokenu. RFC 7662 volá tento proces introspekce a vyžaduje, aby vám `POST` formuláře HTML na introspekce prostředek. Formulář HTML musí obsahovat aspoň pár klíč/hodnota s klíčem `token`. Tuto žádost k autorizačnímu serveru musí být také ověřené zajistit, že škodlivý klienty nelze přejít rybolov platné tokeny.

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
`response-variable-name` Atribut se používá k udělení přístupu vrácená odpověď. Název definovaný v této vlastnosti můžete použít jako klíč do `context.Variables` slovníku pro přístup k `IResponse` objektu.

Z odpovědi objektu můžete načíst obsah a RFC 7622 říká API Management, že odpověď musí být objektem JSON a musí obsahovat aspoň vlastnost s názvem `active` , který je logická hodnota. Když `active` má hodnotu true, pak tento token se považuje za platný.

### <a name="reporting-failure"></a>Oznamuje se chyba
Můžete použít `<choose>` zásad zjistit, jestli je token platný a pokud ano, vrátí odpověď 401.

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

Jak je uvedeno [RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) vystihuje jak `bearer` tokeny by měla sloužit, správu rozhraní API také vrátí hodnotu `WWW-Authenticate` hlavičku odpovědi 401. WWW-Authenticate určena dáte pokyn, aby klient o tom, jak vytvořit žádost o správně autorizované. Z důvodu celou řadu postupů s OAuth2 framework je obtížné komunikovat všechny potřebné informace. Naštěstí existují úsilí probíhá na pomoc [klienti zjistíte, jak správně autorizaci požadavků na server prostředků](http://tools.ietf.org/html/draft-jones-oauth-discovery-00).

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

Toto je pouze jeden z mnoha příklady jak `send-request` zásady je možné integrovat do procesů požadavků a odpovědí prostřednictvím služby API Management užitečné externích služeb.

## <a name="response-composition"></a>Složení odpovědi
`send-request` Zásadu lze použít pro zlepšení primární požadavek na back-end systém, protože jste viděli v předchozím příkladu, nebo může sloužit jako kompletní nahrazení pro volání back-endu. Tímto způsobem můžete snadno vytvořit složené prostředky, které jsou seskupeny z více různých systémů.

### <a name="building-a-dashboard"></a>Vytváření řídicího panelu
Někdy budete chtít mohli zveřejnit informace, které existují v různých systémů back-endu, třeba, k řízení řídicího panelu. Klíčové ukazatele výkonu pocházejí ze všechny různé back EndY, ale chcete raději nechcete poskytuje přímý přístup k nim a by pokud může načíst všechny informace v jednom požadavku. Například některé z informací back-end potřebuje některé segmentování a analyzování a trochu sanitaci nejprve! Možnost pro ukládání do mezipaměti složený prostředek může být užitečné ke snížení zatížení back-endu, protože víte, že uživatelé mají podporují bombarduje klávesu F5, chcete-li zobrazit, pokud jejich nedostatečně metriky může změnit.    

### <a name="faking-the-resource"></a>Faking prostředku
Prvním krokem při vytváření prostředků řídicí panel je konfigurace novou operaci na webu Azure Portal. Jedná se o operaci zástupný symbol slouží ke konfiguraci zásad složení vytvářet dynamické prostředků.

![Operace řídicí panel](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Provedení žádosti
Po operaci vytvoření, můžete nakonfigurovat zásady speciálně pro tuto operaci. 

![Operace řídicí panel](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Prvním krokem je extrahovat všechny parametry dotazu z příchozího požadavku, takže je můžete dál back-endu. V tomto příkladu řídicí panel se zobrazuje informace v závislosti na určitou dobu a proto má `fromDate` a `toDate` parametru. Můžete použít `set-variable` zásady k extrahování informací z adresy URL požadavku.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Jakmile tyto informace můžete provádět požadavky všech systémů back-endu. Každý požadavek vytvoří novou adresu URL s informace o parametrech a volá jeho příslušný server a uloží odpovědi kontextové proměnné.

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
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Tyto požadavky na spouštění v pořadí, což není ideální. 

### <a name="responding"></a>Reagovat
K vytvoření složeného odpovědi, můžete použít [vrátit odpověď](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) zásad. `set-body` Element výraz lze použít k vytvoření nového `JObject` se všechny komponenty reprezentace je vložený jako vlastnosti.

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

Dokončení zásad by měl vypadat takto:

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
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
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

V konfiguraci operace zástupného symbolu můžete nakonfigurovat řídicí panel prostředků do mezipaměti pro nejméně jednu hodinu. 

## <a name="summary"></a>Souhrn
Služba Azure API Management poskytuje flexibilní zásady, které je možné selektivně použít pro provoz protokolu HTTP a umožňuje složení back-endové služby. Určuje, zda chcete vylepšit brány rozhraní API s výstrahy funkce, ověřování, ověřování možností nebo vytvořit nový složené prostředky založené na několika službách back-endu `send-request` a odhalit tak svět možnosti související zásady.

