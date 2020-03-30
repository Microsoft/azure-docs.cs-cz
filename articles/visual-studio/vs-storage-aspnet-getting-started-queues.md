---
title: Začínáme s úložištěm front Azure pomocí Visual Studia (ASP.NET)
description: Jak začít používat úložiště front Azure v projektu ASP.NET v sadě Visual Studio po připojení k účtu úložiště pomocí služby Visual Studio Connected Services
services: storage
author: ghogen
manager: jillfra
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/23/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: f229661ca78dc75adbc0b49073dc6f0feaf2ba22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980755"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s úložištěm front Azure a připojenými službami Visual Studia (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

Úložiště front Azure poskytuje cloudové zasílání zpráv mezi součástmi aplikací. Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

Tento kurz ukazuje, jak psát ASP.NET kód pro některé běžné scénáře pomocí entit úložiště fronty Azure. Tyto scénáře zahrnují běžné úkoly, jako je například vytvoření fronty Azure a přidávání, úpravy, čtení a odebírání zpráv fronty.

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-account-create.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvoření řadiče MVC 

1. V **Průzkumníku řešení**klepněte pravým tlačítkem myši na **řadiče**a v místní nabídce vyberte **položku Správce >doplňků**.

    ![Přidání ovladače do aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. V dialogovém okně **Přidat kancovací okno** vyberte **MVC 5 Controller - Empty**a vyberte **Přidat**.

    ![Zadejte typ řadiče MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. V dialogovém okně **Přidat řadič** pojmenujte řadič *QueuesController*a vyberte **Přidat**.

    ![Pojmenování řadiče MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Do souboru přidejte `QueuesController.cs` následující *příkazy pomocí* direktiv:

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Vytvoření fronty

Následující kroky ilustrují, jak vytvořit frontu:

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`. 

1. Přidejte metodu s názvem **CreateQueue,** která vrací **hodnotu ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci **CreateQueue** metoda získat **Objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získat **Objekt CloudQueueClient** představuje klienta služby fronty.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Získejte **Objekt CloudQueue,** který představuje odkaz na požadovaný název fronty. Metoda **CloudQueueClient.GetQueueReference** neprovádí požadavek proti úložišti fronty. Odkaz je vrácena bez ohledu na to, zda fronta existuje. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.CreateIfNotExists** metoda k vytvoření fronty, pokud ještě neexistuje. Metoda **CloudQueue.CreateIfNotExists** vrátí **hodnotu true,** pokud fronta neexistuje a je úspěšně vytvořena. V opačném případě je **vrácena false.**    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aktualizujte **ViewBag** s názvem fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte **položku >Zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení příkaz **CreateQueue** a vyberte **Přidat**.

1. Otevřete `CreateQueue.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Vytvořit frontu** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Vytvořit frontu](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak již bylo zmíněno dříve, **Metoda CloudQueue.CreateIfNotExists** vrátí **hodnotu true** pouze v případě, že fronta neexistuje a je vytvořena. Proto pokud spustíte aplikaci, když existuje fronta, metoda vrátí **false**. Chcete-li aplikaci spustit vícekrát, musíte frontu před znovu spuštěním odstranit. Odstranění fronty lze provést pomocí **metody CloudQueue.Delete.** Frontu můžete také odstranit pomocí [portálu Azure nebo](https://go.microsoft.com/fwlink/p/?LinkID=525040) [Průzkumníka úložiště Microsoft Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Přidání zprávy do fronty

Po vytvoření [fronty](#create-a-queue)můžete do této fronty přidat zprávy. Tato část vás provede přidáním zprávy do *fronty test fronty*. 

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **AddMessage,** která vrací **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **AddMessage** metoda získat **objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získat **Objekt CloudQueueClient** představuje klienta služby fronty.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer,** který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Vytvořte objekt **CloudQueueMessage** představující zprávu, kterou chcete přidat do fronty. Objekt **CloudQueueMessage** lze vytvořit buď z řetězce (ve formátu UTF-8) nebo bajtového pole.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Volání **CloudQueue.AddMessage** metoda přidat zprávy do fronty.

    ```csharp
    queue.AddMessage(message);
    ```

1. Vytvořte a nastavte několik vlastností **ViewBag** pro zobrazení v zobrazení.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte **položku >Zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte **addmessage** pro název zobrazení a vyberte **Přidat**.

1. Otevřete `AddMessage.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Přidat zprávu** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Přidání zprávy](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Tyto dvě části – [Čtení zprávy z fronty bez odebrání](#read-a-message-from-a-queue-without-removing-it) a čtení a odebrání zprávy z [fronty](#read-and-remove-a-message-from-a-queue) – ilustrují, jak číst zprávy z fronty.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Čtení zprávy z fronty bez její odebrání

Tato část ukazuje, jak nahlédnout na zprávu ve frontě (přečtěte si první zprávu bez odebrání).  

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **PeekMessage,** která vrací **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **PeekMessage** metoda získat **objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získat **Objekt CloudQueueClient** představuje klienta služby fronty.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer,** který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.PeekMessage** metoda číst první zprávu ve frontě bez odebrání z fronty. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aktualizujte **ViewBag** se dvěma hodnotami: název fronty a zprávy, která byla přečtena. Objekt **CloudQueueMessage** zpřístupňuje dvě vlastnosti pro získání hodnoty **objektu: CloudQueueMessage.AsBytes** a **CloudQueueMessage.AsString**. **AsString** (použitý v tomto příkladu) vrátí řetězec, zatímco **AsBytes** vrátí bajtové pole.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte **položku >Zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte **peekmessage** pro název zobrazení a vyberte **Přidat**.

1. Otevřete `PeekMessage.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a **výběrem možnosti Náhled zprávy** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Náhled zprávy](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Čtení a odebrání zprávy z fronty

V této části se dozvíte, jak číst a odebírat zprávu z fronty.   

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **ReadMessage,** která vrací **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **ReadMessage** metoda získat **objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získat **Objekt CloudQueueClient** představuje klienta služby fronty.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer,** který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.GetMessage** metoda číst první zprávu ve frontě. **Metoda CloudQueue.GetMessage** zneviditelní zprávu po dobu 30 sekund (ve výchozím nastavení) pro jakýkoli jiný kód čtení zpráv tak, aby žádný jiný kód můžete upravit nebo odstranit zprávu při zpracování. Chcete-li změnit dobu, po kterou je zpráva neviditelná, upravte parametr **visibilityTimeout** předaný metodě **CloudQueue.GetMessage.**

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Volání **CloudQueueMessage.Delete** metoda odstranit zprávu z fronty.

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aktualizujte **ViewBag** s odstraněnou zprávou a název fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte **položku >Zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte **readmessage** pro název zobrazení a vyberte **Přidat**.

1. Otevřete `ReadMessage.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Číst nebo odstranit zprávu** zobrazíte výsledky podobné následujícímu snímku obrazovky:
  
    ![Čtení a odstranění zprávy](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Získání délky fronty

Tato část ukazuje, jak získat délku fronty (počet zpráv). 

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **GetQueueLength,** která vrací **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **ReadMessage** metoda získat **objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získat **Objekt CloudQueueClient** představuje klienta služby fronty.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer,** který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.FetchAttributes** metoda načíst atributy fronty (včetně jeho délky). 

    ```csharp
    queue.FetchAttributes();
    ```

1. Přístup **CloudQueue.ApproximateMessageCount** vlastnost získat délku fronty.
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aktualizujte **ViewBag** s názvem fronty a jeho délka.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte **položku >Zobrazení**.

1. V dialogovém okně **Přidat zobrazení** **zadejte** pro název zobrazení délku zobrazení a vyberte **Přidat**.

1. Otevřete `GetQueueLengthMessage.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Získat délku fronty** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Získat délku fronty](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Odstranění fronty
Tato část ukazuje, jak odstranit frontu. 

> [!NOTE]
> 
> Tato část předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **DeleteQueue,** která vrací **hodnotu ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci **DeleteQueue** metoda získat **objekt CloudStorageAccount,** který představuje informace o účtu úložiště. Pomocí následujícího kódu získáte informace o připojovacím řetězci * &lt;* úložiště a účtu úložiště z konfigurace služby Azure: (Změňte>název účtu úložiště na název účtu úložiště Azure, ke které přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získat **Objekt CloudQueueClient** představuje klienta služby fronty.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer,** který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Volání **CloudQueue.Delete** metoda odstranit frontu reprezentovanou **Objekt CloudQueue.**

    ```csharp
    queue.Delete();
    ```

1. Aktualizujte **ViewBag** s názvem fronty a jeho délka.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. V **Průzkumníku řešení**rozbalte složku **Zobrazení,** klepněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte **položku >Zobrazení**.

1. V dialogovém okně **Přidat zobrazení** zadejte pro název zobrazení položku **DeleteQueue** a vyberte **Přidat**.

1. Otevřete `DeleteQueue.cshtml`a upravte tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. V **Průzkumníku řešení**rozbalte složku **Sdílená zobrazení >** a otevřete `_Layout.cshtml`.

1. Za poslední **Html.ActionLink**přidejte následující **html.actionlink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a výběrem **možnosti Získat délku fronty** zobrazte výsledky podobné následujícímu snímku obrazovky:
  
    ![Odstranit frontu](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Další kroky
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme s úložištěm objektů blob Azure a připojenými službami Visual Studia (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s úložištěm tabulek Azure a připojenými službami Visual Studia (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
