---
title: Vlastní ukládání do mezipaměti ve službě Azure API Management
description: Zjistěte, jak ukládat do mezipaměti položky podle klíče ve službě Azure API Management
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780816"
---
# <a name="custom-caching-in-azure-api-management"></a>Vlastní ukládání do mezipaměti ve službě Azure API Management
Služba Azure API Management obsahuje integrovanou podporu [ukládání do mezipaměti odpovědi HTTP](api-management-howto-cache.md) pomocí adresy URL prostředku jako klíč. Klíč může být upravena pomocí hlavičky žádosti `vary-by` vlastnosti. To je užitečné pro ukládání do mezipaměti celé odpovědi protokolu HTTP (označuje se také jako reprezentace), ale někdy je užitečné ukládat do mezipaměti pouze část reprezentaci. Nové [hodnota vyhledávání mezipaměti](/azure/api-management/api-management-caching-policies#GetFromCacheByKey) a [hodnota úložiště mezipaměti](/azure/api-management/api-management-caching-policies#StoreToCacheByKey) zásady poskytují možnost ukládat a načítat libovolné části dat z v rámci definic zásad. Tato možnost také přidá hodnotu do dříve uvedených [požadavků na odeslání](/azure/api-management/api-management-advanced-policies#SendRequest) zásad vzhledem k tomu, že můžete nyní ukládat do mezipaměti odpovědi z externích služeb.

## <a name="architecture"></a>Architektura
Použití služby API Management sdílené na tenanta dat do mezipaměti tak, aby škálování až na několik jednotek je stále získáte přístup ke stejné dat uložených v mezipaměti. Ale při práci s nasazení ve více oblastech jsou nezávislé mezipaměti v každé oblasti. Je důležité mezipaměti nelze považovat za úložiště dat, kde je jediný zdroj některá část informací. Pokud jste a později se rozhodli využít výhod nasazení ve více oblastech, pak zákazníkům, kteří cestují ztratit přístup ke tato data uložená v mezipaměti.

## <a name="fragment-caching"></a>Fragment ukládání do mezipaměti
Existují určité případy, ve kterém odpovědi vracené obsahovat část dat, která je nákladná k určení a ještě stále aktuální pro přiměřeně krátké doby. Jako příklad zvažte službu vytvořená leteckou, který poskytuje informace o stavu letu rezervace letenek, atd. Pokud je uživatel členem programu body airlines, bude také mít informace týkající se jejich aktuální stav a sbírají vzdálenost. Tyto informace související s uživateli mohou být uloženy v různých systémů, ale může být vhodné zahrnout do odpovědi vracené o stavu letu a rezervace. To lze provést pomocí procesu nazývaného fragment ukládání do mezipaměti. Primární reprezentaci lze vrátit ze zdrojového serveru pomocí nějaký druh tokenu k označení, ve kterém má být vložen informace související s uživateli. 

Vezměte v úvahu následující odpověď JSON z rozhraní API back-endu.

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

A sekundární prostředek na `/userprofile/{userid}` vypadá to,

```json
{ "username" : "Bob Smith", "Status" : "Gold" }
```

K určení odpovídající uživatelské informace, včetně, API Management je potřeba zjistit, kdo je koncový uživatel. Tento mechanismus je závislý na implementaci. Jako příklad, používám `Subject` z deklarací identity `JWT` token. 

```xml
<set-variable
  name="enduserid"
  value="@(context.Request.Headers.GetValueOrDefault("Authorization","").Split(' ')[1].AsJwt()?.Subject)" />
```

Rozhraní API správy úložiště `enduserid` hodnotu kontextové proměnné pro pozdější použití. Dalším krokem je určení, zda má předchozí požadavek už načíst informace o uživateli a uložená v mezipaměti. V takovém případě se používá rozhraní API Management `cache-lookup-value` zásad.

```xml
<cache-lookup-value
key="@("userprofile-" + context.Variables["enduserid"])"
variable-name="userprofile" />
```

Pokud neexistuje žádná položka v mezipaměti, která odpovídá hodnotě klíče a ne `userprofile` kontextová proměnná se vytvoří. API Management kontroluje úspěchu při použití vyhledávání `choose` řízení toku zásad.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("userprofile"))">
        <!-- If the userprofile context variable doesn’t exist, make an HTTP request to retrieve it.  -->
    </when>
</choose>
```

Pokud `userprofile` kontextová proměnná neexistuje, pak API Management se bude nutné provést požadavek HTTP se jej načíst.

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

API Management použije `enduserid` jak vytvořit adresu URL k prostředku profilu uživatele. Jakmile se služba API Management má odpověď, si vyžádá základní text z odpovědi a uloží je zpět do místní proměnné.

```xml
<set-variable
    name="userprofile"
    value="@(((IResponse)context.Variables["userprofileresponse"]).Body.As<string>())" />
```

Aby se zabránilo API Management v provádění požadavku HTTP znovu, když stejný uživatel zadá další žádost, lze vybrat profil uživatele uložit do mezipaměti.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />
```

API Management ukládá hodnotu v mezipaměti pomocí přesné, která se API Management se původně pokusila načíst pomocí stejného klíče. Doba, po kterou API Management zvolí pro ukládání hodnoty horní by měla být podle toho, jak často jsou informace o změny a jak proti chybám uživatelů na zastaralé informace. 

