---
title: Výuka – práce s frontami úložiště Azure – Azure Storage
description: Kurz o tom, jak používat službu Azure Queue k vytváření front a vkládání, přijímaných a odstraňovacích zpráv.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2019
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: cbrooks
ms.openlocfilehash: 9cbdc5231fdc9f836f300b1a3a81a237a9efc123
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75968200"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Kurz: Práce s frontami úložiště Azure

Úložiště Azure Queue implementuje cloudové fronty, které umožňují komunikaci mezi součástmi distribuované aplikace. Každá fronta udržuje seznam zpráv, které mohou být přidány komponentou odesílatele a zpracovány komponentou příjemce. S frontou aplikace můžete škálovat okamžitě tak, aby splňovaly požadavky. Tento článek ukazuje základní kroky pro práci s frontou úložiště Azure.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření účtu úložiště Azure
> - Vytvoření aplikace
> - Přidání podpory pro asynchronní kód
> - Vytvoření fronty
> - Vložení zpráv do fronty
> - Vyřazení zpráv ze zařazení do fronty
> - Odstranění prázdné fronty
> - Kontrola argumentů příkazového řádku
> - Sestavení a spuštění aplikace

## <a name="prerequisites"></a>Požadavky

- Získejte bezplatnou kopii editoru [kódu Visual Studio pro](https://code.visualstudio.com/download) různé platformy.
- Stáhněte a nainstalujte sadu [.NET Core SDK](https://dotnet.microsoft.com/download).
- Pokud nemáte aktuální předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Nejprve vytvořte účet úložiště Azure. Podrobný návod k vytvoření účtu úložiště najdete v tématu Vytvoření rychlého startu [účtu úložiště.](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json)

## <a name="create-the-app"></a>Vytvoření aplikace

Vytvořte aplikaci .NET Core s názvem **QueueApp**. Pro jednoduchost bude tato aplikace odesílat i přijímat zprávy prostřednictvím fronty.

1. V okně konzoly (například CMD, PowerShell nebo `dotnet new` Azure CLI) použijte příkaz k vytvoření nové konzolové aplikace s názvem **QueueApp**. Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Přepněte do nově vytvořené složky **QueueApp** a vytvořte aplikaci, abyste ověřili, že je vše v pořádku.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Měli byste vidět výsledky podobné následujícímu:

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

## <a name="add-support-for-asynchronous-code"></a>Přidání podpory pro asynchronní kód

Vzhledem k tomu, že aplikace používá cloudové prostředky, kód běží asynchronně. Však C # **async** a **await** nebyly platné klíčová slova v **Main** metody až do C# 7.1. Na tento kompilátor můžete snadno přepnout pomocí příznaku v souboru **csproj.**

1. Z příkazového řádku v adresáři projektu sem otevřete `code .` kód sady Visual Studio v aktuálním adresáři. Ponechte okno příkazového řádku otevřené. Později bude možné provést další příkazy. Pokud se zobrazí výzva k přidání prostředků Jazyka C# potřebných k sestavení a ladění, klepněte na tlačítko **Ano.**

2. Otevřete v editoru soubor **QueueApp.csproj**.

3. Přidejte `<LangVersion>7.1</LangVersion>` do první **PropertyGroup** v souboru sestavení. Ujistěte se, že přidáte pouze **LangVersion** značku jako **targetframework** se může lišit v závislosti na verzi rozhraní .NET, které jste nainstalovali.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Uložte soubor **QueueApp.csproj.**

5. Otevřete zdrojový soubor **Program.cs** a aktualizujte metodu **Main** tak, aby běžela asynchronně. Nahradit **void** **asynchronní task** vrácená hodnota.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Uložte **soubor Program.cs.**

## <a name="create-a-queue"></a>Vytvoření fronty

1. Nainstalujte balíčky **Microsoft.Azure.Storage.Common** a **Microsoft.Azure.Storage.Queue** do projektu pomocí příkazu. `dotnet add package` Spusťte následující příkazy dotnet ze složky projektu v okně konzoly.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   dotnet add package Microsoft.Azure.Storage.Queue
   ```

2. V horní části **souboru Program.cs** přidejte následující `using System;` obory názvů hned za příkaz. Tato aplikace používá typy z těchto oborů názvů pro připojení k Azure Storage a práci s frontami.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Storage;
   using Microsoft.Azure.Storage.Queue;
   ```

3. Uložte **soubor Program.cs.**

### <a name="get-your-connection-string"></a>Získání připojovacího řetězce

Klientská knihovna používá k navázání připojení připojovací řetězec. Váš připojovací řetězec je k dispozici v části **Nastavení** vašeho účtu úložiště na webu Azure Portal.

1. Ve webovém prohlížeči se přihlaste na [portál Azure](https://portal.azure.com/).

2. Na webu Azure Portal přejděte na svůj účet úložiště.

3. Vyberte **přístupové klávesy**.

4. Klepněte na tlačítko **Kopírovat** vpravo od pole **Připojovací řetězec.**

![Připojovací řetězec](media/storage-tutorial-queues/get-connection-string.png)

Připojovací řetězec má tento formát:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Přidání připojovacího řetězce do aplikace

Přidejte připojovací řetězec do aplikace, aby měl přístup k účtu úložiště.

1. Přepněte zpět na kód sady Visual Studio.

2. Ve třídě **Program** přidejte `private const string connectionString =` člena, který bude držet připojovací řetězec.

3. Za rovnítko vložte hodnotu řetězce, kterou jste zkopírovali dříve na webu Azure Portal. Hodnota **connectionString** bude jedinečná pro váš účet.

4. Odeberte kód "Hello World" z **hlavního**. Váš kód by měl vypadat podobně jako následující, ale s jedinečnou hodnotou připojovacího řetězce.

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

5. Aktualizujte **Main** vytvořit **Objekt CloudQueue,** který je později předán do metody odesílání a přijímání.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Uložte soubor.

## <a name="insert-messages-into-the-queue"></a>Vkládání zpráv do fronty

Vytvořte novou metodu pro odeslání zprávy do fronty. Přidejte do třídy **Program** následující metodu. Tato metoda získá odkaz na frontu, pak vytvoří novou frontu, pokud již neexistuje voláním [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Potom přidá zprávu do fronty voláním [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

1. Přidejte do třídy **Program** následující metodu **SendMessageAsync.**

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

Zpráva musí být ve formátu, který může být součástí požadavku XML s kódováním UTF-8 a může mít velikost až 64 kB. Pokud zpráva obsahuje binární data, doporučujeme zakódovat zprávu Base64.

Ve výchozím nastavení je maximální doba života zprávy nastavena na 7 dní. Pro aktuální dobu přenosu zprávy můžete zadat libovolné kladné číslo. Chcete-li přidat zprávu, jejíž platnost nevyprší, použijte `Timespan.FromSeconds(-1)` ve svém volání **AddMessageAsync**.

```csharp
await theQueue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

## <a name="dequeue-messages"></a>Vyřazení zpráv ze zařazení do fronty

Vytvořte novou metodu s názvem **ReceiveMessageAsync**. Tato metoda obdrží zprávu z fronty voláním [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Jakmile je zpráva úspěšně přijata, je důležité ji odstranit z fronty, aby nebyla zpracována více než jednou. Po přijetí zprávy ji odstraňte z fronty voláním [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

1. Přidejte do třídy **Program** následující metodu **ReceiveMessageAsync.**

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

## <a name="delete-an-empty-queue"></a>Odstranění prázdné fronty

Na konci projektu je vhodné určit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které necháte běžet, vás můžou stát peníze. Pokud fronta existuje, ale je prázdná, zeptejte se uživatele, zda ji chce odstranit.

1. Rozbalte metodu **ReceiveMessageAsync** tak, aby obsahovala výzvu k odstranění prázdné fronty.

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

## <a name="check-for-command-line-arguments"></a>Kontrola argumentů příkazového řádku

Pokud existují nějaké argumenty příkazového řádku předány do aplikace, předpokládejme, že se jedná o zprávu, která má být přidána do fronty. Spojte argumenty dohromady a vytvořte řetězec. Přidejte tento řetězec do fronty zpráv voláním metody **SendMessageAsync,** kterou jsme přidali dříve.

Pokud neexistují žádné argumenty příkazového řádku, proveďte operaci načtení. Volání **ReceiveMessageAsync** metoda načíst první zprávu ve frontě.

Nakonec počkejte na vstup uživatele před ukončením voláním **Console.ReadLine**.

1. Rozbalte **metodu Main** a zkontrolujte argumenty příkazového řádku a počkejte na vstup uživatele.

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

1. Z příkazového řádku v adresáři projektu spusťte následující příkaz dotnet k vytvoření projektu.

   ```console
   dotnet build
   ```

2. Po úspěšném sestavení projektu spusťte následující příkaz a přidejte první zprávu do fronty.

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

3. Spusťte aplikaci bez argumentů příkazového řádku pro příjem a odebrání první zprávy ve frontě.

   ```console
   dotnet run
   ```

4. Pokračujte v spouštění aplikace, dokud nebudou odebrány všechny zprávy. Pokud ji spustíte ještě jednou, zobrazí se zpráva, že fronta je prázdná, a výzva k odstranění fronty.

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
2. Přidání a odebrání zpráv z fronty
3. Odstranění fronty úložiště Azure

Další informace najdete v rychlém startu front Azure.

> [!div class="nextstepaction"]
> [Rychlý start fronty](storage-quickstart-queues-portal.md)
