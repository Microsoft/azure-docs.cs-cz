---
title: Vlastní ukládání do mezipaměti ve službě Azure API Management
description: Zjistěte, jak ukládat položky do mezipaměti podle klíče ve správě rozhraní Azure API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 772bc8dd-5cda-41c4-95bf-b9f6f052bc85
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 922ab731ccd76e6a1336d61abe4b0251e358beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60780816"
---
# <a name="custom-caching-in-azure-api-management"></a>Vlastní ukládání do mezipaměti ve službě Azure API Management
Služba Azure API Management má integrovanou podporu pro [ukládání do mezipaměti odpovědi HTTP](api-management-howto-cache.md) pomocí adresy URL prostředku jako klíče. Klíč lze upravit hlavičkami požadavku `vary-by` pomocí vlastností. To je užitečné pro ukládání do mezipaměti celé odpovědi HTTP (aka reprezentace), ale někdy je užitečné pouze do mezipaměti část reprezentace. Nové [zásady vyhledávání mezipaměti](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) a [hodnoty úložiště mezipaměti](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) poskytují možnost ukládat a načítat libovolné části dat z definice zásad. Tato možnost také přidává hodnotu dříve zavedené zásady [odesílání požadavků,](/azure/api-management/api-management-advanced-policies#SendRequest) protože nyní můžete ukládat odpovědi z externích služeb do mezipaměti.

## <a name="architecture"></a>Architektura
Služba API Management používá sdílenou mezipaměť dat pro jednotlivé klienty, takže při škálování na více jednotek stále získáte přístup ke stejným datům uloženým v mezipaměti. Však při práci s nasazením s více oblastmi existují nezávislé mezipaměti v rámci každé z oblastí. Je důležité nepovažovat mezipaměť jako úložiště dat, kde je jediným zdrojem některé informace. Pokud jste tak učinili a později jste se rozhodli využít nasazení ve více oblastech, mohou zákazníci s uživateli, kteří cestují, ztratit přístup k těmto datům uloženým v mezipaměti.

## <a name="fragment-caching"></a>Ukládání fragmentů do mezipaměti
Existují určité případy, kdy odpovědi, které jsou vráceny obsahují určitou část dat, která je nákladná, a přesto zůstává čerstvá po přiměřenou dobu. Jako příklad zvažte službu vytvořenou leteckou společností, která poskytuje informace týkající se rezervací letů, stavu letu atd. Pokud je uživatel členem programu leteckých bodů, bude mít také informace týkající se jejich aktuálního stavu a nahromaděných ujetých kilometrů. Tyto informace týkající se uživatele mohou být uloženy v jiném systému, ale může být žádoucí zahrnout je do odpovědí vrácených o stavu letu a rezervacích. To lze provést pomocí procesu nazývaného ukládání fragmentů do mezipaměti. Primární reprezentace může být vrácena ze serveru původu pomocí nějakého tokenu k označení, kam mají být vloženy informace související s uživatelem. 

Zvažte následující odpověď JSON z back-endového rozhraní API.

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

A sekundární zdroj, `/userprofile/{userid}` který vypadá jako,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Chcete-li určit příslušné informace o uživateli, které mají být zahrnuty, musí správa rozhraní API určit, kdo je koncový uživatel. Tento mechanismus je závislý na implementaci. Jako příklad používám deklaraci `Subject` tokenu. `JWT` 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Správa rozhraní `enduserid` API ukládá hodnotu do kontextové proměnné pro pozdější použití. Dalším krokem je zjištění, zda předchozí požadavek již načetl informace o uživateli a uložil je do mezipaměti. Za tímto cílem `cache-lookup-value` používá zásady správa rozhraní API.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Pokud není žádná položka v mezipaměti, která odpovídá `userprofile` hodnotě klíče, pak je vytvořen žádný kontext proměnné. Správa rozhraní API kontroluje úspěšnost `choose` vyhledávání pomocí zásad toku řízení.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Pokud `userprofile` kontextová proměnná neexistuje, bude muset správa rozhraní API provést požadavek HTTP, aby ji načetla.

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

Správa rozhraní `enduserid` API používá k vytvoření adresy URL pro prostředek profilu uživatele. Jakmile api management má odpověď, vytáhne základní text z odpovědi a uloží zpět do proměnné kontextu.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Chcete-li zabránit api management z provedení tohoto požadavku HTTP znovu, když stejný uživatel provede jiný požadavek, můžete zadat pro uložení profilu uživatele v mezipaměti.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

Správa rozhraní API ukládá hodnotu do mezipaměti pomocí přesně stejného klíče, který se původně pokusil načíst pomocí služby API Management. Doba trvání, kterou se správa rozhraní API rozhodne ukládat hodnotu, by měla být založena na tom, jak často se informace mění a jak jsou uživatelé tolerantní k zastaralým informacím. 

Je důležité si uvědomit, že načítání z mezipaměti je stále mimo proces, požadavek na síť a potenciálně může stále přidat desítky milisekund k požadavku. Výhody přicházejí při určování informace o profilu uživatele trvá déle, než je z důvodu nutnosti provádět databázové dotazy nebo agregovat informace z více back-endů.

Posledním krokem v procesu je aktualizace vrácené odpovědi s informacemi o profilu uživatele.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Můžete se rozhodnout zahrnout uvozovky jako součást tokenu tak, aby i v případě, že nedojde k nahrazení, odpověď je stále platný JSON.  

Jakmile zkombinujete všechny tyto kroky dohromady, konečný výsledek je zásada, která vypadá jako následující.

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

Tento přístup ukládání do mezipaměti se používá především na webových stránkách, kde html je složen na straně serveru tak, aby mohla být vykreslena jako jedna stránka. To může být také užitečné v prostředí API, kde klienti nemohou provádět ukládání do mezipaměti HTTP na straně klienta nebo je žádoucí, aby tuto odpovědnost na klienta.

Stejný druh ukládání fragmentů do mezipaměti lze provést také na webových serverech back-endu pomocí serveru mezipaměti Redis, nicméně použití služby API Management k provedení této práce je užitečné, pokud fragmenty uložené v mezipaměti pocházejí z různých back-endů než primární Reakce.

## <a name="transparent-versioning"></a>Transparentní správa verzí
Je běžnou praxí pro více různých implementačních verzí rozhraní API, které mají být podporovány v jednom okamžiku. Například pro podporu různých prostředí (dev, test, produkční, atd.) nebo pro podporu staršíverze rozhraní API poskytnout čas pro spotřebitele rozhraní API migrovat na novější verze. 

Jeden přístup k zpracování tohoto, namísto vyžadování vývojáři `/v1/customers` `/v2/customers` klienta změnit adresy URL z na je uložit do dat profilu spotřebitele, které verze rozhraní API, které aktuálně chcete použít a volání příslušné adresy URL back-endu. Chcete-li určit správnou adresu URL back-endu pro volání pro konkrétního klienta, je nutné zadat dotaz na některá konfigurační data. Ukládáním těchto konfiguračních dat do mezipaměti může správa rozhraní API minimalizovat snížení výkonu při tomto vyhledávání.

Prvním krokem je určení identifikátoru použitého ke konfiguraci požadované verze. V tomto příkladu jsem se rozhodl přidružit verzi ke klíči předplatného produktu. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

Správa rozhraní API pak provádí vyhledávání mezipaměti, aby se zjistilo, zda již načetlpožadovanou verzi klienta.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Potom správa rozhraní API zkontroluje, zda nebyl a našel v mezipaměti.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Pokud ji správa rozhraní API nenašla, načte ji.

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

Extrahujte základní text odpovědi z odpovědi.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Uložte jej zpět do mezipaměti pro budoucí použití.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

A nakonec aktualizujte adresu URL back-end a vyberte verzi služby požadovanou klientem.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Úplné zásady jsou následující:

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

Povolení spotřebitelů rozhraní API, aby transparentně řídit, které back-endverze je přístup klientů bez nutnosti aktualizovat a znovu nasadit klienty je elegantní řešení, které řeší mnoho problémů s verzí rozhraní API.

## <a name="tenant-isolation"></a>Izolace klienta
Ve větších nasazeních s více tenanty některé společnosti vytvářejí samostatné skupiny klientů na různých nasazeních back-endového hardwaru. Tím se minimalizuje počet zákazníků, kteří jsou ovlivněny problém hardwaru na back-endu. Umožňuje také postupně zavádět nové verze softwaru. V ideálním případě by tato back-endová architektura měla být transparentní pro spotřebitele rozhraní API. Toho lze dosáhnout podobným způsobem jako transparentní správa verzí, protože je založena na stejné technice manipulace s adresou URL back-endu pomocí stavu konfigurace na klíč rozhraní API.  

Místo vrácení upřednostňované verze rozhraní API pro každý klíč předplatného byste vrátit identifikátor, který se vztahuje klienta na přiřazenou skupinu hardwaru. Tento identifikátor lze použít k vytvoření příslušné adresy URL back-endu.

## <a name="summary"></a>Souhrn
Svoboda používat mezipaměť pro správu rozhraní Azure API pro ukládání jakéhokoli druhu dat umožňuje efektivní přístup ke konfiguračním datům, která mohou ovlivnit způsob zpracování příchozího požadavku. Lze také použít k ukládání fragmentů dat, které můžete rozšířit odpovědi, vrácené z back-endu rozhraní API.
