---
title: Začínáme s Azure queue storage a Visual Studio připojené služby (ASP.NET) | Dokumentace Microsoftu
description: Jak začít pracovat pomocí Azure queue storage v projektu aplikace ASP.NET v sadě Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
services: storage
author: ghogen
manager: douge
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ms.openlocfilehash: a114833b40fe834aa0108d1a2f3821f0b7cbddbc
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321848"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s Azure queue storage a Visual Studio připojené služby (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

Úložiště Azure queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Tento kurz ukazuje, jak napsat kód technologie ASP.NET pro některé běžné scénáře pomocí Azure queue storage entit. Mezi tyto scénáře patří běžné úkoly, jako je například vytváření fronty služby Azure a přidání, úpravy, čtení a odstranění fronty zpráv.

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvořit kontroler MVC 

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na **řadiče**a v místní nabídce vyberte **Přidat -> řadiče**.

    ![Přidání kontroleru aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. Na **přidat vygenerované uživatelské rozhraní** dialogového okna, vyberte **kontroler MVC 5 – prázdný**a vyberte **přidat**.

    ![Zadejte typ kontroleru MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. Na **přidat kontroler** dialogového okna, názvu kontroleru *QueuesController*a vyberte **přidat**.

    ![Název kontroleru MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Přidejte následující *pomocí* direktivy `QueuesController.cs` souboru:

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
> Této části se předpokládá dokončení kroků [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`. 

1. Přidat metodu nazvanou **CreateQueue** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **CreateQueue** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Získání **CloudQueue** objekt, který představuje odkaz na požadovaný název. **CloudQueueClient.GetQueueReference** metoda neprovede požadavek fronty úložiště. Odkaz se vrátí, zda fronta existuje. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.CreateIfNotExists** metodu pro vytvoření fronty, pokud ještě neexistuje. **CloudQueue.CreateIfNotExists** vrátí metoda **true** Pokud fronta neexistuje a byl úspěšně vytvořen. V opačném případě **false** je vrácena.    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aktualizace **objekt ViewBag** s názvem fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **CreateQueue** názvu zobrazení a vyberte **přidat**.

1. Otevřít `CreateQueue.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **vytvořit frontu** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Vytvořit frontu](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak už bylo zmíněno dříve, **CloudQueue.CreateIfNotExists** vrátí metoda **true** pouze když neexistuje a vytvoření fronty. Proto při spuštění aplikace, fronta existuje, metoda vrátí **false**. Ke spuštění aplikace více než jednou, je nutné odstranit fronty před spuštěním aplikace. Odstranění fronty, můžete to udělat pomocí **CloudQueue.Delete** metody. Můžete také odstranit pomocí fronty [webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) nebo [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Jakmile [vytvořili frontu](#create-a-queue), můžete taky přidat zprávy do této fronty. Tato část popisuje přidání zprávy do fronty *zkušební fronty*. 

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidat metodu nazvanou **AddMessage** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **AddMessage** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Vytvořte **CloudQueueMessage** objekt představující zprávu, kterou chcete přidat do fronty. A **CloudQueueMessage** objekt můžete vytvořit z řetězce (ve formátu UTF-8) nebo s polem bajtů.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Volání **CloudQueue.AddMessage** způsob, jak přidat messaged do fronty.

    ```csharp
    queue.AddMessage(message);
    ```

1. Vytvořte a nastavte pár **objekt ViewBag** vlastnosti zobrazit v zobrazení.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **AddMessage** názvu zobrazení a vyberte **přidat**.

1. Otevřít `AddMessage.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **přidat zprávu** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Přidat zprávu](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Dva oddíly - [čtení zprávy z fronty, ale neodebrali.](#read-a-message-from-a-queue-without-removing-it) a [pro čtení a odebrání zprávy z fronty](#read-and-remove-a-message-from-a-queue) -ukazují, jak číst zprávy z fronty.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Čtení zprávy z fronty, ale neodebrali.

Tato část ukazuje, jak prohlížet zprávy ve frontě (první zprávu přečíst ale neodebrali.).  

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidat metodu nazvanou **PeekMessage** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **PeekMessage** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.PeekMessage** metodu za účelem čtení bez odebrání z fronty první zprávu ve frontě. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aktualizace **objekt ViewBag** pomocí dvou hodnot: název fronty a zpráva, která byla načtena. **CloudQueueMessage** objekt poskytuje dvě vlastnosti pro získání hodnoty objektu: **CloudQueueMessage.AsBytes** a **CloudQueueMessage.AsString**. **AsString** (používá se v tomto příkladu) vrátí řetězec, zatímco **AsBytes** vrátí pole bajtů.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **PeekMessage** názvu zobrazení a vyberte **přidat**.

1. Otevřít `PeekMessage.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **Peek message** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Náhled zprávy](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Čtení a odebrání zprávy z fronty

V této části se dozvíte, jak číst a odebere zprávu z fronty.   

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidat metodu nazvanou **ReadMessage** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **ReadMessage** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.GetMessage** metodu za účelem čtení první zprávu ve frontě. **CloudQueue.GetMessage** metoda provádí zprávy neviditelná po dobu 30 sekund (ve výchozím nastavení) pro jakýkoli jiný kód tak, aby žádný kód můžete upravit nebo odstranit zprávy při vaší zpracování čtení zpráv. Chcete-li změnit dobu zprávy je neviditelné, upravte **visibilityTimeout** parametr předávaný do **CloudQueue.GetMessage** metody.

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Volání **CloudQueueMessage.Delete** metodu k odstranění zprávy z fronty.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aktualizace **objekt ViewBag** zprávu odstranit a s názvem fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **ReadMessage** názvu zobrazení a vyberte **přidat**.

1. Otevřít `ReadMessage.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **pro čtení nebo odstranění zprávy** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Čtení a odstranění zprávy](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Získání délky fronty

Tato část ukazuje, jak získat délku fronty (počet zpráv). 

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidat metodu nazvanou **GetQueueLength** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **ReadMessage** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.FetchAttributes** metodu pro načtení atributů fronty (včetně jeho délky). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Přístup **CloudQueue.ApproximateMessageCount** vlastnost k získání délky fronty.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aktualizace **objekt ViewBag** názvem fronty a jeho délka.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **GetQueueLength** názvu zobrazení a vyberte **přidat**.

1. Otevřít `GetQueueLengthMessage.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **získání délky fronty** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Získání délky fronty](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Odstranění fronty
Tato část ukazuje, jak se odstranění fronty. 

> [!NOTE]
> 
> Této části se předpokládá dokončení kroků [nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidat metodu nazvanou **DeleteQueue** , který vrátí **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **DeleteQueue** metoda, získání **CloudStorageAccount** objekt reprezentující informace o vašem účtu úložiště. Použijte následující kód k získání připojovacího řetězce k úložišti a informace o účtu úložiště z konfigurace služby Azure: (Změna  *&lt;název účtu úložiště >* k názvu účtu úložiště Azure jste přístup.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání **CloudQueueClient** objekt představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získání **CloudQueueContainer** objekt, který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.Delete** metoda Odstranit frontu reprezentována **CloudQueue** objektu.

    ```csharp
    queue.Delete();
    ```

1. Aktualizace **objekt ViewBag** názvem fronty a jeho délka.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. V **Průzkumníka řešení**, rozbalte **zobrazení** složky, klikněte pravým tlačítkem na **fronty**a v místní nabídce vyberte **Přidat -> zobrazení**.

1. Na **přidat zobrazení** dialogové okno, zadejte **DeleteQueue** názvu zobrazení a vyberte **přidat**.

1. Otevřít `DeleteQueue.cshtml`a upravit ji tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. V **Průzkumníka řešení**, rozbalte **zobrazení -> sdílený** složky a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**, přidejte následující **Html.ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **získání délky fronty** zobrazíte výsledky podobně jako na následujícím snímku obrazovky:
  
    ![Odstranit frontu](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Další postup
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s úložištěm objektů blob v Azure a Visual Studio připojené služby (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s Azure table storage a Visual Studio připojené služby (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
