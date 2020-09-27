---
title: Kurz – práce s Azure Storage frontami v .NET
description: V tomto kurzu se naučíte, jak pomocí Služba front Azure vytvořit fronty a vkládat, získávat a odstraňovat zprávy pomocí kódu .NET.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 06/09/2020
ms.service: storage
ms.subservice: queues
ms.topic: tutorial
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: 23684dbbc5cb8c2d5fc4880ae8fe1999450928e0
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400566"
---
# <a name="tutorial-work-with-azure-storage-queues-in-net"></a>Kurz: práce s frontami úložiště Azure v .NET

Služba Azure Queue Storage implementuje cloudové fronty a umožňuje komunikaci mezi komponentami distribuované aplikace. Každá fronta uchovává seznam zpráv, které může přidat komponenta odesílatel a kterou zpracovávají komponenty přijímače. Ve frontě se aplikace může škálovat okamžitě, aby splňovala požadavky. Tento článek popisuje základní kroky pro práci s frontou služby Azure Storage.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> - Vytvoření účtu úložiště Azure
> - Vytvoření aplikace
> - Přidání klientských knihoven Azure
> - Přidat podporu pro asynchronní kód
> - Vytvoření fronty
> - Vložení zpráv do fronty
> - Vyřadit zprávy z fronty
> - Odstraní prázdnou frontu.
> - Vyhledat argumenty příkazového řádku
> - Sestavte a spusťte aplikaci.

## <a name="prerequisites"></a>Požadavky

- Získejte bezplatnou kopii [Visual Studio Codeového](https://code.visualstudio.com/download) editoru pro různé platformy.
- Stáhněte a nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download) verze 3,1 nebo novější.
- Pokud nemáte aktuální předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-azure-storage-account"></a>Vytvoření účtu úložiště Azure

Nejdřív vytvořte účet úložiště Azure. Podrobný průvodce vytvořením účtu úložiště najdete v rychlém startu [Vytvoření účtu úložiště](../common/storage-account-create.md?toc=%2Fazure%2Fstorage%2Fqueues%2Ftoc.json) . Jedná se o samostatný krok, který provedete po vytvoření bezplatného účtu Azure v požadavcích.

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

## <a name="add-the-azure-client-libraries"></a>Přidání klientských knihoven Azure

1. Do projektu přidejte Azure Storage klientské knihovny pomocí `dotnet add package` příkazu.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   Spusťte následující příkaz ze složky projektu v okně konzoly.

   ```console
   dotnet add package Azure.Storage.Queues
   ```

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

1. Otevřete zdrojový soubor **program.cs** a přidejte následující obory názvů ihned po `using System;` příkazu. Tato aplikace používá typy z těchto oborů názvů pro připojení k Azure Storage a práci s frontami.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_UsingStatements":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_UsingStatements":::

1. Uložte soubor **Program.cs**.

## <a name="add-support-for-asynchronous-code"></a>Přidat podporu pro asynchronní kód

Vzhledem k tomu, že aplikace používá cloudové prostředky, kód se spouští asynchronně.

1. Aktualizujte metodu **Main** tak, aby běžela asynchronně. Nahraďte **void** hodnotou návratové hodnoty **asynchronní úlohy** .

   ```csharp
   static async Task Main(string[] args)
   ```

1. Uložte soubor **Program.cs**.

## <a name="create-a-queue"></a>Vytvoření fronty

Před provedením jakýchkoli volání do rozhraní API Azure musíte získat přihlašovací údaje z Azure Portal.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

### <a name="add-the-connection-string-to-the-app"></a>Přidání připojovacího řetězce do aplikace

Přidejte připojovací řetězec do aplikace, aby mohl získat přístup k účtu úložiště.

1. Přepněte zpátky na Visual Studio Code.

1. V metodě **Main**  nahraďte `Console.WriteLine("Hello World!");` kód následujícím řádkem, který získá připojovací řetězec z proměnné prostředí.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_DeclareConnectionString":::

1. Přidejte následující kód do části **Main** k vytvoření objektu Queue, který je později předán do metod Odeslat a přijmout.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_CreateQueueClient":::

1. Uložte soubor.

## <a name="insert-messages-into-the-queue"></a>Vložit zprávy do fronty

Vytvořte novou metodu pro odeslání zprávy do fronty.

