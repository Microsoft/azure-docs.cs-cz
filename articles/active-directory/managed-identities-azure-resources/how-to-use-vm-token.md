---
title: Použití spravovaných identit na virtuálním počítači k získání přístupového tokenu – Azure AD
description: Podrobný postup a příklady použití spravovaných identit pro prostředky Azure na virtuálních počítačích pro získání přístupového tokenu OAuth.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541f76ad825f492679530902c571096ca4b01902
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98726227"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Použití spravovaných identit pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu. 

Tento článek poskytuje různé příklady kódu a skriptů pro získání tokenu a také pokyny k důležitým tématům, jako je zpracování vypršení platnosti tokenu a chyby protokolu HTTP. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud plánujete použít Azure PowerShell příklady v tomto článku, nainstalujte si nejnovější verzi [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Veškerý ukázkový kód/skript v tomto článku předpokládá, že klient běží na virtuálním počítači se spravovanými identitami pro prostředky Azure. Pomocí funkce připojit k virtuálnímu počítači v Azure Portal se můžete vzdáleně připojit k vašemu VIRTUÁLNÍmu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md)nebo některého z článků variant (pomocí PowerShellu, rozhraní příkazového řádku, šablony nebo sady Azure SDK). 

> [!IMPORTANT]
> - Hranice zabezpečení spravovaných identit pro prostředky Azure je prostředek, na kterém se používá. Všechny kódy a skripty běžící na virtuálním počítači můžou vyžádat a načíst tokeny pro všechny spravované identity, které jsou na ní dostupné. 

## <a name="overview"></a>Přehled

