---
title: 'Podepsat požadavek HTTP pomocí jazyka C #'
description: V tomto kurzu se vysvětluje verze jazyka C# podepisování požadavku HTTP s podpisem HMAC pro komunikační služby Azure.
author: alexandra142
manager: soricos
services: azure-communication-services
ms.author: apistrak
ms.date: 03/10/2021
ms.topic: include
ms.service: azure-communication-services
ms.openlocfilehash: 34c7df2b0e61536c0b5f0bc1e4a97d58d0d9c6a4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103490500"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nainstalujte [Visual Studio](https://visualstudio.microsoft.com/downloads/).
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku služby Azure Communication Services](../../quickstarts/create-communication-resource.md). Pro tento kurz budete muset zaznamenat své **resourceEndpoint** a **resourceAccessKey** .

## <a name="sign-an-http-request-with-c"></a>Podepsat požadavek HTTP pomocí jazyka C #

Přístup k ověřování pomocí klíče používá sdílený tajný klíč k vygenerování podpisu HMAC pro každý požadavek HTTP. Tento podpis se vygeneruje pomocí SHA256 algoritmu, který se pošle v `Authorization` hlavičce pomocí `HMAC-SHA256` schématu. Například:

```
Authorization: "HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature=<hmac-sha256-signature>"
```

`hmac-sha256-signature`Skládá se z těchto:

- Příkaz HTTP (například `GET` nebo `PUT` )
- Cesta požadavku HTTP
- Date (Datum)
- Hostitel
- x-MS-Content-SHA256

## <a name="setup"></a>Nastavení

Následující postup popisuje, jak vytvořit autorizační hlavičku.

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly, jako je například cmd, PowerShell nebo bash, `dotnet new` vytvořte pomocí příkazu novou konzolovou aplikaci s názvem `SignHmacTutorial` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program. cs**.

```console
dotnet new console -o SignHmacTutorial
```

Změňte adresář na nově vytvořenou složku aplikace. `dotnet build`K zkompilování aplikace použijte příkaz.

```console
cd SignHmacTutorial
dotnet build
```

## <a name="install-the-package"></a>Instalace balíčku

Nainstalujte balíček `Newtonsoft.Json` , který se používá pro serializaci těla.

```console
dotnet add package Newtonsoft.Json
```

Aktualizujte `Main` deklaraci metody pro podporu asynchronního kódu. Začněte tím, že použijete následující kód.

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
            // Tutorial code goes here.
        }
    }
}

```

## <a name="create-a-request-message"></a>Vytvořit zprávu požadavku

V tomto příkladu pošleme požadavek na vytvoření nové identity pomocí rozhraní API pro ověřování komunikačních služeb (verze `2021-03-07` ).

Do metody `Main` přidejte následující kód.

```csharp
string resourceEndpoint = "resourceEndpoint";
//Create a uri you are going to call.
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

## <a name="create-a-content-hash"></a>Vytvořit hodnotu hash obsahu

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

Nahraďte `resourceAccessKey` přístupovým klíčem ke skutečnému prostředku komunikační služby.

## <a name="create-an-authorization-header-string"></a>Vytvoří řetězec autorizační hlavičky.

Nyní vytvoříme řetězec, který přidáme do hlavičky autorizace.

1. Vypočítá hodnotu hash obsahu.
1. Zadejte časové razítko koordinovaného univerzálního času (UTC).
1. Připravte řetězec, který se má podepsat.
1. Vypočítá signaturu.
1. Zřetězí řetězec, který se použije v autorizační hlavičce.
 
Do metody `Main` přidejte následující kód.

```csharp
// Compute a content hash.
var contentHash = ComputeContentHash(serializedBody);
//Specify the Coordinated Universal Time (UTC) timestamp.
var date = DateTimeOffset.UtcNow.ToString("r", CultureInfo.InvariantCulture);
//Prepare a string to sign.
var stringToSign = $"POST\n{requestUri.PathAndQuery}\n{date};{requestUri.Authority};{contentHash}";
//Compute the signature.
var signature = ComputeSignature(stringToSign);
//Concatenate the string, which will be used in the authorization header.
var authorizationHeader = $"HMAC-SHA256 SignedHeaders=date;host;x-ms-content-sha256&Signature={signature}";
```

## <a name="add-headers-to-requestmessage"></a>Přidat záhlaví do requestMessage

Použijte následující kód k přidání požadovaných hlaviček do `requestMessage` .

```csharp
//Add a content hash header.
requestMessage.Headers.Add("x-ms-content-sha256", contentHash);
//Add a date header.
requestMessage.Headers.Add("Date", date);
//Add an authorization header.
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