Je důležité si uvědomit, že načítání z mezipaměti je stále mimo proces síťový požadavek a potenciálně může přesto přidat desítky milisekund na požadavek. Výhody pocházet při určování, že trvá déle, než který kvůli nutnosti databázové dotazy nebo shromažďují informace z více back EndY informace profilu uživatele.

Posledním krokem v procesu je aktualizace vrácená odpověď pomocí informací o profilu uživatele.

```xml
<!-- Update response body with user profile-->
<find-and-replace
    from='"$userprofile$"'
    to="@((string)context.Variables["userprofile"])" />
```

Můžete zvolit, které chcete zahrnout uvozovek jako součást tokenu mechanismu tak, aby i v případě, že nedojde, nahradit, odpověď je stále platný kód JSON.  

Jakmile kombinovat všechny tyto kroky společně, konečný výsledek je zásadu, která bude vypadat jako následující.

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

Tento přístup ukládání do mezipaměti se používá především na webech kde HTML tvoří na straně serveru tak, aby ji lze vykreslit jako jednu stránku. Může být také užitečné v rozhraních API, pokud klienti nemohou provádět ukládání do mezipaměti na straně klienta protokolu HTTP nebo není žádoucí umístit zodpovědnosti na straně klienta.

Tento stejný druh fragment ukládání do mezipaměti je možné provést na back-end webových serverů pomocí ukládání do mezipaměti serveru Redis, ale pomocí služby API Management k provedení této práce je užitečný při uložené v mezipaměti fragmenty pocházejí z různých back EndY než primární odpovědi.

## <a name="transparent-versioning"></a>Transparentní správy verzí
Je běžnou praxí při více verzí rozhraní API a proto není podporován v daný okamžik jinou implementaci. Například pro podporu různých prostředích (dev, test, produkčního prostředí atd.) nebo pro podporu starších verzích rozhraní API pro rozhraní API příjemců migrovat na novější verze. 

Jeden ze způsobů zpracování, nemusíte mít klienta vývojáři, chcete-li změnit adresy URL z `/v1/customers` k `/v2/customers` je uložit ve příjemce dat profilu, která verze rozhraní API aktuálně chtějí používat a volání adresy URL příslušného back-endu. Určit adresu URL back-endu správné volání pro konkrétního klienta, je nezbytné k dotazování některé konfigurační data. Díky ukládání do mezipaměti tato konfigurační data, API Management může minimalizovat snížení výkonu dělat toto vyhledávání.

Prvním krokem je určit identifikátor použitý ke konfiguraci požadovanou verzi. V tomto příkladu se rozhodli spojit na verzi, aby kód product key pro předplatné. 

```xml
<set-variable name="clientid" value="@(context.Subscription.Key)" />
```

API Management pak provede vyhledávání v mezipaměti zobrazíte, zda již načtena na požadovanou verzi.

```xml
<cache-lookup-value
key="@("clientversion-" + context.Variables["clientid"])"
variable-name="clientversion" />
```

API Management se poté zkontroluje zobrazíte, pokud se nenašel žádné ji v mezipaměti.

```xml
<choose>
    <when condition="@(!context.Variables.ContainsKey("clientversion"))">
```

Pokud rozhraní API správy nebyl nalezen, API Management načte.

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

Rozbalte text těla odpovědi z odpovědi.

```xml
<set-variable
      name="clientversion"
      value="@(((IResponse)context.Variables["clientconfiguresponse"]).Body.As<string>())" />
```

Store ho zpátky do mezipaměti pro budoucí použití.

```xml
<cache-store-value
      key="@("clientversion-" + context.Variables["clientid"])"
      value="@((string)context.Variables["clientversion"])"
      duration="100000" />
```

A nakonec Aktualizujte adresu URL back-end k výběru verze požadované klientem služby.

```xml
<set-backend-service
      base-url="@(context.Api.ServiceUrl.ToString() + "api/" + (string)context.Variables["clientversion"] + "/")" />
```

Dokončení zásad vypadá takto:

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

Povolení rozhraní API příjemců transparentně řídit, které verze back-end je přistupováno klienty bez nutnosti aktualizace a opětovné nasazení klientů je elegantní řešení, která řeší mnoho aspekty správy verzí rozhraní API.

## <a name="tenant-isolation"></a>Izolaci klientů
Některé společnosti v nasazeních s více tenanty, větší vytvořit samostatné skupiny klientů v různých nasazení hardwaru back-endu. Tím se minimalizují počet zákazníků, kteří jsou ovlivněny hardwarové potíže na back-endu. Umožňuje také nová verze softwaru do nasazené ve fázích. Tato architektura back-end v ideálním případě by měl být transparentní pro zákazníky. To můžete dosáhnout podobným způsobem jako transparentní přes správu verzí, protože je založena na stejný postup manipulace s URL back-end pomocí konfigurace stavu na jeden klíč rozhraní API.  

Místo vrácení upřednostňovanou verzi rozhraní API pro každý klíč předplatného, by vrátit identifikátor, který se týká klienta do skupiny přiřazené hardwaru. Tento identifikátor slouží k vytvoření adresy URL příslušného back-endu.

## <a name="summary"></a>Souhrn
Dá volnost, abyste mezipaměť správy rozhraní API služby Azure pro ukládání všech typů dat umožňuje efektivní přístup ke konfigurační data, která může mít vliv na způsob zpracování příchozího požadavku. To lze také ukládat data fragmenty, které můžete rozšířit odpovědi vracené ze služby back-endového rozhraní API.
