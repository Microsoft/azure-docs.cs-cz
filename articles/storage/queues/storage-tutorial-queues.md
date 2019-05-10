---
title: Kurz – práce s frontami služby Azure storage – Azure Storage
description: Kurz předvádějící způsoby použití služby front Azure k vytvoření fronty a vložení, zobrazovat a odstraňovat zprávy.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: cbrooks
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.date: 04/24/2019
ms.openlocfilehash: 6b833ef56b890eb4ea0db6b48fe8c2622e211498
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233876"
---
# <a name="tutorial-work-with-azure-storage-queues"></a>Kurz: Práce s frontami Azure Storage

Azure Queue storage implementuje založené na cloudu fronty k umožnění komunikace mezi součástmi distribuované aplikace. Každá fronta udržuje seznam zpráv, které lze přidávat komponenta odesílatele a zpracovány komponenty příjemce. S frontou můžete okamžitě škálovat aplikace podle potřeby. Tento článek popisuje základní kroky pro práci se do fronty Azure storage.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření účtu úložiště Azure
> - Vytvoření aplikace
> - Přidání podpory pro asynchronní kód
> - Vytvoření fronty
> - Vložení zprávy do fronty
> - Odstranění z fronty zpráv
> - Odstranit prázdné frontě
> - Zkontrolujte argumenty příkazového řádku
> - Sestavení a spuštění aplikace

## <a name="prerequisites"></a>Požadavky

- Získejte si bezplatnou kopii verze různé platformy [Visual Studio Code](https://code.visualstudio.com/download) editoru.
- Stáhněte a nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).
- Pokud nemáte aktuální předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/) předtím, než začnete.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Nejprve vytvořte účet úložiště Azure. Podrobný návod k vytvoření účtu úložiště, najdete v článku [vytvořit účet úložiště](../common/storage-quickstart-create-account.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) rychlý start.

## <a name="create-the-app"></a>Vytvoření aplikace

Vytvoření aplikace .NET Core s názvem **QueueApp**. Pro jednoduchost tato aplikace bude odesílat i přijímat zprávy ve frontě.

1. V okně konzoly (jako je CMD, PowerShell nebo rozhraní příkazového řádku Azure), použijte `dotnet new` příkaz pro vytvoření nových konzolovou aplikaci s názvem **QueueApp**. Tento příkaz vytvoří jednoduchý "Hello World" C# projekt s jeden zdrojový soubor: **Program.cs**.

   ```console
   dotnet new console -n QueueApp
   ```

2. Přepnout na nově vytvořený **QueueApp** složky a sestavení aplikace a zkontrolujte, že vše je dobře.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Zobrazí se výsledky podobné následujícím:

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

Protože aplikace využívá cloudových prostředků, kód se spustí asynchronně. Ale C#společnosti **asynchronní** a **await** nebyly platné klíčových slov v **hlavní** metody až do C# 7.1. Snadno můžete přepnout, kterou kompilátor prostřednictvím příznaku v **csproj** souboru.

1. Z příkazového řádku v adresáři projektu, zadejte `code .` otevřete Visual Studio Code v aktuálním adresáři. Nechte okno příkazového řádku otevřené. Budou další příkazy prováděly později. Pokud se zobrazí výzva k přidání C# prostředky potřebné k sestavení a ladění, klikněte **Ano** tlačítko.

2. Otevřít **QueueApp.csproj** souboru v editoru.

