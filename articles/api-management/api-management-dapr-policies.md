---
title: Zásady integrace služby Azure API Management Dapr | Microsoft Docs
description: Přečtěte si o zásadách Azure API Management pro interakci s rozšířeními mikroslužeb Dapr.
author: vladvino
ms.author: vlvinogr
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 051bf4398555f318f613c66d58ec65be1d30e215
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646805"
---
# <a name="api-management-dapr-integration-policies"></a>API Management zásady integrace Dapr

V tomto tématu najdete referenční informace k zásadám Dapr Integration API Management. Dapr je přenosný modul runtime pro vytváření bezstavových a stavových aplikací založených na mikroslužbách s jakýmkoli jazykem nebo architekturou. Tímto způsobem se kodifikovaly společné vzory mikroslužeb, jako je zjišťování a volání služeb pomocí logiky opakování pro sestavení, publikování a přihlášení k odběru s minimálním doručením při doručování alespoň po doručení, nebo připojit prostředky vazby k usnadnění složení pomocí externích služeb. Podrobné informace a pokyny, jak začít s Dapr, najdete na [dapr.IO](https://dapr.io) . Informace o přidávání a konfiguraci zásad najdete v tématu [zásady v API Management](api-management-howto-policies.md).

> [!CAUTION]
> Zásady, na které se odkazuje v tomto tématu, jsou v Public Preview a podléhají [doplňkovým podmínkám použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)Preview.

> [!IMPORTANT]
> Zásady, na které se odkazuje v tomto tématu, fungují jenom v [samoobslužné verzi API Management brány](self-hosted-gateway-overview.md) s povolenou podporou Dapr.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>Povolit podporu Dapr v samoobslužné bráně

Pokud chcete zapnout podporu Dapr v samoobslužné bráně, přidejte do [šablony nasazení Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md) [poznámky Dapr](https://github.com/dapr/docs/blob/master/README.md) , které nahradí "App-Name" požadovaným názvem. Kompletní návod k nastavení a použití API Management s Dapr je k dispozici [zde](https://aka.ms/apim/dapr/walkthru).
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Zásady integrace Dapr (Distributed Application Runtime)

-  [Odeslat požadavek na službu](api-management-dapr-policies.md#invoke): používá modul runtime Dapr k vyhledání a spolehlivé komunikaci s mikroslužbou Dapr. Další informace o vyvolání služby v Dapr najdete v popisu v tomto souboru [Readme](https://github.com/dapr/docs/blob/master/README.md#service-invocation) .
-  [Odeslat zprávu do publikace nebo dílčího tématu](api-management-dapr-policies.md#pubsub): používá modul runtime Dapr k publikování zprávy do tématu publikovat/odběr. Další informace o publikování a odběru zpráv v Dapr najdete v popisu v tomto souboru [Readme](https://github.com/dapr/docs/blob/master/README.md) .
-  [Aktivační vazba pro výstup](api-management-dapr-policies.md#bind): používá modul runtime Dapr k vyvolání externího systému prostřednictvím výstupní vazby. Další informace o vazbách v Dapr najdete v popisu v tomto souboru [Readme](https://github.com/dapr/docs/blob/master/README.md) .

## <a name="send-request-to-a-service"></a><a name="invoke"></a> Odeslat žádost službě

Tato zásada nastaví cílovou adresu URL pro aktuální požadavek na `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` nahrazení parametrů šablony hodnotami zadanými v příkazu Policy.

Zásada předpokládá, že Dapr běží v kontejneru vozíku ve stejném pod jako brána. Po přijetí žádosti modul runtime Dapr provádí zjišťování služby a skutečné vyvolání, včetně možného překladu protokolů mezi HTTP a gRPC, opakovanými pokusy, distribuovaným trasováním a zpracováním chyb.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

Následující příklad ukazuje vyvolání metody s názvem "back" v mikroslužbě s názvem "echo". `set-backend-service`Zásada nastaví cílovou adresu URL na `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back` . `forward-request`Zásada odešle požadavek do modulu runtime Dapr, který ho doručí do mikroslužby.

`forward-request`Tady jsou uvedené zásady pro přehlednost. Zásada je obvykle zděděná z globálního oboru prostřednictvím `base` klíčového slova.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementy

| Element             | Popis  | Povinné |
|---------------------|--------------|----------|
| nastavení-back-endu – služba | Kořenový element | Ano      |

### <a name="attributes"></a>Atributy

| Atribut        | Popis                     | Povinné | Výchozí |
|------------------|---------------------------------|----------|---------|
| back-end – ID       | Musí být nastavené na "dapr".           | Ano      | –     |
| dapr-ID aplikace      | Název cílové mikroslužby Slouží k vytvoření parametru [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) v Dapr.| Ano | – |
| dapr – metoda      | Název metody nebo adresy URL, která se má vyvolat u cílové mikroslužby. Provede mapování na parametr [názvu metody](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) v Dapr.| Ano | – |
| dapr – obor názvů   | Název oboru názvů, ve kterém je cílová mikroslužba umístěná. Slouží k vytvoření parametru [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) v Dapr.| Ne | – |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí
- **Obory zásad:** všechny rozsahy

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Odeslat zprávu do tématu Pub/sub

Tato zásada dává pokyn API Management bráně, aby odesílala zprávu do tématu publikování a odběru Dapr. Tato zásada dosahuje požadavku HTTP POST na `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}` nahrazení parametrů šablony a přidání obsahu zadaného v příkazu zásad.

Zásada předpokládá, že modul runtime Dapr běží v kontejneru vozíku ve stejném pod jako brána. Modul runtime Dapr implementuje sémantiku pro publikování a podadresáře.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

Následující příklad ukazuje odeslání těla aktuální žádosti do [tématu](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters) "nové" v [součásti](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)"objednávky" v rámci "objednávky". Odpověď přijatá z modulu runtime Dapr je uložena v položce Dapr-Response v kolekci Variables v objektu [Context](api-management-policy-expressions.md#ContextVariables) .

Pokud modul runtime Dapr nemůže najít cílové téma, například a odpoví na chybu, aktivuje se oddíl "On-Error". Odpověď přijatá z modulu runtime Dapr se vrátí volajícímu. V opačném případě `200 OK` se vrátí výchozí odpověď.

Oddíl "back-end" je prázdný a žádost není předána do back-endu.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementy

| Element             | Popis  | Povinné |
|---------------------|--------------|----------|
| publikování na dapr     | Kořenový element | Ano      |

### <a name="attributes"></a>Atributy

| Atribut        | Popis                     | Povinné | Výchozí |
|------------------|---------------------------------|----------|---------|
| pubsub – název      | Název cílové součásti PubSub Provede mapování na parametr [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) v Dapr. Pokud není k dispozici, hodnota atributu __tématu__ musí být ve formátu `pubsub-name/topic-name` .    | Ne       | Žádné    |
| téma            | Název tématu. Provede mapování na parametr [tématu](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) v Dapr.               | Ano      | –     |
| ignorovat – chyba     | Pokud se nastaví `true` pokyn, aby se při přijetí chyby [](api-management-error-handling-policies.md) z modulu runtime Dapr | Ne | `false` |
| Response – proměnná – Název | Název položky kolekce [Variables](api-management-policy-expressions.md#ContextVariables) , která se má použít pro uložení odpovědi z modulu runtime Dapr | Ne | Žádné |
| timeout | Čas (v sekundách), po který se má čekat na odpověď Dapr runtime Může být v rozsahu od 1 do 240 sekund. | Ne | 5 |
| šablona | Modul šablonování, který se má použít pro transformaci obsahu zprávy Jedinou podporovanou hodnotou je "kapalina". | Ne | Žádné |
| typ obsahu | Typ obsahu zprávy jedinou podporovanou hodnotou je "Application/JSON". | Ne | Žádné |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí, odchozí, zapnutá chyba
- **Obory zásad:** všechny rozsahy

## <a name="trigger-output-binding"></a><a name="bind"></a> Aktivační vazba pro výstup

Tato zásada instruuje API Management bránu, aby aktivovala odchozí [vazbu](https://github.com/dapr/docs/blob/master/README.md)Dapr. Tato zásada dosahuje požadavku HTTP POST na `http://localhost:3500/v1.0/bindings/{{bind-name}}` nahrazení parametru šablony a přidání obsahu zadaného v příkazu zásad.

Zásada předpokládá, že modul runtime Dapr běží v kontejneru vozíku ve stejném pod jako brána. Dapr runtime zodpovídá za vyvolání externího prostředku reprezentovaného vazbou.

### <a name="policy-statement"></a>Prohlášení o zásadách

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>Příklady

#### <a name="example"></a>Příklad

Následující příklad ukazuje, jak aktivovat odchozí vazbu s názvem "externí systémy" s názvem operace "vytvořit", metadata skládající se ze dvou položek klíč/hodnota s názvem "zdroj" a "klient-IP" a tělo přicházející z původní žádosti. Odpověď přijatá z modulu runtime Dapr je zachycena v položce "BIND-Response" kolekce Variables v objektu [Context](api-management-policy-expressions.md#ContextVariables) .

Pokud z nějakého důvodu dojde k chybě modulu runtime Dapr a odpoví chyba, je aktivována část "On-Error" a odpověď přijatá z modulu runtime Dapr se vrátí volajícímu. V opačném případě `200 OK` se vrátí výchozí odpověď.

Oddíl "back-end" je prázdný a žádost není předána do back-endu.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>Elementy

| Element             | Popis  | Povinné |
|---------------------|--------------|----------|
| Invoke-dapr-Binding | Kořenový element | Ano      |
| zprostředkovatele identity            | Vazba specifických metadat ve formě párů klíč/hodnota. Provede mapování na vlastnost [metadata](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) v Dapr. | Ne |
| data            | Obsah zprávy Provede mapování na vlastnost [data](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) v Dapr. | Ne |


### <a name="attributes"></a>Atributy

| Atribut        | Popis                     | Povinné | Výchozí |
|------------------|---------------------------------|----------|---------|
| name            | Název cílové vazby Musí odpovídat názvu vazeb [definovaných](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) v Dapr.           | Ano      | –     |
| operation       | Název cílové operace (konkrétní vazba). Provede mapování na vlastnost [Operation](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) v Dapr. | Ne | Žádné |
| ignorovat – chyba     | Pokud se nastaví `true` pokyn, aby se při přijetí chyby [](api-management-error-handling-policies.md) z modulu runtime Dapr | Ne | `false` |
| Response – proměnná – Název | Název položky kolekce [Variables](api-management-policy-expressions.md#ContextVariables) , která se má použít pro uložení odpovědi z modulu runtime Dapr | Ne | Žádné |
| timeout | Čas (v sekundách), po který se má čekat na odpověď Dapr runtime Může být v rozsahu od 1 do 240 sekund. | Ne | 5 |
| šablona | Modul šablonování, který se má použít pro transformaci obsahu zprávy Jedinou podporovanou hodnotou je "kapalina". | Ne | Žádné |
| typ obsahu | Typ obsahu zprávy jedinou podporovanou hodnotou je "Application/JSON". | Ne | Žádné |

### <a name="usage"></a>Využití

Tyto zásady se dají použít v následujících [oddílech](./api-management-howto-policies.md#sections) a [oborech](./api-management-howto-policies.md#scopes)zásad.

- **Oddíly zásad:** příchozí, odchozí, zapnutá chyba
- **Obory zásad:** všechny rozsahy
