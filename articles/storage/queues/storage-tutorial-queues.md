---
title: 'Kurz: práce s frontami Azure Queue Storage v .NET'
description: Kurz týkající se použití Queue Storage Azure k vytváření front a vkládání, získávání a odstraňování zpráv pomocí kódu .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 06/09/2020
ms.topic: tutorial
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c41b218ac0d347b2e58931421493755346b13d7
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591969"
---
# <a name="tutorial-work-with-azure-queue-storage-queues-in-net"></a>Kurz: práce s frontami Azure Queue Storage v .NET

Azure Queue Storage implementuje cloudové fronty pro povolení komunikace mezi součástmi distribuované aplikace. Každá fronta uchovává seznam zpráv, které může přidat komponenta odesílatel a kterou zpracovávají komponenty přijímače. Ve frontě se aplikace může škálovat okamžitě, aby splňovala požadavky. Tento článek popisuje základní kroky pro práci s frontou služby Azure Queue Storage.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření účtu Azure Storage
> - Vytvoření aplikace
> - Přidání klientských knihoven Azure
> - Přidat podporu pro asynchronní kód
> - Vytvoření fronty
> - Vložení zpráv do fronty
> - Vyřadit zprávy z fronty
> - Odstraní prázdnou frontu.
> - Vyhledat argumenty příkazového řádku
> - Sestavte a spusťte aplikaci.

## <a name="prerequisites"></a>Předpoklady

- Získejte bezplatnou kopii [Visual Studio Codeového](https://code.visualstudio.com/download) editoru pro různé platformy.
- Stáhněte a nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download) verze 3,1 nebo novější.
- Pokud nemáte aktuální předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu Azure Storage

Nejprve vytvořte účet Azure Storage. Podrobný průvodce vytvořením účtu úložiště najdete v tématu [Vytvoření účtu úložiště](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json). Jedná se o samostatný krok, který provedete po vytvoření bezplatného účtu Azure v požadavcích.

## <a name="create-the-app"></a>Vytvoření aplikace

Vytvořte aplikaci .NET Core s názvem `QueueApp` . Pro zjednodušení bude tato aplikace odesílat i přijímat zprávy přes frontu.

1. V okně konzoly (například cmd, PowerShell nebo Azure CLI) `dotnet new` vytvořte pomocí příkazu novou konzolovou aplikaci s názvem `QueueApp` . Tento příkaz vytvoří jednoduchý projekt "Hello World" s jedním zdrojovým souborem s názvem `Program.cs` .

   ```console
   dotnet new console -n QueueApp
   ```

2. Přepněte do nově vytvořené složky `QueueApp` a sestavte aplikaci, abyste ověřili, že je vše v pořádku.

   ```console
   cd QueueApp
   ```

   ```console
   dotnet build
   ```

   Mělo by se zobrazit výsledky podobné následujícímu výstupu:

   ```output
   C:\Tutorials>dotnet new console -n QueueApp
   The template "Console Application" was created successfully.

   Processing post-creation actions...
   Running 'dotnet restore' on QueueApp\QueueApp.csproj...
     Restore completed in 155.63 ms for C:\Tutorials\QueueApp\QueueApp.csproj.

   Restore succeeded.

   C:\Tutorials>cd QueueApp

   C:\Tutorials\QueueApp>dotnet build
   Microsoft (R) Build Engine version 16.0.450+ga8dc7f1d34 for .NET Core
   Copyright (C) Microsoft Corporation. All rights reserved.

     Restore completed in 40.87 ms for C:\Tutorials\QueueApp\QueueApp.csproj.
     QueueApp -> C:\Tutorials\QueueApp\bin\Debug\netcoreapp3.1\QueueApp.dll

   Build succeeded.
       0 Warning(s)
       0 Error(s)

   Time Elapsed 00:00:02.40

   C:\Tutorials\QueueApp>_
   ```

<!-- markdownlint-disable MD023 -->

## <a name="add-the-azure-client-libraries"></a>Přidání klientských knihoven Azure