1. Do třídy **programu** přidejte následující metodu **InsertMessageAsync** .

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   Této metodě se předal odkaz na frontu. Vytvoří se nová fronta, pokud ještě neexistuje, voláním [CreateIfNotExistsAsync](/dotnet/api/azure.storage.queues.queueclient.createifnotexistsasync). Pak přidá *newMessage* do fronty voláním [SendMessageAsync](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_InsertMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Této metodě se předal odkaz na frontu. Vytvoří se nová fronta, pokud ještě neexistuje, voláním [CreateIfNotExistsAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.createifnotexistsasync). Pak přidá *newMessage* do fronty voláním [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_InsertMessage":::

1. **Volitelné** Ve výchozím nastavení je maximální hodnota TTL (Time-to-Live) pro zprávu nastavená na sedm dní. Můžete zadat libovolné kladné číslo pro hodnotu TTL (Time to Live). Následující fragment kódu přidá zprávu, která *nikdy* nevyprší.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

    Chcete-li přidat zprávu, jejíž platnost nevyprší, použijte `Timespan.FromSeconds(-1)` ve volání **SendMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

    Chcete-li přidat zprávu, jejíž platnost nevyprší, použijte `Timespan.FromSeconds(-1)` ve volání **AddMessageAsync**.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_SendNonExpiringMessage":::

1. Uložte soubor.

Zpráva fronty musí být ve formátu kompatibilním s požadavkem XML pomocí kódování UTF-8. Zpráva může mít velikost až 64 KB. Pokud zpráva obsahuje binární data, zpráva se [zakóduje ve formátu base64](/dotnet/api/system.convert.tobase64string) .

## <a name="dequeue-messages"></a>Vyřadit zprávy z fronty

Vytvořte novou metodu pro načtení zprávy z fronty. Po úspěšném přijetí zprávy je důležité ji odstranit z fronty, takže nebude zpracována více než jednou.

1. Přidejte do třídy **programu** novou metodu s názvem **RetrieveNextMessageAsync** .

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   Tato metoda přijímá zprávu z fronty voláním [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync), předáním 1 do prvního parametru pro načtení pouze další zprávy ve frontě. Po přijetí zprávy ji vymažte z fronty voláním [DeleteMessageAsync](/dotnet/api/azure.storage.queues.queueclient.deletemessageasync).

   Při odeslání zprávy do fronty s verzí sady SDK před V12 je automaticky kódována v kódování Base64. Počínaje V12 se odstranila funkčnost. Když načtete zprávu pomocí sady V12 SDK, není automaticky dekódované v kódování Base64. Obsah musíte explicitně [dekódovat](/dotnet/api/system.convert.frombase64string) sami.

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   Tato metoda přijímá zprávu z fronty voláním [GetMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessageasync). Po přijetí zprávy ji vymažte z fronty voláním [DeleteMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessageasync).

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Initial.cs" id="snippet_InitialRetrieveMessage":::

1. Uložte soubor.

## <a name="delete-an-empty-queue"></a>Odstraní prázdnou frontu.

Je to osvědčený postup na konci projektu k určení, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které necháte běžet, vás stojí peníze. Pokud fronta existuje, ale je prázdná, požádejte uživatele, jestli ho chce odstranit.

1. Rozbalte metodu **RetrieveNextMessageAsync** pro zahrnutí výzvy k odstranění prázdné fronty.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_RetrieveMessage":::

1. Uložte soubor.

## <a name="check-for-command-line-arguments"></a>Vyhledat argumenty příkazového řádku

Pokud se do aplikace přenesou nějaké argumenty příkazového řádku, předpokládá se, že se jedná o zprávu, která se přidá do fronty. Spojit argumenty dohromady a vytvořit řetězec. Přidejte tento řetězec do fronty zpráv voláním metody **InsertMessageAsync** , kterou jsme přidali dříve.

Pokud nejsou k dispozici žádné argumenty příkazového řádku, zkuste operaci načtení. Pro načtení další zprávy ve frontě zavolejte metodu **RetrieveNextMessageAsync** .

Nakonec před ukončením počkejte na vstup uživatele voláním **Console. ReadLine**.

1. Rozbalte metodu **Main** a vyhledejte argumenty příkazového řádku a počkejte na zadání uživatele.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_Main":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v11/QueueApp/Program.cs" id="snippet_Main":::

1. Uložte soubor.

## <a name="complete-code"></a>Celý kód

Zde je kompletní výpis kódu pro tento projekt.

   # <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

   :::code language="csharp" source="~/azure-storage-snippets/queues/tutorial/dotnet/dotnet-v12/QueueApp/Program.cs" id="snippet_AllCode":::

   # <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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
1. Odstranění fronty Azure Storage

Další informace najdete v rychlých startech front Azure.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění pro portál](storage-quickstart-queues-portal.md)

- [Rychlé zprovoznění front pro .NET](storage-quickstart-queues-dotnet.md)
- [Rychlé zprovoznění front pro Java](storage-quickstart-queues-java.md)
- [Rychlé zprovoznění front pro Python](storage-quickstart-queues-python.md)
- [Rychlé zprovoznění front pro JavaScript](storage-quickstart-queues-nodejs.md)