3. Přidat `<LangVersion>7.1</LangVersion>` do první **PropertyGroup** v souboru sestavení. Ujistěte se, že můžete přidat pouze **LangVersion** označit jako vaše **TargetFramework** může lišit v závislosti na tom, kterou verzi rozhraní .NET jste nainstalovali.

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <OutputType>Exe</OutputType>
       <TargetFramework>netcoreapp2.1</TargetFramework>
       <LangVersion>7.1</LangVersion>
     </PropertyGroup>

   ...

   ```

4. Uložit **QueueApp.csproj** souboru.

5. Otevřít **Program.cs** zdrojového souboru a aktualizace **hlavní** metody, aby se spouštěly asynchronně. Nahraďte **void** s **asynchronní úloha** návratovou hodnotu.

   ```csharp
   static async Task Main(string[] args)
   ```

6. Uložit **Program.cs** souboru.

## <a name="create-a-queue"></a>Vytvoření fronty

1. Nainstalujte **Windows Azure. Úložiště** balíčku do projektu s `dotnet add package` příkazu. Ze složky projektu v okně konzoly spusťte následující příkaz dotnet.

   ```console
   dotnet add package WindowsAzure.Storage
   ```

2. V horní části **Program.cs** přidejte následující obory názvů hned po `using System;` příkazu. Tato aplikace používá pro připojení ke službě Azure Storage a práci s frontami typy z těchto oborů názvů.

   ```csharp
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;
   ```

3. Uložit **Program.cs** souboru.

### <a name="get-your-connection-string"></a>Získání připojovacího řetězce

Klientská knihovna používá připojovací řetězec k navázání připojení. Připojovací řetězec je k dispozici v **nastavení** části vašeho účtu úložiště na webu Azure Portal.

1. Ve webovém prohlížeči, přihlaste se k [webu Azure portal](https://portal.azure.com/).

2. Na webu Azure Portal přejděte na svůj účet úložiště.

3. Vyberte **přístupové klíče**.

4. Klikněte na tlačítko **kopírování** tlačítko vpravo od **připojovací řetězec** pole.

![Připojovací řetězec](media/storage-tutorial-queues/get-connection-string.png)

Připojovací řetězec má tento formát:

   ```
   "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
   ```

### <a name="add-the-connection-string-to-the-app"></a>Přidat připojovací řetězec do aplikace

Přidáte připojovací řetězec do aplikace, aby měl přístup k účtu úložiště.

1. Přepněte zpátky na Visual Studio Code.

2. V **Program** třídy, přidejte `private const string connectionString =` člena pro uložení připojovacího řetězce.

3. Za znaménkem rovnítka vložte hodnotu řetězce, který jste zkopírovali dříve na portálu Azure. **ConnectionString** hodnota bude jedinečný pro váš účet.

4. Odebrat kód "Hello World" z **hlavní**. Váš kód by měl vypadat podobně jako následující ale hodnotou řetězce jedinečné připojení.

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

5. Aktualizace **hlavní** k vytvoření **CloudQueue** objektu, který se potom předány do odesílání a příjem metody.

   ```csharp
        static async Task Main(string[] args)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
            CloudQueue queue = queueClient.GetQueueReference("mystoragequeue");
        }
   ```

6. Uložte soubor.

## <a name="insert-messages-into-the-queue"></a>Vložení zprávy do fronty

Vytvoření nové metody pro odeslání zprávy do fronty. Přidejte následující metodu do vaší **Program** třídy. Tato metoda získá odkaz na frontu, pak vytvoří novou frontu, pokud ještě neexistuje voláním [CreateIfNotExistsAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync?view=azure-dotnet). Poté jej přidá zprávu do fronty voláním [AddMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync?view=azure-dotnet).

1. Přidejte následující **SendMessageAsync** metodu pro vaše **Program** třídy.

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

## <a name="dequeue-messages"></a>Odstranění z fronty zpráv

Vytvořit novou metodu s názvem **ReceiveMessageAsync**. Tato metoda přijímá zprávy z fronty pomocí volání [GetMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync?view=azure-dotnet). Jakmile úspěšně doručení zprávy, je potřeba odstranit z fronty, takže se zpracuje více než jednou. Po přijetí zprávy odstraní ji z fronty pomocí volání [DeleteMessageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync?view=azure-dotnet).

1. Přidejte následující **ReceiveMessageAsync** metodu pro vaše **Program** třídy.

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

## <a name="delete-an-empty-queue"></a>Odstranit prázdné frontě

Je osvědčeným postupem na konci projektu a zjistěte, jestli stále potřebují prostředky, které jste vytvořili. Levé může spuštěné prostředky nákladů peníze. Pokud fronta existuje, ale je prázdný, požádejte uživatele, pokud by například k jeho odstranění.

1. Rozbalte **ReceiveMessageAsync** tak, aby zahrnoval výzvy k odstranění prázdnou frontu.

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

## <a name="check-for-command-line-arguments"></a>Zkontrolujte argumenty příkazového řádku

Pokud existují jakékoli argumenty příkazového řádku předané do aplikace, předpokládají, že jsou přidané do fronty zprávu. Připojte se k argumenty a vytvořit tak řetězec. Přidejte tento řetězec do fronty zpráv voláním **SendMessageAsync** metoda jsme přidali dříve.

Pokud neexistují žádné argumenty příkazového řádku, provést operace načtení. Volání **ReceiveMessageAsync** metodu pro načtení první zprávu ve frontě.

Nakonec čekání na vstup uživatele před ukončením voláním **Console.ReadLine**.

1. Rozbalte **hlavní** metodu ke kontrole argumenty příkazového řádku a počkat na vstup uživatele.

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

Tady je úplný výpis pro tento projekt kódu.

   ```csharp
   using System;
   using System.Threading.Tasks;
   using Microsoft.WindowsAzure.Storage;
   using Microsoft.WindowsAzure.Storage.Queue;

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

1. Z příkazového řádku v adresáři projektu spusťte následující příkaz dotnet k sestavení projektu.

   ```console
   dotnet build
   ```

2. Po úspěšném sestavení projektu, spusťte následující příkaz pro přidání první zprávu do fronty.

   ```console
   dotnet run First queue message
   ```

Byste měli vidět tento výstup:

   ```output
   C:\Tutorials\QueueApp>dotnet run First queue message
   The queue was created.
   Sent: First queue message
   Press Enter..._
   ```

3. Spusťte aplikaci bez argumentů příkazového řádku pro příjem a odstranit první zprávu ve frontě.

   ```console
   dotnet run
   ```

4. I nadále spouštět aplikace, dokud se odeberou všechny zprávy. Pokud ho ještě jednou, získáte zprávu, že je fronta prázdná a výzva k odstranění fronty.

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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

1. Vytvoření fronty
2. Přidání a odebrání zprávy z fronty
3. Odstranění fronty služby Azure storage

Projděte si rychlý start Azure fronty pro další informace.

> [!div class="nextstepaction"]
> [Rychlý start fronty](storage-quickstart-queues-portal.md)
