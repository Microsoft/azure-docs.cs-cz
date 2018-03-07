---
title: "Jak získat přístupový token pomocí Identity služby spravovat Azure virtuálních počítačů"
description: "Podrobné pokyny a příklady pro získání OAuth pomocí souboru MSI virtuálních počítačů Azure přístup k tokenu."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 3d9d4d682a25d11129e81855a6bf149ac1d5cff0
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Jak používat Azure virtuálního počítače spravované služby Identity (MSI) pro získání tokenu 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Tento článek obsahuje příklady různých kód a skript pro získání tokenu, jakož i pokyny k důležité oblastech, jako je zpracování vypršení platnosti tokenu a chyb protokolu HTTP.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud budete chtít použít v prostředí Azure PowerShell příkladech v tomto článku, je nutné nainstalovat nejnovější verzi [prostředí Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Všechny ukázkový kód nebo skript v tomto článku předpokládá klienta běží na virtuálním počítači povolená MSI. Použijte funkci "Připojit" virtuální počítač na portálu Azure se vzdáleně připojit k virtuálnímu počítači. Informace o povolení MSI na virtuálním počítači, v [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md), nebo jeden z typu variant článků (pomocí prostředí PowerShell, rozhraní příkazového řádku, šablonu nebo Azure SDK). 

## <a name="overview"></a>Přehled

Klientská aplikace může požádat o MSI [jen aplikace přístupový token](develop/active-directory-dev-glossary.md#access-token) pro přístup k danému prostředku. Token je [podle objektu služby MSI](msi-overview.md#how-does-it-work). Jako takový není nutné pro klienta k registraci k získání tokenu přístupu v části vlastní instanční objekt. Token je vhodná pro použití jako token nosiče v [service-to-service volá vyžadují pověření klienta](active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Získat token pomocí protokolu HTTP](#get-a-token-using-http) | Podrobnosti protokolu pro koncový bod tokenu MSI |
| [Získat token pomocí jazyka C#](#get-a-token-using-c) | Příklad použití koncový bod MSI REST z klienta C# |
| [Získat token pomocí přejděte](#get-a-token-using-go) | Příklad použití koncový bod MSI REST z přejděte klienta |
| [Získat token pomocí Azure PowerShell](#get-a-token-using-azure-powershell) | Příklad použití koncový bod MSI REST z klienta PowerShell |
| [Získat token pomocí CURL](#get-a-token-using-curl) | Příklad použití koncový bod MSI REST z klienta Bash/CURL |
| [Zpracování vypršení platnosti tokenu](#handling-token-expiration) | Pokyny pro nakládání s vypršenou platností přístupové tokeny |
| [Zpracování chyb](#error-handling) | Pokyny pro zpracování chyby protokolu HTTP vrácená z koncový bod tokenu MSI |
| [ID prostředků pro služby Azure](#resource-ids-for-azure-services) | Získání ID prostředku pro podporované služby Azure |

## <a name="get-a-token-using-http"></a>Získat token pomocí protokolu HTTP 

Základní rozhraní pro získání tokenu přístupu je založena na REST, zpřístupnění pro všechny klientské aplikace spuštěna na virtuálním počítači, který může provádět volání HTTP REST. Toto je podobná programovací model Azure AD, s výjimkou klient používá koncový bod localhost na virtuálním počítači (vs Azure AD koncového bodu).

Ukázková žádost:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, která určuje, že chcete načíst data z koncového bodu. V takovém případě OAuth přístupový token. | 
| `http://localhost:50342/oauth2/token` | MSI koncového bodu, kde 50342 je výchozím portem a je možné konfigurovat. |
| `resource` | Parametr řetězce dotazu, která udává, identifikátor ID URI aplikace cílového prostředku. Zobrazuje se taky v `aud` vydaný token deklarace identity (cílové skupiny). Tento příklad požádá o token pro přístup k Azure Resource Manager, který má identifikátor ID URI aplikace z https://management.azure.com/. |
| `Metadata` | Pole hlavičky požadavku HTTP, vyžadují MSI jako zmírnění proti útoku serveru straně požadavek padělání (SSRF). Tato hodnota musí být nastavena na hodnotu true, všechny malými písmeny.

Ukázková odpověď:

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
| `access_token` | Požadovaný přístupový token. Při volání metody zabezpečené rozhraní REST API, je token vložený do `Authorization` pole hlavičky požadavku jako token "nosiče", umožňuje rozhraní API pro ověření volající. | 
| `refresh_token` | Není používán MSI. |
| `expires_in` | Počet sekund, po které přístupový token je stále platný, před vypršením platnosti od času vystavování. Čas vystavení lze nalézt v tokenu `iat` deklarací identity. |
| `expires_on` | Časový interval, když vyprší platnost přístupového tokenu. Datum je reprezentován jako počet sekund od "pod hodnotou 1970-01-01T0:0:0Z UTC" (odpovídá na token `exp` deklarace identity). |
| `not_before` | Časový interval, když se projeví přístupový token a nelze přijmout. Datum je reprezentován jako počet sekund od "pod hodnotou 1970-01-01T0:0:0Z UTC" (odpovídá na token `nbf` deklarace identity). |
| `resource` | Prostředek přístupový token byl požadován pro, který odpovídá `resource` požadavku parametr řetězce dotazu. |
| `token_type` | Typ tokenu, což je přístupový token "Nosiče", což znamená, že prostředek můžete poskytnout přístup k nosiče tohoto tokenu. |

## <a name="get-a-token-using-c"></a>Získat token pomocí jazyka C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
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

## <a name="get-a-token-using-go"></a>Získat token pomocí přejděte

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
    msi_endpoint, err := url.Parse("http://localhost:50342/oauth2/token")
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

## <a name="get-a-token-using-azure-powershell"></a>Získat token pomocí Azure PowerShell

Následující příklad ukazuje, jak používat koncový bod MSI REST z klienta PowerShell tak, aby:

1. Získejte přístupový token.
2. Použití tokenu přístupu volání rozhraní REST API Azure Resource Manager a získat informace o virtuálním počítači. Nezapomeňte nahradit vaše ID odběru, název skupiny prostředků a název virtuálního počítače pro `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, a `<VM-NAME>`, v uvedeném pořadí.

```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Získat token pomocí CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Zpracování vypršení platnosti tokenu

Subsystém místní MSI tokeny ukládá do mezipaměti. Proto můžete volat ho tak často, jak se vám líbí a volání na přenosu do služby Azure AD výsledků pouze, pokud:
- dojde k neúspěšnému přístupu do mezipaměti z důvodu žádné token v mezipaměti
- vypršela platnost tokenu

Pokud jste do mezipaměti tokenu ve vašem kódu, byste měli být připravení zpracovávat scénáře, kde prostředek udává, že vypršela platnost tokenu.

## <a name="error-handling"></a>Zpracování chyb 

Koncový bod MSI signály chyby přes pole Kód stavu záhlaví zprávy odpovědi HTTP, jako 4xx nebo 5xx chyb:

| Stavový kód | Důvod chyby | Postupy: zpracování |
| ----------- | ------------ | ------------- |
| došlo k chybě 4xx v požadavku. | Jeden nebo více parametrů žádosti bylo nesprávné. | Nezkoušejte zopakovat.  Zkontrolujte podrobnosti o chybě pro další informace.  4xx chyby jsou chyby při návrhu.|
| 5xx přechodné chybě ze služby. | Dílčí systému MSI nebo Azure Active Directory vrátila přechodné chybě. | Je bezpečné opakujte po čekání minimálně 1 sekunda.  Pokud se znovu pokusíte příliš rychle nebo příliš často, Azure AD může vrátí chybu maximální rychlost (429).|

Pokud dojde k chybě, obsahuje odpovídající text odpovědi HTTP JSON s podrobnosti o chybě:

| Element | Popis |
| ------- | ----------- |
| error   | Chyba identifikátor. |
| error_description | Podrobný popis chyby. **Popisy chyb můžete kdykoli změnit. Nezapisovat kód, který větví na základě hodnot v popisu chyby.**|

### <a name="http-response-reference"></a>Odkaz na odpovědi HTTP

Tato část popisuje možné chybové odpovědi. A "200 OK" ve stavu úspěšné odpovědi a přístupový token se nachází v textu odpovědi JSON, v elementu access_token.

| Stavový kód | Chyba | Popis chyby | Řešení |
| ----------- | ----- | ----------------- | -------- |
| 400 – Chybný požadavek | invalid_resource | AADSTS50001: Aplikaci s názvem  *\<URI\>*  nebyl nalezen v klientovi s názvem  *\<ID klienta\>*. To může nastat, když aplikace nebyla nainstalována správcem klienta nebo souhlas žádný uživatel v klientovi. Požadavek na ověření mohou mít odeslány nesprávný klienta. \ | (Pouze Linux) |
| 400 – Chybný požadavek | bad_request_102 | Není zadána hlavička požadovaná metadata | Buď `Metadata` pole hlavičky požadavku ze svého požadavku chybí nebo je v nesprávném formátu. Hodnota musí být zadány jako `true`, všechny malými písmeny. V části "Ukázková žádost" v [předcházející části REST](#rest) příklad.|
| 401 unauthorized | unknown_source | Neznámé zdroje  *\<identifikátor URI\>* | Ověřte, že žádost HTTP GET URI správně naformátován. `scheme:host/resource-path` Část musí být zadány jako `http://localhost:50342/oauth2/token`. V části "Ukázková žádost" v [předcházející části REST](#rest) příklad.|
|           | invalid_request | Žádosti chybí povinný parametr, obsahuje neplatnou hodnotu parametru, parametr obsahuje více než jednou nebo je jinak poškozený. |  |
|           | unauthorized_client | Klient nemá oprávnění k žádosti o token přístupu pomocí této metody. | Způsobené požadavek, který nepoužili místní smyčky volání rozšíření, nebo na virtuální počítač, který nemá MSI správně nakonfigurován. V tématu [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálních počítačů. |
|           | access_denied | Vlastník prostředku nebo autorizace server odepřel žádost. |  |
|           | unsupported_response_type | Autorizace serveru nepodporuje získání tokenu přístupu pomocí této metody. |  |
|           | invalid_scope | Požadovaný rozsah je neplatný, neznámý nebo je nesprávný. |  |
| 500 vnitřní chybu serveru | Neznámé | Nepodařilo se získat token ze služby Active directory. Podrobnosti najdete v tématu protokoly v  *\<cesta k souboru\>* | Ověřte, že je povoleno MSI ve virtuálním počítači. V tématu [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálních počítačů.<br><br>Také ověřte, že žádost HTTP GET URI správně naformátován, zejména prostředek, který je zadaný identifikátor URI v řetězci dotazu. Najdete v části "Ukázková žádost" v [předcházející části REST](#rest) příklad, nebo [služeb Azure, podpora Azure AD ověření](msi-overview.md#azure-services-that-support-azure-ad-authentication) seznam služeb a jejich odpovídající ID prostředku.

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure

V tématu [služeb Azure, podpora Azure AD ověření](msi-overview.md#azure-services-that-support-azure-ad-authentication) seznam prostředků, které podporují Azure AD a byly testovány s MSI a jejich odpovídající ID prostředku.


## <a name="related-content"></a>Související obsah

- Pokud chcete povolit MSI ve virtuálním počítači Azure, najdete v části [konfigurace virtuálních počítačů spravovaných služba Identity (MSI) pomocí portálu Azure](msi-qs-configure-portal-windows-vm.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.








