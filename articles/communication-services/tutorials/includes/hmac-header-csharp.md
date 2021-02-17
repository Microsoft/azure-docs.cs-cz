---
title: 'Podepsat požadavek HTTP pomocí jazyka C #'
description: Toto je verze jazyka C# podepisování požadavku HTTP s podpisem HMAC pro komunikační služby.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 01/15/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 3c1b56f81e5164bbdfa94fdaeca5f5f1f55b3b51
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100551634"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:
- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nainstalovat [Visual Studio](https://visualstudio.microsoft.com/downloads/) 
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../quickstarts/create-communication-resource.md). Pro tento kurz budete muset zaznamenat své **resourceEndpoint** a  **resourceAccessKey** .



## <a name="sign-an-http-request-with-c"></a>Podepsat požadavek HTTP pomocí jazyka C #
Přístup k ověřování pomocí klíče používá sdílený tajný klíč k vygenerování podpisu HMAC pro každý požadavek HTTP. Tento podpis se vygeneruje pomocí SHA256 algoritmu, který se pošle v `Authorization` hlavičce pomocí `HMAC-SHA256` schématu. Příklad:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature`Skládá se z těchto: 

- Příkaz HTTP (např. `GET` nebo `PUT` )
- Cesta požadavku HTTP
- Date (Datum)
- Hostitel
- x-MS-Content-SHA256

## <a name="setting-up"></a>Nastavení
Následující postup popisuje, jak vytvořit autorizační hlavičku:

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `SignHmacTutorial` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program.cs**.

```console
dotnet new console -o SignHmacTutorial
```

Změňte adresář na nově vytvořenou složku aplikace a použijte `dotnet build` příkaz pro zkompilování aplikace.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Instalace balíčku

Nainstalujte balíček `Newtonsoft.Json` , který se používá pro serializaci těla:

```console
dotnet add package Newtonsoft.Json
```

Aktualizujte `Main` deklaraci metody pro podporu asynchronního kódu. Pro začátek použijte následující kód:

```csharp
using System;
using System.Globalization;
using System.Net.Http;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json;
namespace SignHmacTutorial
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Sign an HTTP request Tutorial");
            // Tutorial code goes here
        }
    }
}

```
## <a name="create-a-request-message"></a>Vytvořit zprávu požadavku

V tomto příkladu pošleme žádost o vytvoření nové identity pomocí rozhraní API pro ověřování komunikačních služeb (verze `2021-03-07` ).

Do metody přidejte následující kód `Main` :

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create an uri you are going to call
var requestUri = new Uri($"{resourceEndpoint}/identities?api-version=2021-03-07");
//Endpoint identities?api-version=2021-03-07 accepts list of scopes as a body
var body = new[] { "chat" }; 
var serializedBody = JsonConvert.SerializeObject(body);
var requestMessage = new HttpRequestMessage(HttpMethod.Post, requestUri)
{
    Content = new StringContent(serializedBody, Encoding.UTF8)
};
```

Nahraďte `resourceEndpoint` hodnotou reálného koncového bodu prostředku.

## <a name="create-content-hash"></a>Vytvořit hodnotu hash obsahu

Hodnota hash obsahu je součástí podpisu HMAC. Použijte následující kód k výpočtu hodnoty hash obsahu. Tuto metodu můžete přidat do `Progam.cs` `Main` metody.

```csharp
static string ComputeContentHash(string content)
{
    using (var sha256 = SHA256.Create())
    {
        byte[] hashedBytes = sha256.ComputeHash(Encoding.UTF8.GetBytes(content));
        return Convert.ToBase64String(hashedBytes);
    }
}
```

## <a name="compute-a-signature"></a>Vypočítat podpis
Pomocí následujícího kódu vytvořte metodu pro výpočet vašeho podpisu HMAC.

```csharp
 static string ComputeSignature(string stringToSign)
{
    string secret = "resourceAccessKey";
    using (var hmacsha256 = new HMACSHA256(Convert.FromBase64String(secret)))
    {
        var bytes = Encoding.ASCII.GetBytes(stringToSign);
        var hashedBytes = hmacsha256.ComputeHash(bytes);
        return Convert.ToBase64String(hashedBytes);
    }
}
```

Nahraďte `resourceAccessKey` přístupovým klíčem skutečného prostředku služby Azure Communication Services.

## <a name="create-an-authorization-header-string"></a>Vytvoří řetězec autorizační hlavičky.

Nyní vytvoříme řetězec, který přidáme do hlavičky autorizace:

1. Vypočítat hodnotu hash obsahu
2. Zadejte časové razítko koordinovaného univerzálního času (UTC).
3. Příprava řetězce k podepsání
4. Vypočítat signaturu
5. Zřetězí řetězec, který se použije v autorizační hlavičce.
 
Do metody přidejte následující kód `Main` :

```csharp
// Compute a content hash
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in authorization header
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Přidat záhlaví do requestMessage

Použijte následující kód k přidání požadovaných hlaviček do `requestMessage` :

```csharp
//Add content hash header
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//add date header
requestMessage.Headers.Add("Date", date);
//add Authorization header
requestMessage.Headers.Add("Authorization", authorizationHeader);
```

## <a name="test-the-client"></a>Test klienta
Zavolejte koncový bod pomocí `HttpClient` a ověřte odpověď.

```csharp
HttpClient httpClient = new HttpClient
{
    BaseAddress = requestUri
};
var response = await httpClient.SendAsync(requestMessage);
var responseString = await response.Content.ReadAsStringAsync();
Console.WriteLine(responseString);
```
