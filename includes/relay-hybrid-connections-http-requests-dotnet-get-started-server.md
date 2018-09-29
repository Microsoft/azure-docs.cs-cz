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
ms.openlocfilehash: 5c7c2fe101315959d07ce4912905bbf59a7ee664
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452801"
---
### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

V sadě Visual Studio vytvořte nový projekt **Konzolová aplikace (.NET Framework)**.

### <a name="add-the-relay-nuget-package"></a>Přidání balíčku NuGet služby Relay

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
2. Vyberte možnost **Zahrnout předběžné verze**. 
3. Vyberte **Procházet** a vyhledejte **Microsoft.Azure.Relay**. Ve výsledcích hledání vyberte **Microsoft Azure Relay**.
4. Jako verzi vyberte **2.0.0-preview1-20180523**. 
5. Vyberte **Nainstalovat** a dokončete instalaci. Zavřete dialogové okno.

### <a name="write-code-to-receive-messages"></a>Napsání kódu pro přijímání zpráv

1. Na začátku souboru Program.cs nahraďte existující příkazy `using` následujícími příkazy `using`:
   
    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.Relay;
    using System.Net;
    ```
2. Do třídy `Program` přidejte konstanty s podrobnostmi o hybridním připojení. Zástupné symboly v závorkách nahraďte hodnotami, které jste získali při vytváření hybridního připojení. Nezapomeňte použít plně kvalifikovaný obor názvů.
   
    ```csharp
    // replace {RelayNamespace} with the name of your namespace
    private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";

    // replace {HybridConnectionName} with the name of your hybrid connection
    private const string ConnectionName = "{HybridConnectionName}";

    // replace {SAKKeyName} with the name of your Shared Access Policies key, which is RootManageSharedAccessKey by default
    private const string KeyName = "{SASKeyName}";

    // replace {SASKey} with the primary key of the namespace you saved earlier
    private const string Key = "{SASKey}";
    ```

3. Do třídy `Program` přidejte metodu `RunAsync`:
   
    ```csharp
    private static async Task RunAsync()
    {
        var cts = new CancellationTokenSource();
   
        var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
        var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
   
        // Subscribe to the status events.
        listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
        listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
        listener.Online += (o, e) => { Console.WriteLine("Online"); };

        // Provide an HTTP request handler
        listener.RequestHandler = (context) =>
        {
            // Do something with context.Request.Url, HttpMethod, Headers, InputStream...
            context.Response.StatusCode = HttpStatusCode.OK;
            context.Response.StatusDescription = "OK, This is pretty neat";
            using (var sw = new StreamWriter(context.Response.OutputStream))
            {
                sw.WriteLine("hello!");
            }
            
            // The context MUST be closed here
            context.Response.Close();
        };
            
        // Opening the listener establishes the control channel to
        // the Azure Relay service. The control channel is continuously 
        // maintained, and is reestablished when connectivity is disrupted.
        await listener.OpenAsync();
        Console.WriteLine("Server listening");
    
        // Start a new thread that will continuously read the console.
        await Console.In.ReadLineAsync();
        
        // Close the listener after you exit the processing loop.
        await listener.CloseAsync();
    }
    ```
5. Do metody `Main` ve třídě `Program` přidejte následující řádek kódu:
   
    ```csharp
    RunAsync().GetAwaiter().GetResult();
    ```
   
    Hotový soubor Program.cs by měl vypadat nějak takto:
   
    ```csharp
    namespace Server
    {
        using System;
        using System.IO;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.Relay;
   
        public class Program
        {
            private const string RelayNamespace = "{RelayNamespace}.servicebus.windows.net";
            private const string ConnectionName = "{HybridConnectionName}";
            private const string KeyName = "{SASKeyName}";
            private const string Key = "{SASKey}";
   
            public static void Main(string[] args)
            {
                RunAsync().GetAwaiter().GetResult();
            }
   
            private static async Task RunAsync()
            {
                var tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(KeyName, Key);
                var listener = new HybridConnectionListener(new Uri(string.Format("sb://{0}/{1}", RelayNamespace, ConnectionName)), tokenProvider);
           
                // Subscribe to the status events.
                listener.Connecting += (o, e) => { Console.WriteLine("Connecting"); };
                listener.Offline += (o, e) => { Console.WriteLine("Offline"); };
                listener.Online += (o, e) => { Console.WriteLine("Online"); };
        
                // Provide an HTTP request handler
                listener.RequestHandler = (context) =>
                {
                    // Do something with context.Request.Url, HttpMethod, Headers, InputStream...
                    context.Response.StatusCode = HttpStatusCode.OK;
                    context.Response.StatusDescription = "OK";
                    using (var sw = new StreamWriter(context.Response.OutputStream))
                    {
                        sw.WriteLine("hello!");
                    }
                    
                    // The context MUST be closed here
                    context.Response.Close();
                };
           
                // Opening the listener establishes the control channel to
                // the Azure Relay service. The control channel is continuously 
                // maintained, and is reestablished when connectivity is disrupted.
                await listener.OpenAsync();
                Console.WriteLine("Server listening");
           
                // Start a new thread that will continuously read the console.
                await Console.In.ReadLineAsync();
               
                // Close the listener after you exit the processing loop.
                await listener.CloseAsync();
            }
        }
    }
    ```