Klientská aplikace může pro přístup k danému prostředku požádat o spravované identity [jenom přístupového tokenu](../develop/developer-glossary.md#access-token) pro prostředky Azure. Token je [založený na spravovaných identitách pro objekt služby Azure Resources](overview.md#managed-identity-types). V takovém případě není nutné, aby se klient zaregistroval k získání přístupového tokenu v rámci vlastního instančního objektu. Token je vhodný k použití jako nosný token ve [voláních služby-služba vyžadujících pověření klienta](../develop/v2-oauth2-client-creds-grant-flow.md).

| Odkaz | Popis |
| -------------- | -------------------- |
| [Získání tokenu pomocí protokolu HTTP](#get-a-token-using-http) | Podrobnosti protokolu pro spravované identity pro koncový bod tokenu prostředků Azure |
| [Získání tokenu pomocí knihovny Microsoft. Azure. Services. AppAuthentication pro .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Příklad použití knihovny Microsoft. Azure. Services. AppAuthentication z klienta .NET
| [Získání tokenu pomocí jazyka C #](#get-a-token-using-c) | Příklad použití spravovaných identit pro prostředky Azure – koncový bod REST z klienta v jazyce C# |
| [Získání tokenu pomocí jazyka Java](#get-a-token-using-java) | Příklad použití spravovaných identit pro prostředky Azure – koncový bod REST z klienta Java |
| [Získání tokenu pomocí jazyka přejít](#get-a-token-using-go) | Příklad použití spravovaných identit pro prostředky Azure – koncový bod REST z klienta přejít |
| [Získání tokenu pomocí Azure PowerShell](#get-a-token-using-azure-powershell) | Příklad použití spravovaných identit pro prostředky Azure – koncový bod REST z klienta PowerShellu |
| [Získání tokenu pomocí KUDRLINKOU](#get-a-token-using-curl) | Příklad použití spravovaných identit pro prostředky Azure – koncový bod REST z bash/SLOŽENÉho klienta |
| Zpracovávání tokenu do mezipaměti | Pokyny pro zpracování přístupových tokenů, jejichž platnost vypršela |
| [Zpracování chyb](#error-handling) | Pokyny pro zpracování chyb HTTP vrácených ze spravovaných identit pro koncový bod tokenu prostředků Azure |
| [ID prostředků pro služby Azure](#resource-ids-for-azure-services) | Kde získat ID prostředků pro podporované služby Azure |

## <a name="get-a-token-using-http"></a>Získání tokenu pomocí protokolu HTTP 

Základní rozhraní pro získání přístupového tokenu je založené na REST, které je dostupné pro všechny klientské aplikace spuštěné na virtuálním počítači, které můžou provádět volání HTTP REST. To se podobá programovacímu modelu Azure AD s tím rozdílem, že klient používá koncový bod na virtuálním počítači (oproti koncovému bodu Azure AD).

Ukázková žádost s použitím koncového bodu Azure Instance Metadata Service ( *doporučeno)*:

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, který indikuje, že chcete načíst data z koncového bodu. V tomto případě se jedná o přístupový token OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Koncový bod spravovaných identit pro prostředky Azure pro Instance Metadata Service. |
| `api-version`  | Parametr řetězce dotazu, který označuje verzi rozhraní API pro koncový bod IMDS. Použijte prosím verzi API `2018-02-01` nebo novější. |
| `resource` | Parametr řetězce dotazu, který označuje identifikátor URI ID aplikace cílového prostředku. Také se zobrazí v `aud` deklaraci identity (cílová skupina) vydaného tokenu. Tento příklad vyžaduje token pro přístup k Azure Resource Manager, která má identifikátor URI ID aplikace `https://management.azure.com/` . |
| `Metadata` | Pole hlavičky požadavku HTTP, které vyžadují spravované identity prostředků Azure jako zmírnění útoků na straně serveru (SSRF). Tato hodnota musí být nastavená na "true", a to u všech malých písmen. |
| `object_id` | Volitelné Parametr řetězce dotazu, který označuje object_id spravované identity, pro kterou chcete vytvořit token. Vyžaduje se, pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem.|
| `client_id` | Volitelné Parametr řetězce dotazu, který označuje client_id spravované identity, pro kterou chcete vytvořit token. Vyžaduje se, pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem.|
| `mi_res_id` | Volitelné Parametr řetězce dotazu, který označuje mi_res_id (ID prostředku Azure) spravované identity, pro kterou chcete token. Vyžaduje se, pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem. |

Ukázková žádost s použitím spravovaných identit pro Azure Resources Endpoint Extension *(plánované pro vyřazení v lednu 2019)*:

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Příkaz HTTP, který indikuje, že chcete načíst data z koncového bodu. V tomto případě se jedná o přístupový token OAuth. | 
| `http://localhost:50342/oauth2/token` | Spravované identity pro koncové body prostředků Azure, kde 50342 je výchozí port a dá se konfigurovat. |
| `resource` | Parametr řetězce dotazu, který označuje identifikátor URI ID aplikace cílového prostředku. Také se zobrazí v `aud` deklaraci identity (cílová skupina) vydaného tokenu. Tento příklad vyžaduje token pro přístup k Azure Resource Manager, která má identifikátor URI ID aplikace `https://management.azure.com/` . |
| `Metadata` | Pole hlavičky požadavku HTTP, které vyžadují spravované identity prostředků Azure jako zmírnění útoků na straně serveru (SSRF). Tato hodnota musí být nastavená na "true", a to u všech malých písmen.|
| `object_id` | Volitelné Parametr řetězce dotazu, který označuje object_id spravované identity, pro kterou chcete vytvořit token. Vyžaduje se, pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem.|
| `client_id` | Volitelné Parametr řetězce dotazu, který označuje client_id spravované identity, pro kterou chcete vytvořit token. Vyžaduje se, pokud má váš virtuální počítač více spravovaných identit přiřazených uživatelem.|

Ukázková odpověď:

```json
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
| `access_token` | Požadovaný přístupový token Při volání zabezpečeného REST API se token vloží do `Authorization` pole Hlavička požadavku jako "nosič", což umožňuje rozhraní API ověřit volajícího. | 
| `refresh_token` | Nepoužívá se spravovanými identitami pro prostředky Azure. |
| `expires_in` | Doba v sekundách, po kterou je přístupový token nadále platný, před vypršením platnosti, od doby vystavení. Čas vystavení se dá najít v `iat` deklaraci identity tokenu. |
| `expires_on` | Časový interval pro přístup k vypršení platnosti přístupového tokenu Datum se reprezentuje jako počet sekund od "1970-01-01T0:0: 0Z UTC" (odpovídá `exp` deklaraci identity tokenu). |
| `not_before` | Časové rozpětí, kdy se přístupový token projeví a lze jej přijmout. Datum se reprezentuje jako počet sekund od "1970-01-01T0:0: 0Z UTC" (odpovídá `nbf` deklaraci identity tokenu). |
| `resource` | Prostředek, pro který byl požadován přístupový token, který odpovídá `resource` parametru řetězce dotazu žádosti. |
| `token_type` | Typ tokenu, což je přístupový token "nosiče", což znamená, že prostředek může udělit přístup k nosiči tohoto tokenu. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Získání tokenu pomocí knihovny Microsoft. Azure. Services. AppAuthentication pro .NET

Pro aplikace a funkce .NET je nejjednodušší způsob práce se spravovanými identitami prostředků Azure prostřednictvím balíčku Microsoft. Azure. Services. AppAuthentication. Tato knihovna vám také umožní testovat kód místně na vývojovém počítači pomocí uživatelského účtu ze sady Visual Studio, [Azure CLI](/cli/azure)nebo integrovaného ověřování služby Active Directory. Další informace o možnostech místního vývoje pomocí této knihovny najdete v [referenčních informacích k Microsoft. Azure. Services. AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication). V této části se dozvíte, jak začít s knihovnou ve vašem kódu.

1. Do své aplikace přidejte odkazy na balíčky [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft. Azure. trezoru](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) .

2.  Do aplikace přidejte následující kód:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Další informace o Microsoft. Azure. Services. AppAuthentication a o operacích, které zpřístupňuje, najdete v tématu Referenční dokumentace ke [službě Microsoft. Azure. Services. AppAuthentication](/dotnet/api/overview/azure/service-to-service-authentication) a [App Service a trezoru klíčů se spravovanými identitami pro Azure Resources .NET – ukázka](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Získání tokenu pomocí jazyka C #

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
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

## <a name="get-a-token-using-java"></a>Získání tokenu pomocí jazyka Java

Pomocí této [knihovny JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) načtěte token pomocí jazyka Java.

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

## <a name="get-a-token-using-go"></a>Získání tokenu pomocí jazyka přejít

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

## <a name="get-a-token-using-azure-powershell"></a>Získání tokenu pomocí Azure PowerShell

Následující příklad demonstruje použití spravovaných identit pro prostředky Azure REST Endpoint z klienta PowerShellu pro:

1. Získání přístupového tokenu
2. Pomocí přístupového tokenu můžete volat Azure Resource Manager REST API a získat informace o virtuálním počítači. Nezapomeňte nahradit ID předplatného, název skupiny prostředků a název virtuálního počítače pro `<SUBSCRIPTION-ID>` , a v `<RESOURCE-GROUP>` `<VM-NAME>` uvedeném pořadí.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Příklad analýzy přístupového tokenu z odpovědi:
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

## <a name="get-a-token-using-curl"></a>Získání tokenu pomocí KUDRLINKOU

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Příklad analýzy přístupového tokenu z odpovědi:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Ukládání tokenů do mezipaměti

I když se používané subsystém spravované identity pro prostředky Azure (IMDS/Managed identity pro Azure Resources Extension) nachází v tokenech mezipaměti, doporučujeme také implementovat mezipaměť tokenů ve vašem kódu. V důsledku toho byste měli připravit na scénáře, kde prostředek indikuje, že platnost tokenu vypršela. 

Volání na výsledek Azure AD, jenom když:
- k mezipaměti nedochází v důsledku neúspěšného tokenu ve spravovaných identitách pro mezipaměť subsystému prostředků Azure.
- platnost tokenu uloženého v mezipaměti vypršela.

## <a name="error-handling"></a>Zpracování chyb

Spravované identity pro prostředky Azure – koncový bod signalizuje chyby prostřednictvím pole Stavový kód hlavičky zprávy odpovědi HTTP, jako 4xx nebo 5xx chyby:

| Stavový kód | Důvod chyby | Postup zpracování |
| ----------- | ------------ | ------------- |
| 404 nebyl nalezen. | Aktualizuje se koncový bod IMDS. | Zkuste to znovu s exponenciálním omezení rychlosti. Viz pokyny níže. |
| 429 příliš mnoho požadavků. |  Bylo dosaženo limitu omezení IMDS. | Zkuste to znovu s exponenciálním omezení rychlosti. Viz pokyny níže. |
| Chyba 4xx v žádosti. | Minimálně jeden parametr požadavku byl nesprávný. | Neopakovat akci.  Další informace najdete v podrobnostech o chybě.  chyby 4xx jsou chyby v době návrhu.|
| 5xx přechodná chyba ze služby. | Spravované identity pro dílčí systém prostředků Azure nebo Azure Active Directory vrátily přechodnou chybu. | Po čekání alespoň na 1 sekundu je bezpečné ho zkusit opakovat.  Pokud to provedete příliš rychle nebo příliš často, IMDS a/nebo Azure AD může vrátit chybu omezení četnosti (429).|
| timeout | Aktualizuje se koncový bod IMDS. | Zkuste to znovu s exponenciálním omezení rychlosti. Viz pokyny níže. |

Pokud dojde k chybě, odpovídající tělo odpovědi HTTP obsahuje JSON s podrobnostmi o chybě:

| Element | Popis |
| ------- | ----------- |
| error   | Identifikátor chyby |
| error_description | Podrobný popis chyby **Popisy chyb se můžou kdykoli změnit. Nepište kód, který se větví na základě hodnot v popisu chyby.**|

### <a name="http-response-reference"></a>Reference k odpovědi HTTP

V této části se nacházejí možné chybové odpovědi. Stav "200 OK" je úspěšná odpověď a přístupový token je obsažen v kódu JSON zprávy odpovědi v prvku access_token.

| Stavový kód | Chyba | Popis chyby | Řešení |
| ----------- | ----- | ----------------- | -------- |
| 400 – Chybný požadavek | invalid_resource | AADSTS50001: aplikace s názvem nebyla *\<URI\>* v tenantovi s názvem nalezena *\<TENANT-ID\>* . K tomu může dojít v případě, že aplikace nebyla nainstalována správcem tenanta nebo nebyla odsouhlasena žádným uživatelem v tenantovi. Je možné, že jste odeslali žádost o ověření na špatného tenanta. \ | (Pouze Linux) |
| 400 – Chybný požadavek | bad_request_102 | Není zadáno požadované záhlaví metadat. | Buď `Metadata` chybí pole s hlavičkou žádosti, nebo je nesprávně naformátované. Hodnota musí být zadána jako `true` , v malých případech. Příklad najdete v části "Ukázkový požadavek" v předchozím oddílu REST.|
| 401 – Neautorizováno | unknown_source | Neznámý zdroj *\<URI\>* | Ověřte, zda je správně naformátován identifikátor URI žádosti HTTP GET. `scheme:host/resource-path`Část musí být zadána jako `http://localhost:50342/oauth2/token` . Příklad najdete v části "Ukázkový požadavek" v předchozím oddílu REST.|
|           | invalid_request | V požadavku chybí povinný parametr, obsahuje neplatnou hodnotu parametru, obsahuje parametr více než jednou nebo je jinak poškozen. |  |
|           | unauthorized_client | Klient nemá autorizaci k vyžádání přístupového tokenu pomocí této metody. | Důvodem je požadavek, který nepoužil místní zpětnou smyčku k volání rozšíření nebo na virtuálním počítači, který nemá spravované identity pro prostředky Azure správně nakonfigurovány. Pokud potřebujete pomoc s konfigurací virtuálních počítačů, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md) . |
|           | access_denied | Vlastník prostředku nebo autorizační Server odepřel požadavek. |  |
|           | unsupported_response_type | Autorizační Server nepodporuje získání přístupového tokenu pomocí této metody. |  |
|           | invalid_scope | Požadovaný obor je neplatný, neznámý nebo poškozený. |  |
| Chyba „500 Internal server error“ | Neznámý | Nepovedlo se načíst token ze služby Active Directory. Podrobnosti najdete v tématu protokoly v *\<file path\>* | Ověřte, že na virtuálním počítači jsou povolené spravované identity pro prostředky Azure. Pokud potřebujete pomoc s konfigurací virtuálních počítačů, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md) .<br><br>Ověřte také, zda je identifikátor URI požadavku HTTP GET správně naformátovaný, zejména identifikátor URI prostředku zadaný v řetězci dotazu. V části "Ukázkový požadavek" v předchozí části REST najdete příklad nebo [služby Azure, které podporují ověřování Azure AD](./services-support-managed-identities.md) pro seznam služeb a jejich odpovídající ID prostředků.

## <a name="retry-guidance"></a>Pokyny pro opakování 

Doporučuje se opakovat pokus, pokud obdržíte kód chyby 404, 429 nebo 5xx (viz [zpracování chyb](#error-handling) výše).

Omezení omezování se vztahují na počet volání na koncový bod IMDS. Při překročení prahové hodnoty omezuje koncový bod IMDS všechny další požadavky, zatímco je omezení aktivní. Během této doby vrátí koncový bod IMDS stavový kód HTTP 429 (příliš mnoho požadavků) a požadavky selžou. 

Pro opakování doporučujeme následující strategii: 

| **Strategie opakování** | **Nastavení** | **Hodnoty** | **Jak to funguje** |
| --- | --- | --- | --- |
|ExponentialBackoff |Počet opakování<br />Min back-off<br />Max back-off<br />Delta back-off<br />První rychlé opakování |5<br />0 s<br />60 s<br />2 s<br />false (nepravda) |Pokus 1 – zpoždění 0 s<br />Pokus 2 – zpoždění ~2 s<br />Pokus 3 – zpoždění ~6 s<br />Pokus 4 – zpoždění ~14 s<br />Pokus 5 – zpoždění ~30 s |

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure

V tématu [služby Azure, které podporují ověřování Azure AD](./services-support-managed-identities.md) , najdete seznam prostředků, které podporují Azure AD a byly testovány se spravovanými identitami pro prostředky Azure a jejich příslušnými ID prostředků.


## <a name="next-steps"></a>Další kroky

- Pokud chcete povolit spravované identity pro prostředky Azure na virtuálním počítači Azure, přečtěte si téma [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí Azure Portal](qs-configure-portal-windows-vm.md).