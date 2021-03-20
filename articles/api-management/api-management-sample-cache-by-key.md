---
title: Vlastní ukládání do mezipaměti ve službě Azure API Management
description: Naučte se, jak ukládat položky podle klíče do služby Azure API Management. Klíč můžete upravit pomocí hlaviček požadavků.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: cf9901b4e49460dd2fb91dceaf239571058c5284
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88213314"
---
# <a name="custom-caching-in-azure-api-management"></a>Vlastní ukládání do mezipaměti ve službě Azure API Management
Služba Azure API Management obsahuje integrovanou podporu [ukládání odpovědí HTTP do mezipaměti](api-management-howto-cache.md) pomocí adresy URL prostředku jako klíče. Klíč lze upravit pomocí hlaviček požadavků pomocí `vary-by` vlastností. To je užitečné pro ukládání celých odpovědí HTTP (označovaných také jako reprezentace), ale v některých případech je vhodné jenom ukládat část reprezentace do mezipaměti. Nové zásady [cache-Lookup-Value](./api-management-caching-policies.md#GetFromCacheByKey) a [cache-Store-Value](./api-management-caching-policies.md#StoreToCacheByKey) umožňují ukládat a načítat libovolné části dat v rámci definic zásad. Tato možnost také přidá hodnotu k dříve zavedené zásadě [odeslání – požadavek](./api-management-advanced-policies.md#SendRequest) , protože teď můžete ukládat odpovědi z externích služeb do mezipaměti.

## <a name="architecture"></a>Architektura
Služba API Management používá sdílenou mezipaměť dat pro jednotlivé klienty, takže při horizontálním navýšení kapacity až na více jednotek získáte přístup ke stejným datům uloženým v mezipaměti. Při práci s nasazením ve více oblastech ale v každé z těchto oblastí existují nezávislé mezipaměti. Je důležité, abyste mezipaměť nepovažovali za úložiště dat, kde se jedná o jediný zdroj některých informací. Pokud jste pracovali a později jste se rozhodli využít výhod nasazení ve více oblastech, můžou zákazníci s uživateli, kteří cestují, přijít o přístup k těmto datům v mezipaměti.

## <a name="fragment-caching"></a>Ukládání fragmentů do mezipaměti
Existují určité případy, kdy vracené odpovědi obsahují část dat, která je náročná na zjištění a stále po rozumnou dobu. Podívejte se například na službu vytvořenou leteckou společností, která poskytuje informace o rezervacích letu, stavu letu atd. Pokud je uživatel členem programu Points Points, měl by také informace týkající se jejich aktuálního stavu a nahromaděné vzdálenosti. Tyto informace související s uživatelem můžou být uložené v jiném systému, ale může být žádoucí je zahrnout do odpovědí vrácených o stav letu a rezervacích. To lze provést pomocí procesu nazývaného ukládání fragmentu do mezipaměti. Primární reprezentace se dá vrátit ze zdrojového serveru pomocí určitého druhu tokenu, který označuje, kam se mají vkládat informace související s uživatelem. 

Vezměte v úvahu následující odpověď JSON z back-endu API.

```json
{
  "airline" : "Air Canada",
  "flightno" : "871",
  "status" : "ontime",
  "gate" : "B40",
  "terminal" : "2A",
  "userprofile" : "$userprofile$"
}  
```

A sekundární prostředek `/userprofile/{userid}` , který vypadá takto,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Chcete-li určit vhodné informace o uživateli, které mají být zahrnuty, API Management nutné určit, kdo má koncový uživatel. Tento mechanismus je závislý na implementaci. Například používám `Subject` deklaraci identity `JWT` tokenu. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

API Management ukládá `enduserid` hodnotu v kontextové proměnné pro pozdější použití. Dalším krokem je určit, jestli už předchozí požadavek načetl informace o uživateli, a uložit ho do mezipaměti. V takovém případě API Management používá `cache-lookup-value` zásady.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Pokud v mezipaměti není žádná položka, která by odpovídala hodnotě klíče, `userprofile` není vytvořena žádná kontextová proměnná. API Management kontroluje úspěšnost vyhledávání pomocí `choose` zásad toku řízení.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Pokud `userprofile` kontextová proměnná neexistuje, bude muset API Management vytvořit požadavek HTTP na jeho načtení.

```xml
<send-request
  mode="new"
  response-variable-name="userprofileresponse"
  timeout="10"
  ignore-error="true">

  <!-- Build a URL that points to the profile for the current end-user -->
  <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),
      (string)context.Variables["enduserid"]).AbsoluteUri)
  </set-url>
  <set-method>GET</set-method>
</send-request>
```

API Management používá `enduserid` k vytvoření adresy URL prostředku profilu uživatele. Jakmile API Management obdrží odpověď, vyžádá text zprávy z odpovědi a uloží ji zpátky do kontextové proměnné.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Aby se zabránilo API Management, aby se tento požadavek HTTP prováděl znovu, když stejný uživatel provede jinou žádost, můžete zadat uložení profilu uživatele do mezipaměti.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management ukládá hodnotu v mezipaměti pomocí přesně stejného klíče, který API Management původně pokusil ho načíst. Doba, po kterou API Management zvolit uložení hodnoty, by měla být založená na tom, jak často se informace mění a jak mají odolní uživatelé zastaralou informaci. 

Je důležité si uvědomit, že načítání z mezipaměti je stále mimo proces, síťový požadavek a potenciálně může do žádosti přidat desítky milisekund. Výhody dostanou při určování informací o profilu uživatele déle než v důsledku nutnosti provádět databázové dotazy nebo agregovat informace z několika back-endy.

Posledním krokem v procesu je aktualizovat vrácenou odpověď s informacemi o profilu uživatele.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Můžete zvolit, aby se uvozovky zahrnuly jako součást tokenu, takže i v případě, že k nahrazení nedojde, je odpověď stále platným kódem JSON.  

Jakmile zkombinujete všechny tyto kroky dohromady, je konečným výsledkem zásada, která vypadá jako následující.

```xml
<policies>
    <inbound>
        <!-- How you determine user identity is application dependent -->
        <set-variable
          name="enduserid"
          value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />

        <!--Look for userprofile for this user in the cache -->
        <cache-lookup-value
          key="@("userprofile-" + context.Variables["enduserid"])"
          variable-name="userprofile" />

        <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
        <choose>
            <when condition="@(!context.Variables.ContainsKey("userprofile"))">
                <!-- Make HTTP request to get user profile -->
                <send-request
                  mode="new"
                  response-variable-name="userprofileresponse"
                  timeout="10"
                  ignore-error="true">

                   <!-- Build a URL that points to the profile for the current end-user -->
                    <set-url>@(new Uri(new Uri("https://apimairlineapi.azurewebsites.net/UserProfile/"),(string)context.Variables["enduserid"]).AbsoluteUri)</set-url>
                    <set-method>GET</set-method>
                </send-request>

                <!-- Store response body in context variable -->
                <set-variable
                  name="userprofile"
                  value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />

                <!-- Store result in cache -->
                <cache-store-value
                  key="@("userprofile-" + context.Variables["enduserid"])"
                  value="@((string)context.Variables["userprofile"])"
                  duration="100000" />
            </when>
        </choose>
        <base />
    </inbound>
    <outbound>
        <!-- Update response body with user profile-->
        <find-and-replace
              from='"$userprofile$"'
              to="@((string)context.Variables["userprofile"])" />
        <base />
    </outbound>
</policies>
```

Tento přístup do mezipaměti se primárně používá na webech, kde se na straně serveru skládá HTML, takže se dá vykreslit jako jediná stránka. Může to být užitečné i v rozhraních API, kde klienti nemůžou provádět ukládání HTTP do mezipaměti na straně klienta nebo je žádoucí, aby na klientovi neumístili tuto odpovědnost.

Stejný druh ukládání fragmentů do mezipaměti můžete také provést na back-end webových serverech pomocí serveru pro ukládání do mezipaměti Redis, ale používání služby API Management k provedení této práce je užitečné v případě, že fragmenty v mezipaměti přicházejí z různých back-endu než primární odpovědi.

## <a name="transparent-versioning"></a>Transparentní Správa verzí
Je běžné, že se v jednom okamžiku podporuje více různých implementačních verzí rozhraní API. Například pro podporu různých prostředí (vývoj, testování, produkce atd.) nebo pro podporu starších verzí rozhraní API, aby uživatelé rozhraní API mohli migrovat na novější verze. 

Jeden z přístupů k tomu, aby vývojáři klientů nemuseli měnit adresy URL z `/v1/customers` na, `/v2/customers` je uložit v datech profilu uživatele, která verze rozhraní API aktuálně chce použít a zavolat příslušnou adresu URL pro back-end. Chcete-li určit správnou adresu URL back-endu pro volání konkrétního klienta, je nutné zadat dotaz na některá konfigurační data. Uložením těchto konfiguračních dat API Management může minimalizovat snížení výkonu při tomto vyhledávání.

Prvním krokem je určení identifikátoru používaného ke konfiguraci požadované verze. V tomto příkladu se rozhodnete přidružit verzi k klíči předplatného produktu. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management pak vyhledáváním v mezipaměti zjistí, zda již byla požadovaná verze klienta načtena.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Pak API Management zkontroluje, jestli se v mezipaměti nenajde.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Pokud API Management nenalezne, API Management ho načte.

```xml
<send-request
    mode="new"
    response-variable-name="clientconfiguresponse"
    timeout="10"
    ignore-error="true">
            <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
            <set-method>GET</set-method>
</send-request>
```

Extrahuje text zprávy odpovědi z odpovědi.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Uložte je zpátky do mezipaměti pro budoucí použití.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

Nakonec aktualizujte adresu URL back-endu a vyberte verzi služby, kterou klient požaduje.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Kompletní zásada je následující:

```xml
<inbound>
    <base />
    <set-variable name="clientid" value="@(context.Subscription.Key)" />
    <cache-lookup-value key="@("clientversion-" + context.Variables["clientid"])" variable-name="clientversion" />

    <!-- If API Management doesn’t find it in the cache, make a request for it and store it -->
    <choose>
        <when condition="@(!context.Variables.ContainsKey("clientversion"))">
            <send-request mode="new" response-variable-name="clientconfiguresponse" timeout="10" ignore-error="true">
                <set-url>@(new Uri(new Uri(context.Api.ServiceUrl.ToString() + "api/ClientConfig/"),(string)context.Variables["clientid"]).AbsoluteUri)</set-url>
                <set-method>GET</set-method>
            </send-request>
            <!-- Store response body in context variable -->
            <set-variable name="clientversion" value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
            <!-- Store result in cache -->
            <cache-store-value key="@("clientversion-" + context.Variables["clientid"])" value="@((string)context.Variables["clientversion"])" duration="100000" />
        </when>
    </choose>
    <set-backend-service base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
</inbound>
```

Povolením spotřebitelů rozhraní API transparentně ovládáte, ke které verzi back-endu se klienti přistupují bez nutnosti aktualizace a opětovné nasazení klientů je elegantní řešení, které řeší mnoho potíží se správou verzí rozhraní API.

## <a name="tenant-isolation"></a>Izolace tenanta
Ve větších nasazeních s více klienty vytvářejí některé společnosti samostatné skupiny klientů v různých nasazeních hardwaru back-endu. Tím se minimalizuje počet zákazníků, na které má vliv problém s hardwarem v back-endu. Také umožňuje, aby byly nové verze softwaru zahrnuty ve fázích. V ideálním případě by tato architektura back-end měla být transparentní pro uživatele rozhraní API. Toho je možné dosáhnout podobným způsobem transparentní správy verzí, protože jsou založené na stejné technice při manipulaci s adresou URL back-endu pomocí stavu konfigurace na klíč rozhraní API.  

Místo vrácení upřednostňované verze rozhraní API pro každý klíč předplatného byste vrátili identifikátor, který vztahuje tenanta k přiřazené hardwarové skupině. Tento identifikátor lze použít k vytvoření příslušné adresy URL back-endu.

## <a name="summary"></a>Souhrn
Volná mezipaměť služby Azure API Management pro ukládání jakéhokoli druhu dat umožňuje efektivní přístup k datům konfigurace, který může ovlivnit způsob zpracování příchozího požadavku. Dá se taky použít k ukládání fragmentů dat, které můžou rozšiřovat odpovědi, které se vrátí z back-endu rozhraní API.
