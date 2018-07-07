---
title: Použití Azure VM identita spravované služby k získání přístupového tokenu
description: Podrobné pokyny a příklady použití MSI virtuálního počítače Azure k získání OAuth přístupový token.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: e564f48b4b90cfcaa72ed51d5f210a71a4980360
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902941"
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Použití Azure VM Identity spravované služby (MSI) pro získání tokenu 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Identita spravované služby poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

Tento článek obsahuje příklady různých kódu a skriptů pro získání tokenu, jakož i pokyny důležitá témata, jako je zpracování vypršení platnosti tokenu a chyby protokolu HTTP. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud máte v plánu pomocí prostředí Azure PowerShell příklady v tomto článku, nezapomeňte nainstalovat nejnovější verzi [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Všechny ukázky kódu či skript v tomto článku předpokládá, že klient je spuštěný na virtuálním počítači se identita spravované služby. Pomocí funkce "Připojení" virtuálního počítače na webu Azure Portal, se vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení MSI virtuálního počítače najdete v tématu [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md), nebo jeden z variant článků (pomocí Powershellu, rozhraní příkazového řádku, šablonu nebo pomocí sady Azure SDK). 

> [!IMPORTANT]
> - Hranice zabezpečení Identita spravované služby je prostředek, který se používá na. Všechny kód/skripty spuštěné na virtuálním počítači můžete vyžádat a získání tokenů pro všechny Identity spravované služby v něm k dispozici. 

## <a name="overview"></a>Přehled

Klientská aplikace může požadovat identita spravované služby [přístupový token jenom pro aplikace](../develop/active-directory-dev-glossary.md#access-token) pro přístup k danému prostředku. Token je [podle instančního objektu MSI](overview.md#how-does-it-work). V důsledku toho není nutné pro klienta k registraci k získání přístupového tokenu v rámci své vlastní instančního objektu. Token, který je vhodný pro použití jako nosný token v [vyžadující přihlašovací údaje pro klienta volání služba služba](../develop/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Získání tokenu pomocí protokolu HTTP](#get-a-token-using-http) | Podrobnosti protokolu pro koncový bod tokenu MSI |
| [Získání tokenu pomocí jazyka C#](#get-a-token-using-c) | Příklad použití koncového bodu MSI REST z klientů jazyka C# |
| [Získání tokenu pomocí jazyka Go](#get-a-token-using-go) | Příklad použití koncového bodu MSI REST z klienta Go |
| [Získání tokenu pomocí Azure Powershellu](#get-a-token-using-azure-powershell) | Příklad použití koncového bodu MSI REST z klienta prostředí PowerShell |
| [Získání tokenu pomocí CURL](#get-a-token-using-curl) | Příklad použití koncového bodu MSI REST z prostředí Bash nebo nástroj CURL klienta |
| [Zpracování, ukládání tokenu do mezipaměti](#handling-token-caching) | Pokyny pro zpracování platnost přístupové tokeny |
| [Zpracování chyb](#error-handling) | Pokyny pro zpracování chyb HTTP vrácená z koncového bodu tokenu MSI |
| [ID prostředků služeb Azure](#resource-ids-for-azure-services) | Získání ID prostředků pro podporované služby Azure |

## <a name="get-a-token-using-http"></a>Získání tokenu pomocí protokolu HTTP 

Základní rozhraní pro získání přístupového tokenu je založené na REST, zpřístupnění pro všechny klientské aplikace běžící na virtuálním počítači, který mohl provádět volání HTTP REST. To se podobá programovací model služby Azure AD, s výjimkou klienta používá koncový bod na virtuálním počítači (vs Azure AD koncový bod).

Ukázková žádost pomocí koncového bodu Azure Instance Metadata služby (IMDS) *(doporučeno)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, což znamená, že má k načtení dat z koncového bodu. V takovém případě OAuth přístupový token. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Koncový bod MSI pro služba Instance Metadata. |
| `api-version`  | Parametr řetězce dotazu, která verze rozhraní API pro koncový bod IMDS. Použijte prosím verzi rozhraní API `2018-02-01` nebo vyšší. |
| `resource` | Parametr řetězce dotazu, určující identifikátor URI ID aplikace z cílového prostředku. Zobrazí se také v `aud` deklarace identity (cílová skupina) vydaného tokenu. V tomto příkladu žádá token pro přístup k Azure Resource Manageru, který má být identifikátor URI ID aplikace z https://management.azure.com/. |
| `Metadata` | Pole hlavičky požadavku HTTP, vyžaduje Instalační služby MSI jako zmírnění proti útoku Server na straně požadavek proti padělání (SSRF). Tato hodnota musí být nastavená na "true", malými písmeny.

Ukázková žádost pomocí koncového bodu Identity spravované služby (MSI) virtuálního počítače rozšíření *(Chcete-li být zastaralé)*:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, což znamená, že má k načtení dat z koncového bodu. V takovém případě OAuth přístupový token. | 
| `http://localhost:50342/oauth2/token` | MSI koncový bod, ve kterém 50342 je výchozím portem a je možné konfigurovat. |
| `resource` | Parametr řetězce dotazu, určující identifikátor URI ID aplikace z cílového prostředku. Zobrazí se také v `aud` deklarace identity (cílová skupina) vydaného tokenu. V tomto příkladu žádá token pro přístup k Azure Resource Manageru, který má být identifikátor URI ID aplikace z https://management.azure.com/. |
| `Metadata` | Pole hlavičky požadavku HTTP, vyžaduje Instalační služby MSI jako zmírnění proti útoku Server na straně požadavek proti padělání (SSRF). Tato hodnota musí být nastavená na "true", malými písmeny.


Ukázkové odpovědi:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Popis |
| ------- | ----------- |
| `access_token` | Požadovaný přístupový token. Při volání rozhraní REST API zabezpečeným token, který je součástí `Authorization` pole hlavičky požadavku jako token "nosiče", umožňuje rozhraní API za účelem ověření volající. | 
| `refresh_token` | Není používána MSI. |
| `expires_in` | Počet sekund, po které přístupový token je stále platné, před vypršením platnosti v době vydání. Čas vystavení lze nalézt v tokenu `iat` deklarací identity. |
| `expires_on` | Interval timespan, když vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od "1970-01-01T0:0:0Z UTC" (odpovídá tokenu `exp` deklarace identity). |
| `not_before` | Interval timespan, když přístupový token se projeví a jdou přijmout. Datum je vyjádřena jako počet sekund od "1970-01-01T0:0:0Z UTC" (odpovídá tokenu `nbf` deklarace identity). |
| `resource` | Přístupový token se požádalo pro odpovídající prostředek `resource` požadavku parametr řetězce dotazu. |
| `token_type` | Zadejte token, který je přístupový token "Nosiče", což znamená, že prostředek může poskytnout přístup k nosný token. |

## <a name="get-a-token-using-c"></a>Získání tokenu pomocí jazyka C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create(http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>Získání tokenu pomocí jazyka Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call MSI /token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Získání tokenu pomocí Azure Powershellu

Následující příklad ukazuje, jak použít koncový bod MSI REST z klienta Powershellu:

1. Získání přístupového tokenu.
2. Použití přístupového tokenu pro volání rozhraní REST API Azure Resource Manageru a získat informace o virtuálním počítači. Nezapomeňte nahraďte ID předplatného, název skupiny prostředků a název virtuálního počítače pro `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, a `<VM-NAME>`v uvedeném pořadí.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Příklad toho, jak analyzovat přístupový token z odpovědi:
```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Získání tokenu pomocí CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Příklad toho, jak analyzovat přístupový token z odpovědi:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="token-caching"></a>Ukládání tokenu do mezipaměti

I když podsystém Identity spravované služby (MSI) používá (rozšíření virtuálního počítače IMDS/MSI) mezipaměti ukládat tokeny, doporučujeme také implementovat ukládání tokenu do mezipaměti ve vašem kódu. V důsledku toho by měl Příprava pro scénáře, ve kterém prostředek označuje, že platnost tokenu vypršela. 

Pouze výsledky volání na přenosu do služby Azure AD při:
- z důvodu žádný token v mezipaměti subsystému MSI dojde k neúspěšnému přístupu do mezipaměti
- platnost tokenu v mezipaměti

## <a name="error-handling"></a>Zpracování chyb

Identita spravované služby koncového bodu signály chyb 4xx a 5xx chyby prostřednictvím pole Kód stavu zprávy hlavičku HTTP odpovědi:

| Stavový kód | Důvod chyby | Způsob zpracování |
| ----------- | ------------ | ------------- |
| 404 Nenalezeno. | Aktualizuje se koncový bod IMDS. | Zkuste to znovu s Expontential omezení rychlosti. Najdete v níže uvedeném doprovodném materiálu. |
| 429 příliš mnoho požadavků. |  Dosáhli jste limitu IMDS omezení. | Opakování pomocí exponenciálního omezení rychlosti. Najdete v níže uvedeném doprovodném materiálu. |
| v požadavku došlo k chybě 4xx. | Nejméně jeden z parametrů žádosti bylo nesprávné. | Neprovádějte opakování.  Zkontrolujte podrobnosti o chybě pro další informace.  4xx chyby jsou chyby v době návrhu.|
| 5xx přechodná chyba služby. | MSI subsystémů nebo Azure Active Directory vrátila o přechodnou chybu. | Je bezpečně opakovat po čekání alespoň 1 sekunda.  Pokud budete operaci opakovat příliš rychle nebo příliš často, může vrátit chybu omezení frekvence (429) IMDS a/nebo Azure AD.|
| timeout | Aktualizuje se koncový bod IMDS. | Zkuste to znovu s Expontential omezení rychlosti. Najdete v níže uvedeném doprovodném materiálu. |

Pokud dojde k chybě, obsahuje odpovídající textu odpovědi HTTP JSON se podrobnosti o chybě:

| Element | Popis |
| ------- | ----------- |
| error   | Chyba identifikátoru. |
| error_description | Podrobný popis chyby. **Popisy chyb můžete kdykoli změnit. Nezapisovat kód, který větví na základě hodnot v popisu chyby.**|

### <a name="http-response-reference"></a>Odkaz na odpovědi HTTP

Tato část popisuje možné chybové odpovědi. Objekt "200 OK" stav je úspěšné odpovědi a přístupový token je obsažený v těle odpovědi JSON, v elementu access_token.

| Kód stavu | Chyba | Popis chyby | Řešení |
| ----------- | ----- | ----------------- | -------- |
| 400 – Chybný požadavek | invalid_resource | AADSTS50001: Aplikaci s názvem *\<URI\>* nebyl nalezen v tenantovi s názvem  *\<ID TENANTA\>*. To může nastat, pokud aplikace nebyla nainstalována správcem tenanta nebo souhlas. k libovolným uživatelem v tenantovi. Možná jste odeslali žádost o ověření do nesprávného tenanta. \ | (Pouze Linux) |
| 400 – Chybný požadavek | bad_request_102 | Není zadána hlavička požadovaná metadata | Buď `Metadata` pole hlavičky požadavku v požadavku chybí nebo je v nesprávném formátu. Hodnota musí být zadán jako `true`, malými písmeny. Naleznete v části "ukázkový požadavek" v [předchozí ZBÝVAJÍCÍ části](#rest) příklad.|
| 401 Neautorizováno | unknown_source | Neznámý zdroj  *\<identifikátoru URI\>* | Ověřte, že váš požadavek HTTP GET identifikátoru URI je správný. `scheme:host/resource-path` Část musí být zadán jako `http://localhost:50342/oauth2/token`. Naleznete v části "ukázkový požadavek" v [předchozí ZBÝVAJÍCÍ části](#rest) příklad.|
|           | invalid_request | Požadavku chybí povinný parametr, obsahuje neplatnou hodnotu parametru, obsahuje více než jednou. parametr nebo jinak je poškozený. |  |
|           | unauthorized_client | Klient není oprávněný žádat přístupový token pomocí této metody. | Způsobeno žádosti, která nebyla použití místní zpětné smyčky pro volání rozšíření, nebo na virtuálním počítači, na kterém není instalační služba MSI správně nakonfigurovaný. Zobrazit [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálního počítače. |
|           | ACCESS_DENIED | Vlastník prostředku nebo autorizační server tuto žádost odmítl. |  |
|           | unsupported_response_type | Autorizační server nepodporuje získání přístupového tokenu pomocí této metody. |  |
|           | invalid_scope | Požadovaný rozsah je neplatný, neznámý nebo poškozený. |  |
| Chyba 500 interní server | Neznámé | Nepovedlo se získat token ze služby Active directory. Podrobnosti najdete v protokolech  *\<cesta k souboru\>* | Ověřte, že instalační služby MSI je povoleno na virtuálním počítači. Zobrazit [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálního počítače.<br><br>Dál ověřte, že váš požadavek HTTP GET identifikátoru URI je správný, zejména zadaná v řetězci dotazu identifikátoru URI prostředku. Naleznete v části "ukázkový požadavek" v [předchozí ZBÝVAJÍCÍ části](#rest) příklad, nebo [služby Azure, že podpora Azure AD ověřování](services-support-msi.md) seznam služeb a jejich odpovídající ID prostředků.

## <a name="retry-guidance"></a>Pokyny pro opakování 

Doporučuje se opakovat, pokud se zobrazí kód 404, 429 nebo kód chyby 5xx (najdete v článku [zpracování chyb](#error-handling) výše).

Omezení platí pro počet volání na IMDS koncový bod. Při překročení omezení koncového bodu IMDS omezuje všechny další žádosti omezení je v platnosti. Během tohoto období se koncový bod IMDS vrátí stavový kód HTTP 429 ("příliš mnoho požadavků"), a neúspěšné požadavky. 

Zkuste to znovu doporučujeme následující strategie: 

| **Strategie opakování** | **Nastavení** | **Hodnoty** | **Jak to funguje** |
| --- | --- | --- | --- |
|ExponentialBackoff |Počet opakování<br />Min back-off<br />Max back-off<br />Delta back-off<br />První rychlé opakování |5<br />0 s<br />60 s<br />2 s<br />false (nepravda) |Pokus 1 – zpoždění 0 s<br />Pokus 2 – zpoždění ~2 s<br />Pokus 3 – zpoždění ~6 s<br />Pokus 4 – zpoždění ~14 s<br />Pokus 5 – zpoždění ~30 s |

## <a name="resource-ids-for-azure-services"></a>ID prostředků služeb Azure

Zobrazit [, že podpora Azure AD ověřování služby Azure](services-support-msi.md) seznam prostředků, které podporují služby Azure AD a prošel testováním s využitím MSI a jejich odpovídající ID prostředků.


## <a name="related-content"></a>Související obsah

- Povolení MSI ve Virtuálním počítači Azure, najdete v článku [nakonfigurovat virtuálním počítači Identity spravované služby (MSI) pomocí webu Azure portal](qs-configure-portal-windows-vm.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.








