---
title: Použití spravovaných identit na virtuálním počítači k získání přístupového tokenu – Azure AD
description: Krok za krokem pokyny a příklady pro použití spravovaných identit pro prostředky Azure na virtuálních počítačích získat přístupový token OAuth.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a58103bad3914bd0c0c6e70f8e3d2882271e1070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049194"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Jak používat spravované identity pro prostředky Azure na virtuálním počítači Azure k získání přístupového tokenu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Tuto identitu můžete použít k ověření na libovolnou službu, která podporuje ověřování Azure AD, bez pověření ve vašem kódu. 

Tento článek obsahuje různé příklady kódu a skriptu pro získávání tokenů, stejně jako pokyny k důležitým tématům, jako je zpracování vypršení platnosti tokenu a chyb protokolu HTTP. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Pokud máte v plánu použít příklady Azure PowerShellu v tomto článku, nainstalujte nejnovější verzi [Azure PowerShellu](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Všechny ukázkový kód nebo skript v tomto článku předpokládá, že klient běží na virtuálním počítači se spravovanými identitami pro prostředky Azure. Pomocí funkce "Připojit" virtuálního počítače na webu Azure Portal můžete vzdáleně připojit k virtuálnímu počítači. Podrobnosti o povolení spravovaných identit pro prostředky Azure na virtuálním počítači najdete [v tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure Portal](qs-configure-portal-windows-vm.md)nebo jednoho z článků variant (pomocí PowerShellu, CLI, šablony nebo Sady Azure SDK). 

> [!IMPORTANT]
> - Hranice zabezpečení spravovaných identit pro prostředky Azure je prostředek, na který se používá. Všechny kódy nebo skripty spuštěné na virtuálním počítači můžou požadovat a načítat tokeny pro všechny spravované identity, které jsou na něm dostupné. 

## <a name="overview"></a>Přehled

Klientská aplikace může požádat o spravované identity pro [přístupový token pouze pro aplikace](../develop/developer-glossary.md#access-token) Azure pro přístup k danému prostředku. Token je [založen na spravovaných identitách pro instanční objekt služby prostředků Azure](overview.md#how-does-the-managed-identities-for-azure-resources-work). Jako takové není nutné, aby se klient sám registroval, aby získal přístupový token pod vlastním instančním objektem. Token je vhodný pro použití jako nosný token ve volání služby ke [službě, která vyžadují pověření klienta](../develop/v2-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [Získání tokenu pomocí protokolu HTTP](#get-a-token-using-http) | Podrobnosti protokolu pro spravované identity pro koncový bod tokenu prostředků Azure |
| [Získání tokenu pomocí knihovny Microsoft.Azure.Services.AppAuthentication pro rozhraní .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Příklad použití knihovny Microsoft.Azure.Services.AppAuthentication z klienta .NET
| [Získání tokenu pomocí C #](#get-a-token-using-c) | Příklad použití spravovaných identit pro koncové hody prostředků Azure rest z klienta C# |
| [Získání tokenu pomocí Javy](#get-a-token-using-java) | Příklad použití spravovaných identit pro koncové místo Azure z klienta Jazyka Java |
| [Získání tokenu pomocí funkce Go](#get-a-token-using-go) | Příklad použití spravovaných identit pro koncové místo azure prostředků REST z klienta Go |
| [Získání tokenu pomocí Azure PowerShellu](#get-a-token-using-azure-powershell) | Příklad použití spravovaných identit pro koncové místo Azure z klienta PowerShellu |
| [Získání tokenu pomocí CURL](#get-a-token-using-curl) | Příklad použití spravovaných identit pro koncové místo Azure prostředků REST z klienta Bash/CURL |
| Zpracování ukládání tokenů do mezipaměti | Pokyny pro zpracování přístupových tokenů s prošlou platností |
| [Zpracování chyb](#error-handling) | Pokyny pro zpracování chyb PROTOKOLU HTTP vrácených ze spravovaných identit pro koncový bod tokenu prostředků Azure |
| [ID prostředků pro služby Azure](#resource-ids-for-azure-services) | Kde získat ID prostředků pro podporované služby Azure |

## <a name="get-a-token-using-http"></a>Získání tokenu pomocí protokolu HTTP 

Základní rozhraní pro získání přístupového tokenu je založeno na REST, takže je přístupné pro všechny klientské aplikace spuštěné na virtuálním počítači, který může provádět volání HTTP REST. To je podobné programovacímodel Azure AD, s výjimkou klient používá koncový bod na virtuálním počítači (vs koncový bod Azure AD).

Ukázkový požadavek pomocí koncového bodu služby Azure Instanci (IMDS) *(doporučeno):*

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Sloveso HTTP označující, že chcete načíst data z koncového bodu. V tomto případě přístupový token OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | Spravované identity pro koncový bod prostředků Azure pro službu metadat instance. |
| `api-version`  | Parametr řetězce dotazu označující verzi rozhraní API pro koncový bod IMDS. Použijte verzi `2018-02-01` rozhraní API nebo vyšší. |
| `resource` | Parametr řetězce dotazu označující identifikátor URI ID aplikace cílového prostředku. Zobrazí se také `aud` v deklaraci (publika) vydaného tokenu. Tento příklad požaduje token pro přístup ke Správci prostředků Azure, který má identifikátor URI ID aplikace `https://management.azure.com/`. |
| `Metadata` | Pole hlavičky požadavku HTTP, které vyžadují spravované identity pro prostředky Azure jako zmírnění útoku na vyžádání na straně serveru (SSRF). Tato hodnota musí být nastavena na "true", ve všech malá písmena. |
| `object_id` | (Nepovinné) Parametr řetězce dotazu označující object_id spravované identity, pro kterou chcete token. Povinné, pokud váš virtuální počítač má více uživatelem přiřazené spravované identity.|
| `client_id` | (Nepovinné) Parametr řetězce dotazu označující client_id spravované identity, pro kterou chcete token. Povinné, pokud váš virtuální počítač má více uživatelem přiřazené spravované identity.|
| `mi_res_id` | (Nepovinné) Parametr řetězce dotazu označující mi_res_id (ID prostředku Azure) spravované identity, pro kterou chcete token. Povinné, pokud váš virtuální počítač má více uživatelem přiřazené spravované identity. |

Ukázkový požadavek pomocí spravovaných identit pro koncový bod rozšíření virtuálních zařízení Azure pro virtuální počítače Azure *(plánovaný pro vyřazení v lednu 2019):*

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Popis |
| ------- | ----------- |
| `GET` | Sloveso HTTP označující, že chcete načíst data z koncového bodu. V tomto případě přístupový token OAuth. | 
| `http://localhost:50342/oauth2/token` | Spravované identity pro koncový bod prostředků Azure, kde 50342 je výchozí port a je konfigurovatelný. |
| `resource` | Parametr řetězce dotazu označující identifikátor URI ID aplikace cílového prostředku. Zobrazí se také `aud` v deklaraci (publika) vydaného tokenu. Tento příklad požaduje token pro přístup ke Správci prostředků Azure, který má identifikátor URI ID aplikace `https://management.azure.com/`. |
| `Metadata` | Pole hlavičky požadavku HTTP, které vyžadují spravované identity pro prostředky Azure jako zmírnění útoku na vyžádání na straně serveru (SSRF). Tato hodnota musí být nastavena na "true", ve všech malá písmena.|
| `object_id` | (Nepovinné) Parametr řetězce dotazu označující object_id spravované identity, pro kterou chcete token. Povinné, pokud váš virtuální počítač má více uživatelem přiřazené spravované identity.|
| `client_id` | (Nepovinné) Parametr řetězce dotazu označující client_id spravované identity, pro kterou chcete token. Povinné, pokud váš virtuální počítač má více uživatelem přiřazené spravované identity.|

Odpověď vzorku:

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
| `access_token` | Požadovaný přístupový token. Při volání zabezpečené rozhraní REST API je `Authorization` token vložen do pole hlavičky požadavku jako token "nosiče", což umožňuje rozhraní API k ověření volajícího. | 
| `refresh_token` | Nepoužívají spravované identity pro prostředky Azure. |
| `expires_in` | Počet sekund, po které je přístupový token i nadále platný před vypršením platnosti, od doby vydání. Čas vydání lze nalézt v `iat` deklaraci tokenu. |
| `expires_on` | Časový rozsah při vypršení platnosti přístupového tokenu. Datum je reprezentováno jako počet sekund od "1970-01-01T0:0:0Z UTC" `exp` (odpovídá deklaraci tokenu). |
| `not_before` | Časový rozsah, kdy se projeví přístupový token a může být přijat. Datum je reprezentováno jako počet sekund od "1970-01-01T0:0:0Z UTC" `nbf` (odpovídá deklaraci tokenu). |
| `resource` | Prostředek, pro který byl požadován přístupový token, který odpovídá parametru `resource` řetězce dotazu požadavku. |
| `token_type` | Typ tokenu, což je přístupový token "Nosič", což znamená, že prostředek může poskytnout přístup k nositeli tohoto tokenu. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Získání tokenu pomocí knihovny Microsoft.Azure.Services.AppAuthentication pro rozhraní .NET

Pro aplikace a funkce rozhraní .NET je nejjednodušší způsob práce se spravovanými identitami pro prostředky Azure prostřednictvím balíčku Microsoft.Azure.Services.AppAuthentication. Tato knihovna vám také umožní otestovat kód místně ve vývojovém počítači pomocí uživatelského účtu z Visual Studia, [rozhraní příkazového příkazu Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)nebo integrovaného ověřování služby Active Directory. Další informace o možnostech místního vývoje v této knihovně najdete v [odkazu Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication). V této části se zobrazí, jak začít s knihovnou v kódu.

1. Přidejte do aplikace odkazy na balíčky [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet.

2.  Přidejte do aplikace následující kód:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Další informace o microsoft.azure.services.appauthentication a operacích, které zveřejňuje, najdete v [odkazu Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication) a [app service a KeyVault se spravovanými identitami pro prostředky Azure .NET ukázka](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Získání tokenu pomocí C #

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

## <a name="get-a-token-using-java"></a>Získání tokenu pomocí Javy

Pomocí této [knihovny JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) načtěte token pomocí javy.

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

## <a name="get-a-token-using-go"></a>Získání tokenu pomocí funkce Go

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

## <a name="get-a-token-using-azure-powershell"></a>Získání tokenu pomocí Azure PowerShellu

Následující příklad ukazuje, jak používat spravované identity pro koncové místo Azure rest z klienta Prostředí PowerShell na:

1. Získejte přístupový token.
2. Pomocí přístupového tokenu můžete volat rozhraní REST API Správce prostředků Azure a získat informace o virtuálním počítači. Nezapomeňte nahradit ID předplatného, název skupiny prostředků a `<SUBSCRIPTION-ID>` `<RESOURCE-GROUP>`název `<VM-NAME>`virtuálního počítače pro , a , v uvedeném pořadí.

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

## <a name="get-a-token-using-curl"></a>Získání tokenu pomocí CURL

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

Zatímco spravované identity pro podsystém prostředků Azure, který se používá (IMDS/spravované identity pro rozšíření virtuálních zařízení Azure) provádí tokeny mezipaměti, doporučujeme také implementovat ukládání tokenů do mezipaměti ve vašem kódu. V důsledku toho byste se měli připravit na scénáře, kde prostředek označuje, že platnost tokenu vypršela. 

On-the-wire volání do Azure AD výsledek pouze v případě, že:
- K chybě mezipaměti dojde kvůli žádnému tokenu ve spravovaných identitách pro mezipaměť podsystému prostředků Azure
- Platnost tokenu uloženého v mezipaměti vypršela

## <a name="error-handling"></a>Zpracování chyb

Spravované identity pro koncový bod prostředků Azure signalizují chyby prostřednictvím pole stavového kódu v záhlaví zprávy odpovědi HTTP jako chyby 4xx nebo 5xx:

| Stavový kód | Důvod chyby | Jak zacházet |
| ----------- | ------------ | ------------- |
| 404 Nalezeno. | Koncový bod IMDS se aktualizuje. | Opakujte akci s expontentním backoffem. Viz pokyny níže. |
| 429 Příliš mnoho žádostí. |  Bylo dosaženo limitu škrticí klapky imds. | Opakujte akci s exponenciálním vypnutím. Viz pokyny níže. |
| 4xx Chyba v požadavku. | Jeden nebo více parametrů požadavku bylo nesprávné. | Neopakujte akci.  Další informace naleznete v podrobnostech o chybě.  4xx chyby jsou chyby návrhu.|
| 5xx Přechodná chyba ze služby. | Spravované identity pro podsystém prostředků Azure nebo službu Azure Active Directory vrátily přechodnou chybu. | Je bezpečné opakovat po čekání alespoň 1 sekundu.  Pokud opakujíte příliš rychle nebo příliš často, IMDS nebo Azure AD může vrátit chybu limit rychlosti (429).|
| timeout | Koncový bod IMDS se aktualizuje. | Opakujte akci s expontentním backoffem. Viz pokyny níže. |

Pokud dojde k chybě, odpovídající tělo odpovědi HTTP obsahuje JSON s podrobnostmi o chybě:

| Element | Popis |
| ------- | ----------- |
| error   | Identifikátor chyby. |
| error_description | Podrobný popis chyby. **Popisy chyb se mohou kdykoli změnit. Nepište kód, který větve na základě hodnot v popisu chyby.**|

### <a name="http-response-reference"></a>Odkaz na odpověď HTTP

V této části jsou uvedeny možné chybové odpovědi. Stav "200 OK" je úspěšná odpověď a přístupový token je obsažen v těle odpovědi JSON, v access_token prvku.

| Kód stavu | Chyba | Popis chyby | Řešení |
| ----------- | ----- | ----------------- | -------- |
| 400 Chybný požadavek | invalid_resource | AADSTS50001: Aplikace * \<s\> * názvem URI nebyla nalezena v tenantovi s názvem * \<TENANT-ID\>*. K tomu může dojít, pokud aplikace nebyla nainstalována správcem klienta nebo s nikterou souhlasnou byla přijata libovolnýuživatel v tenantovi. Je možné, že jste odeslali požadavek na ověření nesprávnému klientovi.\ | (pouze Linux) |
| 400 Chybný požadavek | bad_request_102 | Není zadáno povinné záhlaví metadat. | V `Metadata` požadavku chybí buď pole hlavičky požadavku, nebo je nesprávně formátováno. Hodnota musí být `true`zadána jako , ve všech malá písmena. Viz "Ukázkový požadavek" v předchozí části REST pro příklad.|
| 401 Neautorizováno | unknown_source | Identifikátor * \<URI neznámého zdroje\>* | Ověřte, zda je identifikátor URI požadavku HTTP GET správně formátován. Část `scheme:host/resource-path` musí být `http://localhost:50342/oauth2/token`zadána jako . Viz "Ukázkový požadavek" v předchozí části REST pro příklad.|
|           | invalid_request | Požadavek chybí požadovaný parametr, obsahuje neplatnou hodnotu parametru, obsahuje parametr více než jednou nebo je jinak poškozen. |  |
|           | unauthorized_client | Klient není oprávněn požadovat přístupový token pomocí této metody. | Způsobené požadavek, který nepoužíval místní zpětné smyčky k volání rozšíření nebo na virtuálním počítači, který nemá spravované identity pro prostředky Azure nakonfigurované správně. Pokud potřebujete pomoc s konfigurací virtuálního počítače, přečtěte si název [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure Portal.](qs-configure-portal-windows-vm.md) |
|           | access_denied | Vlastník prostředku nebo autorizační server požadavek zamítl. |  |
|           | unsupported_response_type | Autorizační server nepodporuje získání přístupového tokenu pomocí této metody. |  |
|           | invalid_scope | Požadovaný obor je neplatný, neznámý nebo poškozený. |  |
| Chyba interního serveru 500 | Neznámý | Načtení tokenu ze služby Active Directory se nezdařilo. Podrobnosti viz protokoly v * \<cestě k souboru\>* | Ověřte, že spravované identity pro prostředky Azure byly povolené na virtuálním počítači. Pokud potřebujete pomoc s konfigurací virtuálního počítače, přečtěte si název [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure Portal.](qs-configure-portal-windows-vm.md)<br><br>Ověřte také, zda je identifikátor URI požadavku HTTP GET správně formátován, zejména identifikátor URI prostředku zadaný v řetězci dotazu. Podívejte se na "Ukázkový požadavek" v předchozí části REST pro příklad nebo [služby Azure, které podporují ověřování Azure AD](services-support-msi.md) pro seznam služeb a jejich příslušných ID prostředků.

## <a name="retry-guidance"></a>Pokyny pro opakování 

Pokud se zobrazí chybový kód 404, 429 nebo 5xx, doporučujeme to opakovat (viz [Zpracování chyb](#error-handling) výše).

Omezení omezení omezení platí pro počet volání do koncového bodu IMDS. Při překročení prahové hodnoty omezení koncový bod IMDS omezuje všechny další požadavky, zatímco omezení je v platnosti. Během tohoto období koncový bod IMDS vrátí stavový kód HTTP 429 ("Příliš mnoho požadavků") a požadavky se nezdaří. 

Pro opakování doporučujeme následující strategii: 

| **Strategie opakování** | **Nastavení** | **Hodnoty** | **Jak to funguje** |
| --- | --- | --- | --- |
|ExponentialBackoff |Počet opakování<br />Min back-off<br />Max back-off<br />Delta back-off<br />První rychlé opakování |5<br />0 s<br />60 s<br />2 s<br />false (nepravda) |Pokus 1 – zpoždění 0 s<br />Pokus 2 – zpoždění ~2 s<br />Pokus 3 – zpoždění ~6 s<br />Pokus 4 – zpoždění ~14 s<br />Pokus 5 – zpoždění ~30 s |

## <a name="resource-ids-for-azure-services"></a>ID prostředků pro služby Azure

Podívejte se na [služby Azure, které podporují ověřování Azure AD](services-support-msi.md) pro seznam prostředků, které podporují Azure AD a byly testovány se spravovanými identitami pro prostředky Azure a jejich příslušná ID prostředků.


## <a name="next-steps"></a>Další kroky

- Pokud chcete povolit spravované identity pro prostředky Azure na virtuálním počítači Azure, přečtěte si témat [u tématu Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači pomocí portálu Azure](qs-configure-portal-windows-vm.md).








