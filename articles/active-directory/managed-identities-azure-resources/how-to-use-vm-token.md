---
title: Použití spravované identity pro prostředky Azure na virtuálním počítači k získání přístupového tokenu
description: Podrobné pokyny a příklady použití spravované identity pro prostředky Azure na virtuálních počítačích k získání přístupového tokenu OAuth.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: priyamo
ms.openlocfilehash: 8c554ad96b0f73d1e7bbc61f03c547a3191d5303
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54904246"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Jak získat přístupový token pomocí spravované identity pro prostředky Azure na Virtuálním počítači Azure 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Spravované identity pro prostředky Azure poskytuje služby Azure se automaticky spravované identity v Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, aniž by bylo přihlašovací údaje ve vašem kódu. 

Tento článek obsahuje příklady různých kódu a skriptů pro získání tokenu, jakož i pokyny důležitá témata, jako je zpracování vypršení platnosti tokenu a chyby protokolu HTTP. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud máte v plánu pomocí prostředí Azure PowerShell příklady v tomto článku, nezapomeňte nainstalovat nejnovější verzi [prostředí Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Všechny ukázky kódu či skript v tomto článku předpokládá, že klient je spuštěný na virtuálním počítači pomocí spravované identity pro prostředky Azure. Pomocí funkce "Připojit" virtuální počítač na webu Azure Portal, se vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači, naleznete v tématu [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md), nebo jeden z variant článků (pomocí Powershellu, rozhraní příkazového řádku, šablonu nebo Azure SADA SDK). 

> [!IMPORTANT]
> - Zabezpečení hranic spravovaných identit pro prostředky Azure, je prostředek, který se používá na. Všechny kód/skripty spuštěné na virtuálním počítači můžete vyžádat a získání tokenů pro všechny spravované identity k dispozici na něm. 

## <a name="overview"></a>Přehled

Klientská aplikace může požadovat spravovaných identit pro prostředky Azure [přístupový token jenom pro aplikace](../develop/developer-glossary.md#access-token) pro přístup k danému prostředku. Token je [podle spravovaných identit pro instanční objekt služby Azure prostředky](overview.md#how-does-it-work). V důsledku toho není nutné pro klienta k registraci k získání přístupového tokenu v rámci své vlastní instančního objektu. Token, který je vhodný pro použití jako nosný token v [vyžadující přihlašovací údaje pro klienta volání služba služba](../develop/v1-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [Získání tokenu pomocí protokolu HTTP](#get-a-token-using-http) | Koncový bod token podrobnosti protokolu pro spravované identity pro prostředky Azure |
| [Získání tokenu pomocí knihovnu Microsoft.Azure.Services.appauthentication přistupovat pro .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Příklad použití knihovnu Microsoft.Azure.Services.appauthentication přistupovat z klienta .NET
| [Získání tokenu pomocí jazyka C#](#get-a-token-using-c) | Příklad použití spravované identity pro koncový bod REST prostředků Azure z klientů jazyka C# |
| [Získání tokenu pomocí Javy](#get-a-token-using-java) | Příklad použití spravované identity pro koncový bod REST prostředků Azure z klientskou sadou Java |
| [Získání tokenu pomocí jazyka Go](#get-a-token-using-go) | Příklad použití spravované identity pro koncový bod REST prostředků Azure z Go klienta |
| [Získání tokenu pomocí Azure Powershellu](#get-a-token-using-azure-powershell) | Příklad použití spravované identity pro koncový bod REST prostředků Azure z prostředí PowerShell klienta |
| [Získání tokenu pomocí CURL](#get-a-token-using-curl) | Příklad použití spravované identity pro koncový bod REST prostředků Azure z prostředí Bash nebo nástroj CURL klienta |
| [Zpracování, ukládání tokenu do mezipaměti](#handling-token-caching) | Pokyny pro zpracování platnost přístupové tokeny |
| [Zpracování chyb](#error-handling) | Pokyny pro zpracování chyb HTTP vrácená ze spravovaných identit pro koncový bod tokenu prostředků Azure |
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
| `http://169.254.169.254/metadata/identity/oauth2/token` | Spravované identity pro koncový bod prostředků Azure pro služba Instance Metadata. |
| `api-version`  | Parametr řetězce dotazu, která verze rozhraní API pro koncový bod IMDS. Použijte prosím verzi rozhraní API `2018-02-01` nebo vyšší. |
| `resource` | Parametr řetězce dotazu, určující identifikátor URI ID aplikace z cílového prostředku. Zobrazí se také v `aud` deklarace identity (cílová skupina) vydaného tokenu. V tomto příkladu žádá token pro přístup k Azure Resource Manageru, který má být identifikátor URI ID aplikace z https://management.azure.com/. |
| `Metadata` | Pole hlavičky požadavku HTTP, vyžaduje spravované identity pro prostředky Azure jako zmírnění proti útoku Server na straně požadavek proti padělání (SSRF). Tato hodnota musí být nastavená na "true", malými písmeny. |
| `object_id` | (Volitelné) Parametr řetězce dotazu, která object_id spravovanou identitu, kterou byste chtěli token pro. Povinné, pokud se váš virtuální počítač má několik spravovaných uživatelsky přiřazené identity.|
| `client_id` | (Volitelné) Parametr řetězce dotazu, která client_id spravovanou identitu, kterou byste chtěli token. Povinné, pokud se váš virtuální počítač má několik spravovaných uživatelsky přiřazené identity.|

Ukázková žádost pomocí spravované identity pro prostředky Azure koncového bodu virtuálního počítače rozšíření *(plánovaná k převedení na zastaralého v lednu 2019)*:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, což znamená, že má k načtení dat z koncového bodu. V takovém případě OAuth přístupový token. | 
| `http://localhost:50342/oauth2/token` | Spravované identity pro koncový bod prostředků Azure, ve kterém 50342 je výchozím portem a je možné konfigurovat. |
| `resource` | Parametr řetězce dotazu, určující identifikátor URI ID aplikace z cílového prostředku. Zobrazí se také v `aud` deklarace identity (cílová skupina) vydaného tokenu. V tomto příkladu žádá token pro přístup k Azure Resource Manageru, který má být identifikátor URI ID aplikace z https://management.azure.com/. |
| `Metadata` | Pole hlavičky požadavku HTTP, vyžaduje spravované identity pro prostředky Azure jako zmírnění proti útoku Server na straně požadavek proti padělání (SSRF). Tato hodnota musí být nastavená na "true", malými písmeny.|
| `object_id` | (Volitelné) Parametr řetězce dotazu, která object_id spravovanou identitu, kterou byste chtěli token pro. Povinné, pokud se váš virtuální počítač má několik spravovaných uživatelsky přiřazené identity.|
| `client_id` | (Volitelné) Parametr řetězce dotazu, která client_id spravovanou identitu, kterou byste chtěli token. Povinné, pokud se váš virtuální počítač má několik spravovaných uživatelsky přiřazené identity.|


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
| `refresh_token` | Není možné použít pomocí spravované identity u prostředků Azure. |
| `expires_in` | Počet sekund, po které přístupový token je stále platné, před vypršením platnosti v době vydání. Čas vystavení lze nalézt v tokenu `iat` deklarací identity. |
| `expires_on` | Interval timespan, když vyprší platnost přístupového tokenu. Datum je vyjádřena jako počet sekund od "1970-01-01T0:0:0Z UTC" (odpovídá tokenu `exp` deklarace identity). |
| `not_before` | Interval timespan, když přístupový token se projeví a jdou přijmout. Datum je vyjádřena jako počet sekund od "1970-01-01T0:0:0Z UTC" (odpovídá tokenu `nbf` deklarace identity). |
| `resource` | Přístupový token se požádalo pro odpovídající prostředek `resource` požadavku parametr řetězce dotazu. |
| `token_type` | Zadejte token, který je přístupový token "Nosiče", což znamená, že prostředek může poskytnout přístup k nosný token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Získání tokenu pomocí knihovnu Microsoft.Azure.Services.appauthentication přistupovat pro .NET

Pro aplikace .NET a funkce je nejjednodušší způsob, jak pracovat s spravovaných identit pro prostředky Azure prostřednictvím balíčku Microsoft.Azure.Services.appauthentication přistupovat. Tato knihovna také umožňuje testovat kód místně na svém vývojovém počítači, pomocí uživatelského účtu z aplikace Visual Studio [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), nebo integrované ověřování Active Directory. Další informace o možnostech místní vývoj pomocí této knihovny, najdete v článku [Microsoft.Azure.Services.appauthentication přistupovat odkaz](/azure/key-vault/service-to-service-authentication). V této části se dozvíte, jak začít pracovat s knihovnou ve vašem kódu.

1. Přidat odkazy [Microsoft.Azure.Services.appauthentication přistupovat](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) balíčky NuGet pro vaši aplikaci.

2.  Přidejte následující kód do vaší aplikace:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Další informace o Microsoft.Azure.Services.appauthentication přistupovat a zpřístupňuje operací, najdete v článku [Microsoft.Azure.Services.appauthentication přistupovat odkaz](/azure/key-vault/service-to-service-authentication) a [spravované služby App Service a trezor klíčů s identity pro prostředky Azure .NET ukázku](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Získání tokenu pomocí jazyka C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
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

## <a name="get-a-token-using-java"></a>Získání tokenu pomocí Javy

Použijte tento [JSON knihovny](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) k načtení tokenu pomocí Javy.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
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
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
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

    // Call managed services for Azure resources token endpoint
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

Následující příklad ukazuje způsob použití spravované identity pro koncový bod REST prostředků Azure z prostředí PowerShell klienta:

1. Získání přístupového tokenu.
2. Použití přístupového tokenu pro volání rozhraní REST API Azure Resource Manageru a získat informace o virtuálním počítači. Nezapomeňte nahraďte ID předplatného, název skupiny prostředků a název virtuálního počítače pro `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, a `<VM-NAME>`v uvedeném pořadí.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Příklad toho, jak analyzovat přístupový token z odpovědi:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
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
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Ukládání tokenu do mezipaměti

Při spravované identity používá podsystému prostředky Azure (IMDS/spravovaných identit pro prostředky Azure rozšíření virtuálního počítače) ukládat do mezipaměti tokenů, doporučujeme také implementovat ukládání tokenu do mezipaměti ve vašem kódu. V důsledku toho by měl Příprava pro scénáře, ve kterém prostředek označuje, že platnost tokenu vypršela. 

Pouze výsledky volání na přenosu do služby Azure AD při:
- z důvodu žádný token ve spravovaných identit pro prostředky Azure subsystému mezipaměti dojde k neúspěšnému přístupu do mezipaměti
- platnost tokenu v mezipaměti

## <a name="error-handling"></a>Zpracování chyb

Spravované identity pro koncový bod prostředků Azure signály chyb 4xx a 5xx chyby prostřednictvím pole Stav kód záhlaví zprávy odpovědi HTTP:

| Stavový kód | Důvod chyby | Způsob zpracování |
| ----------- | ------------ | ------------- |
| 404 Nenalezeno. | Aktualizuje se koncový bod IMDS. | Zkuste to znovu s Expontential omezení rychlosti. Najdete v níže uvedeném doprovodném materiálu. |
| 429 příliš mnoho požadavků. |  Dosáhli jste limitu IMDS omezení. | Opakování pomocí exponenciálního omezení rychlosti. Najdete v níže uvedeném doprovodném materiálu. |
| v požadavku došlo k chybě 4xx. | Nejméně jeden z parametrů žádosti bylo nesprávné. | Neprovádějte opakování.  Zkontrolujte podrobnosti o chybě pro další informace.  4xx chyby jsou chyby v době návrhu.|
| 5xx přechodná chyba služby. | Spravované identity subsystémů prostředků Azure nebo Azure Active Directory vrátila o přechodnou chybu. | Je bezpečně opakovat po čekání alespoň 1 sekunda.  Pokud budete operaci opakovat příliš rychle nebo příliš často, může vrátit chybu omezení frekvence (429) IMDS a/nebo Azure AD.|
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
| 400 – Chybný požadavek | invalid_resource | AADSTS50001: Aplikace s názvem *\<URI\>* nebyl nalezen v tenantovi s názvem  *\<ID TENANTA\>*. To může nastat, pokud aplikace nebyla nainstalována správcem tenanta nebo souhlas. k libovolným uživatelem v tenantovi. Možná jste odeslali žádost o ověření do nesprávného tenanta. \ | (Pouze Linux) |
| 400 – Chybný požadavek | bad_request_102 | Není zadána hlavička požadovaná metadata | Buď `Metadata` pole hlavičky požadavku v požadavku chybí nebo je v nesprávném formátu. Hodnota musí být zadán jako `true`, malými písmeny. Naleznete v části "ukázkový požadavek" v [předchozí ZBÝVAJÍCÍ části](#rest) příklad.|
| 401 Neautorizováno | unknown_source | Neznámý zdroj  *\<identifikátoru URI\>* | Ověřte, že váš požadavek HTTP GET identifikátoru URI je správný. `scheme:host/resource-path` Část musí být zadán jako `http://localhost:50342/oauth2/token`. Naleznete v části "ukázkový požadavek" v [předchozí ZBÝVAJÍCÍ části](#rest) příklad.|
|           | invalid_request | Požadavku chybí povinný parametr, obsahuje neplatnou hodnotu parametru, obsahuje více než jednou. parametr nebo jinak je poškozený. |  |
|           | unauthorized_client | Klient není oprávněný žádat přístupový token pomocí této metody. | Způsobeno žádosti, která nebyla použití místní zpětné smyčky pro volání rozšíření, nebo na virtuálním počítači, na kterém není spravovaným identitám pro prostředky Azure, které jsou nakonfigurované správně. Zobrazit [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálního počítače. |
|           | access_denied | Vlastník prostředku nebo autorizační server tuto žádost odmítl. |  |
|           | unsupported_response_type | Autorizační server nepodporuje získání přístupového tokenu pomocí této metody. |  |
|           | invalid_scope | Požadovaný rozsah je neplatný, neznámý nebo poškozený. |  |
| Chyba 500 interní server | Neznámé | Nepovedlo se získat token ze služby Active directory. Podrobnosti najdete v protokolech  *\<cesta k souboru\>* | Ověřte, že spravovaných identit pro prostředky Azure je povoleno na virtuálním počítači. Zobrazit [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md) Pokud potřebujete pomoc s konfigurací virtuálního počítače.<br><br>Dál ověřte, že váš požadavek HTTP GET identifikátoru URI je správný, zejména zadaná v řetězci dotazu identifikátoru URI prostředku. Naleznete v části "ukázkový požadavek" v [předchozí ZBÝVAJÍCÍ části](#rest) příklad, nebo [služby Azure, že podpora Azure AD ověřování](services-support-msi.md) seznam služeb a jejich odpovídající ID prostředků.

## <a name="retry-guidance"></a>Pokyny pro opakování 

Doporučuje se opakovat, pokud se zobrazí kód 404, 429 nebo kód chyby 5xx (najdete v článku [zpracování chyb](#error-handling) výše).

Omezení platí pro počet volání na IMDS koncový bod. Při překročení omezení koncového bodu IMDS omezuje všechny další žádosti omezení je v platnosti. Během tohoto období se koncový bod IMDS vrátí stavový kód HTTP 429 ("příliš mnoho požadavků"), a neúspěšné požadavky. 

Zkuste to znovu doporučujeme následující strategie: 

| **Strategie opakování** | **Nastavení** | **Hodnoty** | **Jak to funguje** |
| --- | --- | --- | --- |
|ExponentialBackoff |Počet opakování<br />Min back-off<br />Max back-off<br />Delta back-off<br />První rychlé opakování |5<br />0 s<br />60 s<br />2 s<br />false (nepravda) |Pokus 1 – zpoždění 0 s<br />Pokus 2 – zpoždění ~2 s<br />Pokus 3 – zpoždění ~6 s<br />Pokus 4 – zpoždění ~14 s<br />Pokus 5 – zpoždění ~30 s |

## <a name="resource-ids-for-azure-services"></a>ID prostředků služeb Azure

Zobrazit [, že podpora Azure AD ověřování služby Azure](services-support-msi.md) seznam prostředků, které podporují služby Azure AD a prošel testováním s využitím spravované identity pro prostředky Azure a jejich odpovídající ID prostředků.


## <a name="next-steps"></a>Další postup

- Povolit spravovaným identitám pro prostředky Azure na Virtuálním počítači Azure, najdete v článku [konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí webu Azure portal](qs-configure-portal-windows-vm.md).








