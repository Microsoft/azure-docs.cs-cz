---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 4a3f38e1423db0755d8c76f8850e41173d250f43
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33904978"
---
### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Pokud jste při vytváření služby Relay zakázali možnost Vyžaduje autorizaci klientů, můžete odeslat žádosti na adresu URL hybridních připojení pomocí libovolného prohlížeče. Pro přístup k chráněným koncovým bodům je nutné vytvořit a předat token v hlavičce `ServiceBusAuthorization`, která se tady zobrazí.

V sadě Visual Studio vytvořte nový projekt **Konzolová aplikace (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Přidání balíčku NuGet služby Relay

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
2. Vyberte **Procházet** a vyhledejte **Microsoft.Azure.Relay**. Ve výsledcích hledání vyberte **Microsoft Azure Relay**. 
3. Vyberte **Nainstalovat** a dokončete instalaci. Zavřete dialogové okno.

### <a name="write-code-to-send-requests"></a>Zápis kódu pro odesílání žádostí

1. Na začátku souboru Program.cs nahraďte existující příkazy `using` následujícími příkazy `using`:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    ```
2. Do třídy `Program` přidejte konstanty s podrobnostmi o hybridním připojení. Zástupné symboly v závorkách nahraďte hodnotami, které jste získali při vytváření hybridního připojení. Nezapomeňte použít plně kvalifikovaný obor názvů.
   
    ```csharp
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
    private const string ConnectionName = "{HybridConnectionName}";
    private const string KeyName = "{SASKeyName}";
    private const string Key = "{SASKey}";
    ```
3. Do třídy `Program` přidejte následující metodu:
   
    ```csharp
    private static async Task RunAsync()
    {
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
        var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
        var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
        var client = new HttpClient();
        var request = new HttpRequestMessage()
        {
            RequestUri = uri,
            Method = HttpMethod.Get,
        };
        request.Headers.Add("ServiceBusAuthorization", token);
        var response = await client.SendAsync(request);
        Console.WriteLine(await response.Content.ReadAsStringAsync());
    }
    ```
4. Ve třídě `Program` přidejte do metody `Main` následující řádek kódu.
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Soubor Program.cs by měl vypadat nějak takto:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
   
    namespace Client
    {
        class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
               var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                KeyName, Key);
                var uri = new Uri(string.Format("https://{0}/{1}", RelayNamespace, ConnectionName));
                var token = (await tokenProvider.GetTokenAsync(uri.AbsoluteUri, TimeSpan.FromHours(1))).TokenString;
                var client = new HttpClient();
                var request = new HttpRequestMessage()
                {
                    RequestUri = uri,
                    Method = HttpMethod.Get,
                };
                request.Headers.Add("ServiceBusAuthorization", token);
                var response = await client.SendAsync(request);
                Console.WriteLine(await response.Content.ReadAsStringAsync());
            }
        }
    }
    ```

