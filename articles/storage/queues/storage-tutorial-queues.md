---
title: Kurz – práce s frontami Azure Storage – Azure Storage
description: V tomto kurzu se naučíte, jak pomocí Služba front Azure vytvořit fronty a vkládat, získávat a odstraňovat zprávy.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "75968200"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Kurz: práce s frontami úložiště Azure

Služba Azure Queue Storage implementuje cloudové fronty a umožňuje komunikaci mezi komponentami distribuované aplikace. Každá fronta uchovává seznam zpráv, které může přidat komponenta odesílatel a kterou zpracovávají komponenty přijímače. Ve frontě se aplikace může škálovat okamžitě, aby splňovala požadavky. Tento článek popisuje základní kroky pro práci s frontou služby Azure Storage.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření účtu úložiště Azure
> - Vytvoření aplikace
> - Přidat podporu pro asynchronní kód
> - Vytvoření fronty
> - Vložení zpráv do fronty
> - Vyřadit zprávy z fronty
> - Odstraní prázdnou frontu.
> - Vyhledat argumenty příkazového řádku
> - Sestavení a spuštění aplikace

## <a name="prerequisites"></a>Požadavky

- Získejte bezplatnou kopii [Visual Studio Codeového](https://code.visualstudio.com/download) editoru pro různé platformy.
- Stáhněte a nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).
- Pokud nemáte aktuální předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Nejdřív vytvořte účet úložiště Azure. Podrobný průvodce vytvořením účtu úložiště najdete v rychlém startu [Vytvoření účtu úložiště](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) .

## <a name="create-the-app"></a>Vytvoření aplikace

Vytvořte aplikaci .NET Core s názvem **QueueApp**. Pro zjednodušení bude tato aplikace odesílat i přijímat zprávy přes frontu.

1. V okně konzoly (například CMD, PowerShell nebo Azure CLI) pomocí `dotnet new` příkazu vytvořte novou konzolovou aplikaci s názvem **QueueApp**. Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Přepněte na nově vytvořenou složku **QueueApp** a sestavte aplikaci, abyste ověřili, jestli je vše v dobrém.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Mělo by se zobrazit výsledky podobné následujícímu:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.62 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp2.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

## <a name="add-support-for-asynchronous-code"></a>Přidat podporu pro asynchronní kód

Vzhledem k tomu, že aplikace používá cloudové prostředky, kód se spouští asynchronně. **Asynchronní** a **očekávána** klíčová slova v jazyce c# však neexistují v **hlavních** metodách až do c# 7,1. Můžete snadno přepnout na tento kompilátor prostřednictvím příznaku v souboru **csproj** .

1. Z příkazového řádku v adresáři projektu zadejte `code .` pro otevření Visual Studio Code v aktuálním adresáři. Nechte okno příkazového řádku otevřené. Pro pozdější spuštění bude k dispozici více příkazů. Pokud budete vyzváni k přidání prostředků C# potřebných pro sestavení a ladění, klikněte na tlačítko **Ano** .

2. Otevřete v editoru soubor **QueueApp.csproj**.

3. Přidejte `<LangVersion>7.1</LangVersion>` do první skupiny **vlastností** v souboru sestavení. Ujistěte se, že jste přidali značku **langversion –** jenom v případě, že váš klíč **targetFramework** se může lišit v závislosti na tom, jakou verzi rozhraní .NET máte nainstalovanou.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Uložte soubor **QueueApp. csproj** .

5. Otevřete zdrojový soubor **program.cs** a aktualizujte metodu **Main** tak, aby běžela asynchronně. Nahraďte **void** hodnotou návratové hodnoty **asynchronní úlohy** .

   ```csharp
   static async Task Main(string[] args)
   ```

6. Uložte soubor **program.cs** .

## <a name="create-a-queue"></a>Vytvoření fronty

1. Pomocí `dotnet add package` příkazu nainstalujte do projektu balíčky **Microsoft. Azure. Storage. Common** a **Microsoft. Azure. Storage. Queue** . Spusťte následující příkazy dotnet ze složky projektu v okně konzoly.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. V horní části souboru **program.cs** přidejte následující obory názvů ihned po `using System;` příkazu. Tato aplikace používá typy z těchto oborů názvů pro připojení k Azure Storage a práci s frontami.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Uložte soubor **program.cs** .

### <a name="get-your-connection-string"></a>Získání připojovacího řetězce