1. Do projektu přidejte Azure Storage klientské knihovny pomocí `dotnet add package` příkazu.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   Spusťte následující příkaz ze složky projektu v okně konzoly.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   Spusťte následující příkazy ze složky projektu v okně konzoly.

   ```console
   dotnet add package Microsoft.Azure.Storage.Common
   ```

   ```console
   dotnet add package Microsoft.Azure.Storage.Queue
   ```
   ---

### <a name="add-using-statements"></a>Přidat příkazy using

1. Z příkazového řádku v adresáři projektu zadejte `code .` pro otevření Visual Studio Code v aktuálním adresáři. Nechte okno příkazového řádku otevřené. Pro pozdější spuštění bude k dispozici více příkazů. Pokud budete vyzváni k přidání prostředků C# potřebných pro sestavení a ladění, klikněte na tlačítko **Ano** .

1. Otevřete `Program.cs` zdrojový soubor a přidejte následující obory názvů hned po `using System;` příkazu. Tato aplikace používá typy z těchto oborů názvů pro připojení k Azure Storage a práci s frontami.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Uložte soubor `Program.cs`.

## <a name="add-support-for-asynchronous-code"></a>Přidat podporu pro asynchronní kód

Vzhledem k tomu, že aplikace používá cloudové prostředky, kód se spouští asynchronně.

1. Aktualizujte `Main` metodu tak, aby běžela asynchronně. Nahraďte `void` `async Task` návratovou hodnotou.

   ```csharp
   static async Task Main(string[] args)
   ```

1. Uložte soubor `Program.cs`.

## <a name="create-a-queue"></a>Vytvoření fronty

Před provedením jakýchkoli volání do rozhraní API Azure musíte získat přihlašovací údaje z Azure Portal.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Přidání připojovacího řetězce do aplikace

Přidejte připojovací řetězec do aplikace, aby mohl získat přístup k účtu úložiště.

1. Přepněte zpátky na Visual Studio Code.

1. V `Main` metodě nahraďte `Console.WriteLine("Hello, World");` kód následujícím řádkem, který získá připojovací řetězec z proměnné prostředí.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Přidejte následující kód k `Main` vytvoření objektu Queue, který je později předán do metod Odeslat a přijmout.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Uložte soubor.

## <a name="insert-messages-into-the-queue"></a>Vložit zprávy do fronty

Vytvořte novou metodu pro odeslání zprávy do fronty.

