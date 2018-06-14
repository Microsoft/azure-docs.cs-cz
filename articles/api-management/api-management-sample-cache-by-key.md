---
title: Vlastní ukládání do mezipaměti ve službě Azure API Management
description: Zjistěte, jak položky do mezipaměti podle klíče ve službě Azure API Management
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
ms.openlocfilehash: 838850d38c9df51fabcf620831371bed401e9492
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376027"
---
# <a name="custom-caching-in-azure-api-management"></a>Vlastní ukládání do mezipaměti ve službě Azure API Management
Služba Azure API Management má integrovanou podporu pro [ukládání do mezipaměti odpovědi HTTP](api-management-howto-cache.md) pomocí adresy URL prostředku jako klíč. Klíč mohou být upravena hlaviček požadavků pomocí `vary-by` vlastnosti. To je užitečné pro ukládání do mezipaměti celé odpovědi protokolu HTTP (neboli reprezentace), ale někdy je vhodné do mezipaměti jenom část reprezentace. Nové [hodnota mezipaměti vyhledávání](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) a [hodnota úložiště mezipaměti](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) zásady poskytují možnost ukládání a načítání libovolný kusy data přímo z definice zásady. Tato možnost také přidá hodnotu dříve přináší [odeslán požadavek](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) zásad vzhledem k tomu, že teď můžete mezipaměti odpovědí z externích služeb.

## <a name="architecture"></a>Architektura
Použití služby API Management sdílené za klienta data do mezipaměti, aby jako škálování na víc jednotek pořád získat přístup ke stejné data uložit do mezipaměti. Při práci s více oblast nasazení existují však nezávislé mezipaměti v každém z oblastí. Je důležité nebude pracovat s mezipaměti jako úložiště dat, kde je jediný zdroj některé část informace. Pokud nebyla a později se rozhodli využít výhod nasazení s více oblastí, potom zákazníkům, kteří cestují mohou ztratit přístup ke tohoto data uložená v mezipaměti.

## <a name="fragment-caching"></a>Fragment ukládání do mezipaměti
Existují určité případy, kde odpovědí nevrátila obsahovat některé část dat, která je nákladná, chcete-li zjistit a ještě stále aktuální pro přiměřeně krátké doby. Jako příklad zvažte službu vytvořené letecká společnost, která obsahuje informace týkající se rezervace letu, stav letu, atd. Pokud je uživatel členem programu body airlines, by také mít informace týkající se jejich aktuální stav a nahromadění vzdálenost. Tyto informace související s uživatelem se pravděpodobně uloží do jiného systému, ale může být žádoucí chcete zahrnout do odpovědi vrátil o letu stav a rezervace. To lze provést pomocí procesu nazývaného fragment ukládání do mezipaměti. Primární reprezentace lze vrátit ze zdrojového serveru pomocí nějaký druh tokenu k označení, kde má být vložen informace související s uživatelem. 

Vezměte v úvahu následující JSON odpověď z back-end rozhraní API.

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

A sekundární prostředek na úrovni `/userprofile/{userid}` jako,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

Zjistit informace o příslušných uživatelských chcete zahrnout, API Management musí zjistit, kdo je koncový uživatel. Tento mechanismus je závislá na implementaci. Jako příklad používám `Subject` z deklarací identity `JWT` tokenu. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Rozhraní API správy úložiště `enduserid` hodnotu v kontextu proměnné pro pozdější použití. Dalším krokem je určit, pokud se na předchozí požadavek již načíst informace o uživateli a uloženy v mezipaměti. V takovém případě se používá rozhraní API Management `cache-lookup-value` zásad.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Pokud neexistuje žádná položka v mezipaměti, která odpovídá hodnotě klíče a ne `userprofile` kontextové proměnné je vytvořen. API Management kontroluje úspěchu při použití vyhledávání `choose` řízení toku zásad.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Pokud `userprofile` kontextové proměnné neexistuje, pak API Management bude nutné provést požadavek HTTP se jej načíst.

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

Používá rozhraní API Management `enduserid` konstruovat adresu URL pro prostředek profilu uživatele. Jakmile služba API Management má odpověď, vrátí základní text z odpovědi a uloží je zpět do kontextové proměnné.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Abyste se vyhnuli API Management v provádění této žádosti HTTP znovu, pokud stejný uživatel zadá další žádost, můžete uložit profil uživatele do mezipaměti.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management ukládá do mezipaměti pomocí přesně stejný klíč, který API Management se původně pokusil načíst její hodnotu. Dobu, která API Management vybere možnost uložení hodnota by měla být založena na jak často jsou informace o změny a jak odolný vůči chybám uživatelů na zastaralé informace. 