Klientská knihovna používá k navázání připojení připojovací řetězec. Připojovací řetězec je k dispozici v části **Nastavení** vašeho účtu úložiště v Azure Portal.

1. Ve webovém prohlížeči se přihlaste k [Azure Portal](https://portal.azure.com/).

2. Na webu Azure Portal přejděte na svůj účet úložiště.

3. Vyberte **přístupové klíče**.

4. Klikněte na tlačítko **Kopírovat** napravo od pole **připojovací řetězec** .

![Připojovací řetězec](media/storage-tutorial-queues/get-connection-string.png)

Připojovací řetězec má tento formát:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Přidání připojovacího řetězce do aplikace

Přidejte připojovací řetězec do aplikace, aby mohl získat přístup k účtu úložiště.

1. Přepněte zpátky na Visual Studio Code.

2. Do třídy **program** přidejte `private const string connectionString =` člena pro uložení připojovacího řetězce.

3. Za znaménko rovná se vložte hodnota řetězce, kterou jste zkopírovali dříve v Azure Portal. Hodnota **ConnectionString** bude pro váš účet jedinečná.

4. Odeberte kód "Hello World" z **Main**. Váš kód by měl vypadat podobně jako následující, ale s jedinečnou hodnotou připojovacího řetězce.

   ```csharp
   namespace QueueApp
   {
       class Program
       {
           private const string connectionString = "DefaultEndpointsProtocol=https; ...";

           static async Task Main(string[] args)
           {
           }
       }
   }
   ```

5. Pokud chcete vytvořit objekt **CloudQueue** , který se později předává do metod Send a Receive, aktualizujte **Main** .

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Uložte soubor.

## <a name="insert-messages-into-the-queue"></a>Vložit zprávy do fronty

Vytvořte novou metodu pro odeslání zprávy do fronty. Přidejte následující metodu do třídy **programu** . Tato metoda získá odkaz na frontu a pak vytvoří novou frontu, pokud ještě neexistuje, voláním [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Poté přidá zprávu do fronty voláním [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Do třídy **programu** přidejte následující metodu **SendMessageAsync** .

   ```csharp
   static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
   {
       bool createdQueue = await theQueue.CreateIfNotExistsAsync();

       if (createdQueue)
       {
           Console.WriteLine("The queue was created.");
       }

       CloudQueueMessage message = new CloudQueueMessage(newMessage);
       await theQueue.AddMessageAsync(message);
   }
   ```

2. Uložte soubor.

Zpráva musí být ve formátu, který může být součástí požadavku XML s kódováním UTF-8 a může mít velikost až 64 KB. Pokud zpráva obsahuje binární data, doporučujeme, abyste zprávu zakódovat ve formátu base64.

Ve výchozím nastavení je maximální hodnota TTL (Time-to-Live) pro zprávu nastavená na 7 dní. Můžete zadat libovolné kladné číslo pro hodnotu TTL (Time to Live). Chcete-li přidat zprávu, jejíž platnost nevyprší, `Timespan.FromSeconds(-1)` použijte ve volání **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Vyřadit zprávy z fronty

Vytvořte novou metodu s názvem **ReceiveMessageAsync**. Tato metoda přijímá zprávu z fronty voláním [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Po úspěšném přijetí zprávy je důležité ji odstranit z fronty, takže nebude zpracována více než jednou. Po přijetí zprávy ji vymažte z fronty voláním [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Do třídy **programu** přidejte následující metodu **ReceiveMessageAsync** .

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
       }
   }
   ```

2. Uložte soubor.

## <a name="delete-an-empty-queue"></a>Odstraní prázdnou frontu.

Je to osvědčený postup na konci projektu k určení, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které necháte běžet, vás můžou stát peníze. Pokud fronta existuje, ale je prázdná, požádejte uživatele, jestli ho chce odstranit.

1. Rozbalte metodu **ReceiveMessageAsync** pro zahrnutí výzvy k odstranění prázdné fronty.

   ```csharp
   static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
   {
       bool exists = await theQueue.ExistsAsync();

       if (exists)
       {
           CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

           if (retrievedMessage != null)
           {
               string theMessage = retrievedMessage.AsString;
               await theQueue.DeleteMessageAsync(retrievedMessage);
               return theMessage;
           }
           else
           {
               Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
               string response = Console.ReadLine();

               if (response == "Y" || response == "y")
               {
                   await theQueue.DeleteIfExistsAsync();
                   return "The queue was deleted.";
               }
               else
               {
                   return "The queue was not deleted.";
               }
           }
       }
       else
       {
           return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
       }
   }
   ```

2. Uložte soubor.

## <a name="check-for-command-line-arguments"></a>Vyhledat argumenty příkazového řádku

Pokud se do aplikace přenesou nějaké argumenty příkazového řádku, předpokládá se, že se jedná o zprávu, která se přidá do fronty. Spojit argumenty dohromady a vytvořit řetězec. Přidejte tento řetězec do fronty zpráv voláním metody **SendMessageAsync** , kterou jsme přidali dříve.

Pokud nejsou k dispozici žádné argumenty příkazového řádku, spusťte operaci načtení. Pro načtení první zprávy ve frontě zavolejte metodu **ReceiveMessageAsync** .

Nakonec před ukončením počkejte na vstup uživatele voláním **Console. ReadLine**.

1. Rozbalte metodu **Main** a vyhledejte argumenty příkazového řádku a počkejte na zadání uživatele.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received: {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }
   ```

2. Uložte soubor.

## <a name="complete-code"></a>Celý kód

Zde je kompletní výpis kódu pro tento projekt.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;

   namespace QueueApp
   {
    class Program
    {
        // The string value is broken up for better onscreen formatting
        private const string connectionString = "DefaultEndpointsProtocol=https;" +
                                                "AccountName=<your storage account name>;" +
                                                "AccountKey=<your key>;" +
                                                "EndpointSuffix=core.windows.net";

        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");

            if (args.Length > 0)
            {
                string value = String.Join(" ", args);
                await SendMessageAsync(queue, value);
                Console.WriteLine($"Sent: {value}");
            }
            else
            {
                string value = await ReceiveMessageAsync(queue);
                Console.WriteLine($"Received {value}");
            }

            Console.Write("Press Enter...");
            Console.ReadLine();
        }

        static async Task SendMessageAsync(CloudQueue theQueue, string newMessage)
        {
            bool createdQueue = await theQueue.CreateIfNotExistsAsync();

            if (createdQueue)
            {
                Console.WriteLine("The queue was created.");
            }

            CloudQueueMessage message = new CloudQueueMessage(newMessage);
            await theQueue.AddMessageAsync(message);
        }

        static async Task<string> ReceiveMessageAsync(CloudQueue theQueue)
        {
            bool exists = await theQueue.ExistsAsync();

            if (exists)
            {
                CloudQueueMessage retrievedMessage = await theQueue.GetMessageAsync();

                if (retrievedMessage != null)
                {
                    string theMessage = retrievedMessage.AsString;
                    await theQueue.DeleteMessageAsync(retrievedMessage);
                    return theMessage;
                }
                else
                {
                    Console.Write("The queue is empty. Attempt to delete it? (Y/N) ");
                    string response = Console.ReadLine();

                    if (response == "Y" || response == "y")
                    {
                        await theQueue.DeleteIfExistsAsync();
                        return "The queue was deleted.";
                    }
                    else
                    {
                        return "The queue was not deleted.";
                    }
                }
            }
            else
            {
                return "The queue does not exist. Add a message to the command line to create the queue and store the message.";
            }
        }
    }
   }
   ```

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Z příkazového řádku v adresáři projektu spusťte následující příkaz dotnet pro sestavení projektu.

   ```console
   dotnet build
   ```

2. Po úspěšném sestavení projektu spusťte následující příkaz, který přidá první zprávu do fronty.

   ```console
   dotnet run First queue message
   ```

Měli byste vidět tento výstup:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Spusťte aplikaci bez argumentů příkazového řádku k přijetí a odebrání první zprávy ve frontě.

   ```console
   dotnet run
   ```

4. Pokračujte v spouštění aplikace, dokud nebudou všechny zprávy odebrány. Pokud ho spustíte ještě jednou, zobrazí se zpráva s oznámením, že je fronta prázdná, a zobrazí se výzva k odstranění fronty.

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Second queue message
   Sent: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run Third queue message
   Sent: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: First queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Second queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   Received: Third queue message
   Press Enter...

   C:\Tutorials\QueueApp>dotnet run
   The queue is empty. Attempt to delete it? (Y/N) Y
   Received: The queue was deleted.
   Press Enter...

   C:\Tutorials\QueueApp>_
   ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

1. Vytvoření fronty
2. Přidávání a odebírání zpráv z fronty
3. Odstranění fronty Azure Storage

Další informace najdete v rychlém startu pro fronty Azure.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění front](storage-quickstart-queues-portal.md)
