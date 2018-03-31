---
title: Začínáme s Azure queue storage a Visual Studio připojené služby (ASP.NET) | Microsoft Docs
description: Jak začít používat fronty Azure storage v projektu ASP.NET v sadě Visual Studio po připojení k účtu úložiště pomocí Visual Studio připojené Services
services: storage
documentationcenter: ''
author: ghogen
manager: douge
editor: ''
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: 89baa741dcd916b70514f9703255b9e6d01c689b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s Azure queue storage a Visual Studio připojené služby (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

Azure queue storage poskytuje cloudu zasílání zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Tento kurz ukazuje, jak napsat kód ASP.NET pro některé běžné scénáře použití fronty Azure storage entity. Mezi tyto scénáře patří běžné úkoly, jako je například vytváření fronty Azure a přidání, úprava, čtení a odebrání zprávy do fronty.

##<a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvořit řadič MVC 

1. V **Průzkumníku řešení**, klikněte pravým tlačítkem na **řadiče**a v místní nabídce vyberte **Přidat -> řadiče**.

    ![Přidat řadič do aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Na **přidat vygenerované uživatelské rozhraní** dialogovém okně, vyberte **kontroler MVC 5 – prázdný**a vyberte **přidat**.

    ![Zadejte typ řadiče MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Na **přidat kontroler** dialogové okno, názvu kontroleru *QueuesController*a vyberte **přidat**.

    ![Název řadiče MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Přidejte následující *pomocí* direktivy pro `QueuesController.cs` souboru:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
## <a name="create-a-queue"></a>Vytvoření fronty

Následující kroky ukazují, jak vytvořit frontu:

> [!NOTE]
> 
> V této části předpokládá, že jste dokončili postup [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`. 

1. Přidejte metodu s názvem **CreateQueue** , který vrací **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **CreateQueue** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Získání **CloudQueue** objekt, který reprezentuje odkaz na název požadovaného fronty. **CloudQueueClient.GetQueueReference** metoda neprovede požadavek fronty úložiště. Odkaz se vrátí, zda fronta existuje. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.CreateIfNotExists** metodu pro vytvoření fronty, pokud ještě neexistuje. **CloudQueue.CreateIfNotExists** metoda vrátí **true** Pokud fronta neexistuje a je úspěšně vytvořen. V opačném **false** je vrácen.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aktualizace **ViewBag** s názvem fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **CreateQueue** pro název zobrazení, vyberte **přidat**.

1. Otevřete `CreateQueue.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **fronty vytvořit** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Vytvořit frontu](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak je uvedeno nahoře, **CloudQueue.CreateIfNotExists** metoda vrátí **true** pouze když neexistuje a k vytvoření fronty. Proto pokud spustíte aplikaci, když fronta existuje, vrátí metoda **false**. Aplikace je třeba spustit vícekrát, je nutné odstranit frontu před spuštěním aplikace znovu. Odstranění fronty, můžete to udělat pomocí **CloudQueue.Delete** metoda. Můžete také odstranit pomocí fronty [portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) nebo [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Jakmile jste [vytvořili frontu](#create-a-queue), můžete taky přidat zprávy do této fronty. Tato část vás provede procesem přidání zprávu do fronty *zkušební fronty*. 

> [!NOTE]
> 
> V této části předpokládá, že jste dokončili postup [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **AddMessage** , který vrací **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **AddMessage** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který reprezentuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Vytvořte **CloudQueueMessage** objektu, který představuje zprávu, kterou chcete přidat do fronty. A **CloudQueueMessage** můžete vytvořit objekt z řetězce (ve formátu UTF-8) nebo pole bajtů.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Volání **CloudQueue.AddMessage** metody přidat messaged do fronty.

    ```csharp
    queue.AddMessage(message);
    ```

1. Vytvoření a nastavení několika **ViewBag** vlastnosti pro zobrazení v zobrazení.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **AddMessage** pro název zobrazení, vyberte **přidat**.

1. Otevřete `AddMessage.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **přidat zprávu** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Přidat zprávu](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Dva oddíly - [přečte zprávu z fronty bez odebráním](#read-a-message-from-a-queue-without-removing-it) a [pro čtení a odebere zprávu z fronty](#read-and-remove-a-message-from-a-queue) -ukazují, jak číst zprávy z fronty.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Přečte zprávu z fronty bez odebráním

Tato část ukazuje postup prohlížet zprávu ve frontě (první zprávu přečíst bez odebráním).  

> [!NOTE]
> 
> V této části předpokládá, že jste dokončili postup [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **PeekMessage** , který vrací **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **PeekMessage** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který reprezentuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.PeekMessage** metoda číst první zprávu ve frontě bez odebere ji z fronty. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aktualizace **ViewBag** se dvěma hodnotami: název fronty a zprávy, která byla načtena. **CloudQueueMessage** objekt poskytuje dvě vlastnosti pro získání hodnota objektu: **CloudQueueMessage.AsBytes** a **CloudQueueMessage.AsString**. **AsString** (používá se v tomto příkladu) vrátí řetězec, zatímco **AsBytes** vrátí pole bajtů.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **PeekMessage** pro název zobrazení, vyberte **přidat**.

1. Otevřete `PeekMessage.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "PeekMessage";
    }
    
    <h2>Peek Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Peeked Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>    
    ```

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **funkce Náhled zpráva** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Prohlížení zpráv](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Přečtěte si a odebrání zprávy z fronty

V této části se dozvíte, jak ke čtení a odebrání zprávy z fronty.   

> [!NOTE]
> 
> V této části předpokládá, že jste dokončili postup [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **ReadMessage** , který vrací **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **ReadMessage** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který reprezentuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.GetMessage** metoda první zprávu ve frontě číst. **CloudQueue.GetMessage** metoda usnadňuje zpráva neviditelná pro jakýkoli jiný kód čtení zpráv tak, aby žádný jiný kód, můžete upravit nebo odstranit zpráva při vaší zpracování, je 30 sekund (ve výchozím nastavení). Chcete-li změnit dobu zpráva je neviditelná, změňte **visibilityTimeout** parametr předána **CloudQueue.GetMessage** metoda.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Volání **CloudQueueMessage.Delete** metoda odstranění zprávy z fronty.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aktualizace **ViewBag** zprávu odstranit a s názvem fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **ReadMessage** pro název zobrazení, vyberte **přidat**.

1. Otevřete `ReadMessage.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "ReadMessage";
    }
    
    <h2>Read Message results</h2>
    
    <table border="1">
        <tr><th>Queue</th><th>Read (and Deleted) Message</th></tr>
        <tr><td>@ViewBag.QueueName</td><td>@ViewBag.Message</td></tr>
    </table>
    ```

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **pro čtení nebo odstranění zprávy** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Čtení a odstraňování zpráv](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Získání délky fronty

Tato část ukazuje postup získání délky fronty (počet zpráv). 

> [!NOTE]
> 
> V této části předpokládá, že jste dokončili postup [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **GetQueueLength** , který vrací **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **ReadMessage** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který reprezentuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.FetchAttributes** metoda načtení atributů fronty (včetně jeho délky). 

    ```csharp
    queue.FetchAttributes();
    ```

6. Přístup **CloudQueue.ApproximateMessageCount** vlastnost k získání délky fronty.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aktualizace **ViewBag** s názvem fronty a jeho délka.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **GetQueueLength** pro název zobrazení, vyberte **přidat**.

1. Otevřete `GetQueueLengthMessage.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **získat délka fronty** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Získání délky fronty](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Odstranění fronty
Tato část ukazuje postup odstranění fronty. 

> [!NOTE]
> 
> V této části předpokládá, že jste dokončili postup [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **DeleteQueue** , který vrací **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **DeleteQueue** metody get **CloudStorageAccount** objekt, který reprezentuje informace o účtu úložiště. Použít následující kód k získání připojovacího řetězce úložiště a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který reprezentuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.Delete** metoda odstranění fronty reprezentována **CloudQueue** objektu.

    ```csharp
    queue.Delete();
    ```

1. Aktualizace **ViewBag** s názvem fronty a jeho délka.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. V **Průzkumníku řešení**, rozbalte **zobrazení** složku, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **DeleteQueue** pro název zobrazení, vyberte **přidat**.

1. Otevřete `DeleteQueue.cshtml`a upravit ho tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. V **Průzkumníku řešení**, rozbalte **-zobrazení > sdílené** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **získat délka fronty** a zobrazte výsledky podobné následujícím snímku obrazovky:
  
    ![Odstranit frontu](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Další postup
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s Azure blob storage a Visual Studio připojené služby (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s Azure table storage a Visual Studio připojené služby (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