Je důležité si uvědomit, že načítání z mezipaměti je stále mimo proces, požadavku sítě a potenciálně můžete přesto přidat desítkami milisekund na žádost. Výhody pocházet při určování, že trvá déle, než který kvůli museli databáze dotazy nebo agregační informace z více back EndY informace o profilu uživatele.

Posledním krokem v procesu je aktualizace vrácený odpovědi pomocí informací o profilu uživatele.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Můžete zvolit, chcete-li použít uvozovky jako součást tokenu, aby i v případě, že nahradit neprobíhá, odpověď je stále platný kód JSON.  

Jakmile se společně sloučit všechny tyto kroky, konečný výsledek je zásadu, která vypadá jako následující.

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

Tento přístup ukládání do mezipaměti se primárně používá v weby kde HTML tvoří na straně serveru, aby je možné vykreslit jako jednu stránku. Může být také užitečná v rozhraní API, kdy klienti nemohou provádět ukládání do mezipaměti na straně klienta protokolu HTTP nebo je žádoucí nechcete uvést tuto odpovědnost na straně klienta.

Tento stejný druh ukládání do mezipaměti fragment lze také provést na webových serverech back-end pomocí ukládání do mezipaměti serveru Redis, ale pomocí služby pro správu rozhraní API pro tuto práci je užitečné, když v mezipaměti fragmenty pocházejí z různých back EndY než primární odpovědi.

## <a name="transparent-versioning"></a>Transparentní Správa verzí
Je obvyklé, více verzí jinou implementaci rozhraní API podporovaná v daném okamžiku. Například pro podporu různých prostředích (vývojářů, testovací, produkční atd.) nebo k podpoře starší verze rozhraní API pro příjemce rozhraní API pro migraci na novější verze. 

Jeden ze způsobů zpracování, místo aby vývojáři klienta Změna adresy URL z `/v1/customers` k `/v2/customers` je uložit v data profilu v klientu kterou verzi rozhraní API aktuálně chtějí používat a volání URL odpovídající back-end. Pokud chcete určit adresu URL back-end správné volání pro konkrétního klienta, je nutné dotaz některé konfigurační data. Pomocí ukládání do mezipaměti tato konfigurační data, API Management minimalizovat snížení výkonu to toto vyhledávání.

Prvním krokem je určit identifikátor slouží ke konfiguraci požadovanou verzi. V tomto příkladu se rozhodli verzi pro kód product key předplatného. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management pak nepodporuje vyhledávání v mezipaměti zobrazíte, zda již načíst verze požadované klienta.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

Potom API Management zkontroluje Pokud nezjistilo jej v mezipaměti.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Pokud se nenašli API Management, rozhraní API správy načte.

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

Rozbalte text odpovědi text z odpovědi.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Uložte ji zpět do mezipaměti pro budoucí použití.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

A nakonec Aktualizujte adresu URL back-end vyberte verzi požadované klientem služby.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Dokončení zásady je následující:

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

Povolení rozhraní API příjemci transparentně řídit, které back-end verze je přistupuje klienty, aniž by museli aktualizovat a znovu nasaďte klienty je elegantní řešení, které řeší mnoho aspekty správy verzí rozhraní API.

## <a name="tenant-isolation"></a>Izolaci klientů
U rozsáhlejších, víceklientské nasazení některé společnosti vytvořit samostatné skupiny klientů na odlišné nasazení hardwaru back-end. Tím se minimalizují počet zákazníci, kteří jsou ovlivněný problémem hardwaru na back-end. Umožňuje také nová verze softwaru nasazen ve fázích. Tato architektura back-end v ideálním případě by měl být transparentní k příjemce rozhraní API. Toho lze dosáhnout podobným způsobem transparentní Správa verzí aplikace je založena na stejné techniky manipulace s adresu URL back-end pomocí konfigurace stav pro každý klíč rozhraní API.  

Místo vrácení upřednostňované verzi rozhraní API pro každý klíč předplatného, by vrátit identifikátor, který se týká klienta do skupiny přiřazené hardwaru. Tento identifikátor slouží k vytvoření adresy URL vhodné back-end.

## <a name="summary"></a>Souhrn
Volný pomocí Azure API management mezipaměti pro ukládání libovolného typu dat umožňuje efektivní přístup k datům konfigurace, které mohou ovlivnit způsob zpracování příchozí žádosti. Je také slouží k ukládání dat fragmenty, které můžete posílení odpovědi, vrácená z rozhraní API back-end.