1. `InsertMessageAsync`Do třídy přidejte následující metodu `Program` .

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   Této metodě se předal odkaz na frontu. Vytvoří se nová fronta, pokud ještě neexistuje, voláním metody [`CreateIfNotExistsAsync`](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync) . Pak přidá `newMessage` do fronty voláním metody [`SendMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   Této metodě se předal odkaz na frontu. Vytvoří se nová fronta, pokud ještě neexistuje, voláním metody [`CreateIfNotExistsAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync) . Pak přidá `newMessage` do fronty voláním metody [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Volitelné:** Ve výchozím nastavení je maximální hodnota TTL (Time-to-Live) pro zprávu nastavená na sedm dní. Můžete zadat libovolné kladné číslo pro hodnotu TTL (Time to Live). Následující fragment kódu přidá zprávu, která **nikdy** nevyprší.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

    Chcete-li přidat zprávu, jejíž platnost nevyprší, použijte `Timespan.FromSeconds(-1)` při volání metody `SendMessageAsync` .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

    Chcete-li přidat zprávu, jejíž platnost nevyprší, použijte `Timespan.FromSeconds(-1)` při volání metody `AddMessageAsync` .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Uložte soubor.

Zpráva fronty musí být ve formátu kompatibilním s požadavkem XML pomocí kódování UTF-8. Zpráva může mít velikost až 64 KB. Pokud zpráva obsahuje binární data, zpráva se [zakóduje ve formátu base64](/dotnet/api/system.convert.tobase64string) .

## <a name="dequeue-messages"></a>Vyřadit zprávy z fronty

Vytvořte novou metodu pro načtení zprávy z fronty. Po úspěšném přijetí zprávy je důležité ji odstranit z fronty, takže nebude zpracována více než jednou.

1. Přidejte do vaší třídy novou metodu s názvem `RetrieveNextMessageAsync` `Program` .

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   Tato metoda přijímá zprávu z fronty voláním metody [`ReceiveMessagesAsync`](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) , která předává `1` první parametr, aby načetla pouze další zprávu ve frontě. Po přijetí zprávy ji pomocí volání odstraňte z fronty [`DeleteMessageAsync`](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync) .

   Při odeslání zprávy do fronty s verzí sady SDK před V12 je automaticky kódována v kódování Base64. Od V12 se tato funkce odebrala. Když načtete zprávu pomocí sady V12 SDK, není automaticky dekódované v kódování Base64. Obsah musíte explicitně [dekódovat](/dotnet/api/system.convert.frombase64string) sami.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   Tato metoda přijímá zprávu z fronty voláním metody [`GetMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync) . Po přijetí zprávy ji pomocí volání odstraňte z fronty [`DeleteMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync) .

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Uložte soubor.

## <a name="delete-an-empty-queue"></a>Odstraní prázdnou frontu.

Je to osvědčený postup na konci projektu k určení, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které necháte běžet, vás stojí peníze. Pokud fronta existuje, ale je prázdná, požádejte uživatele, jestli ho chce odstranit.

1. Rozbalte `RetrieveNextMessageAsync` metodu pro zahrnutí výzvy k odstranění prázdné fronty.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Uložte soubor.

## <a name="check-for-command-line-arguments"></a>Vyhledat argumenty příkazového řádku

Pokud se do aplikace přenesou nějaké argumenty příkazového řádku, předpokládá se, že se jedná o zprávu, která se přidá do fronty. Spojit argumenty dohromady a vytvořit řetězec. Přidejte tento řetězec do fronty zpráv voláním metody, kterou `InsertMessageAsync` jsme přidali dříve.

Pokud nejsou k dispozici žádné argumenty příkazového řádku, zkuste operaci načtení. Zavolejte `RetrieveNextMessageAsync` metodu pro načtení další zprávy ve frontě.

Nakonec před ukončením volání počkejte na vstup uživatele `Console.ReadLine` .

1. Rozbalte `Main` metodu pro kontrolu argumentů příkazového řádku a počkejte na zadání uživatele.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Uložte soubor.

## <a name="complete-code"></a>Celý kód

Zde je kompletní výpis kódu pro tento projekt.

   # <a name="net-v12"></a>[.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_AllCode":::
   ---

## <a name="build-and-run-the-app"></a>Sestavte a spusťte aplikaci.

1. Z příkazového řádku v adresáři projektu spusťte následující příkaz dotnet pro sestavení projektu.

   ```console
   dotnet build
   ```

1. Po úspěšném sestavení projektu spusťte následující příkaz, který přidá první zprávu do fronty.

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

1. Spusťte aplikaci bez argumentů příkazového řádku k přijetí a odebrání první zprávy ve frontě.

   ```console
   dotnet run
   ```

1. Pokračujte v spouštění aplikace, dokud nebudou všechny zprávy odebrány. Pokud ho spustíte ještě jednou, zobrazí se zpráva s oznámením, že je fronta prázdná, a zobrazí se výzva k odstranění fronty.

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
1. Přidávání a odebírání zpráv z fronty
1. Odstranění fronty Azure Queue Storage

Další informace najdete v rychlých startech k Azure Queue Storage.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění pro portál](storage-quickstart-queues-portal.md)

- [Rychlé zprovoznění front pro .NET](storage-quickstart-queues-dotnet.md)
- [Rychlé zprovoznění front pro Java](storage-quickstart-queues-java.md)
- [Rychlé zprovoznění front pro Python](storage-quickstart-queues-python.md)
- [Rychlé zprovoznění front pro JavaScript](storage-quickstart-queues-nodejs.md)
