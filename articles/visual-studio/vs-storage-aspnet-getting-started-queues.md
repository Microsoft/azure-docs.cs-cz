---
title: Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (ASP.NET) | Microsoft Docs
description: Jak začít používat úložiště Azure Queue v projektu ASP.NET v aplikaci Visual Studio po připojení k účtu úložiště pomocí připojených služeb sady Visual Studio
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
ms.openlocfilehash: 19cf2dd912968d0a5df8743c1e720776b8a949f0
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515980"
---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Začínáme s Azure Queue Storage a připojenými službami sady Visual Studio (ASP.NET)

[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

Azure Queue Storage poskytuje cloudové zprávy mezi součástmi aplikace. Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

V tomto kurzu se dozvíte, jak napsat kód ASP.NET pro některé běžné scénáře s využitím entit Azure Queue Storage. Mezi tyto scénáře patří běžné úkoly, jako je vytváření fronty Azure, přidávání, úpravy, čtení a odebírání zpráv fronty.

## <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Účet služby Azure Storage](../storage/common/storage-quickstart-create-account.md)

[!INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/vs-storage-aspnet-getting-started-create-azure-account.md)]

[!INCLUDE [storage-development-environment-include](../../includes/vs-storage-aspnet-getting-started-setup-dev-env.md)]

### <a name="create-an-mvc-controller"></a>Vytvoření kontroleru MVC 

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na **řadiče**a v místní nabídce vyberte možnost **Přidat kontroler >** .

    ![Přidání kontroleru do aplikace ASP.NET MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-menu.png)

1. V dialogovém okně **Přidat generování uživatelského rozhraní** vyberte možnost kontroler **MVC 5 – prázdné**a vyberte **Přidat**.

    ![Zadat typ kontroleru MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller.png)

1. V dialogovém okně **Přidat řadič** pojmenujte kontrolér *QueuesController*a vyberte **Přidat**.

    ![Pojmenování kontroleru MVC](./media/vs-storage-aspnet-getting-started-queues/add-controller-name.png)

1. Do`QueuesController.cs` souboru přidejte následující direktivy *using* :

    ```csharp
    using Microsoft.Azure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;
    ```
   ## <a name="create-a-queue"></a>Vytvoření fronty

Následující postup ukazuje, jak vytvořit frontu:

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`. 

1. Přidejte metodu s názvem **CreateQueue** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult CreateQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```

1. V rámci metody **CreateQueue** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód: (Změňte  *&lt;název účtu úložiště >* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Získání objektu **CloudQueueClient** představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```
1. Získejte objekt **CloudQueue** , který představuje odkaz na požadovaný název fronty. Metoda **CloudQueueClient. GetQueueReference** neprovádí požadavek na úložiště front. Odkaz je vrácen bez ohledu na to, zda fronta existuje nebo ne. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chcete-li vytvořit frontu, pokud ještě neexistuje, zavolejte metodu **CloudQueue. CreateIfNotExists** . Metoda **CloudQueue. CreateIfNotExists** vrátí **hodnotu true** , pokud fronta neexistuje a je úspěšně vytvořena. V opačném případě se vrátí **hodnota false** .    

    ```csharp
    ViewBag.Success = queue.CreateIfNotExists();
    ```

1. Aktualizujte **ViewBag** s názvem fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte možnost **Přidat zobrazení >** .

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **CreateQueue** a vyberte **Přidat**.

1. Otevřete `CreateQueue.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Create Queue";
    }
    
    <h2>Create Queue results</h2>

    Creation of @ViewBag.QueueName @(ViewBag.Success == true ? "succeeded" : "failed")
    ```

1. V **Průzkumník řešení**rozbalte položku **Zobrazení-> Sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Create queue", "CreateQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **vytvořit frontu** , aby se zobrazily podobné výsledky jako na následujícím snímku obrazovky:
  
    ![Vytvořit frontu](./media/vs-storage-aspnet-getting-started-queues/create-queue-results.png)

    Jak bylo zmíněno dříve, metoda **CloudQueue. CreateIfNotExists** vrátí **hodnotu true** pouze v případě, že fronta neexistuje a je vytvořena. Proto pokud aplikaci spustíte v případě existence fronty, vrátí metoda **hodnotu false**. Pokud chcete aplikaci spustit několikrát, musíte před spuštěním aplikace tuto frontu odstranit. Odstranění fronty lze provést pomocí metody **CloudQueue. Delete** . Frontu můžete také odstranit pomocí [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) nebo [Průzkumník služby Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md).  

## <a name="add-a-message-to-a-queue"></a>Přidat zprávu do fronty

Po [vytvoření fronty](#create-a-queue)můžete do této fronty přidat zprávy. Tato část vás provede přidáním zprávy do *fronty test-fronta*. 

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **AddMessage** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult AddMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci metody **AddMessage** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód: (Změňte  *&lt;název účtu úložiště >* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání objektu **CloudQueueClient** představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer** , který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Vytvořte objekt **CloudQueueMessage** reprezentující zprávu, kterou chcete přidat do fronty. Objekt **CloudQueueMessage** lze vytvořit buď z řetězce (ve formátu UTF-8), nebo pole bajtů.

    ```csharp
    CloudQueueMessage message = new CloudQueueMessage("Hello, Azure Queue Storage");
    ```

1. Chcete-li přidat zprávu do fronty, zavolejte metodu **CloudQueue. AddMessage** .

    ```csharp
    queue.AddMessage(message);
    ```

1. Vytvořte a nastavte několik vlastností **ViewBag** pro zobrazení v zobrazení.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte možnost **Přidat zobrazení >** .

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **AddMessage** a vyberte **Přidat**.

1. Otevřete `AddMessage.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "Add Message";
    }
    
    <h2>Add Message results</h2>
    
    The message '@ViewBag.Message' was added to the queue '@ViewBag.QueueName'.
    ```

1. V **Průzkumník řešení**rozbalte položku **Zobrazení-> Sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Add message", "AddMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a výběrem **přidat zprávu** zobrazíte podobné výsledky jako na následujícím snímku obrazovky:
  
    ![Přidat zprávu](./media/vs-storage-aspnet-getting-started-queues/add-message-results.png)

Tyto dvě části- [přečtou zprávu z fronty, aniž byste ji odebrali](#read-a-message-from-a-queue-without-removing-it) a načetli [a odebrali zprávu z](#read-and-remove-a-message-from-a-queue) fronty – ukazují, jak číst zprávy z fronty.    

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Přečtěte si zprávu z fronty bez jejího odebrání.

Tato část ukazuje, jak prohlížet zprávy ve frontě (Přečtěte si první zprávu, aniž byste ji odebrali).  

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **PeekMessage** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult PeekMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci metody **PeekMessage** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód: (Změňte  *&lt;název účtu úložiště >* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání objektu **CloudQueueClient** představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer** , který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Voláním metody **CloudQueue. PeekMessage** si přečtěte první zprávu ve frontě, aniž byste ji odebrali z fronty. 

    ```csharp
    CloudQueueMessage message = queue.PeekMessage();
    ```

1. Aktualizujte **ViewBag** o dvě hodnoty: název fronty a zprávu, která byla přečtena. Objekt **CloudQueueMessage** zpřístupňuje dvě vlastnosti pro získání hodnoty objektu: **CloudQueueMessage. AsBytes** a **CloudQueueMessage. AsString**. **AsString** (použitý v tomto příkladu) vrací řetězec, zatímco **AsBytes** vrací bajtové pole.

    ```csharp
    ViewBag.QueueName = queue.Name; 
    ViewBag.Message = (message != null ? message.AsString : "");
    ```

1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte možnost **Přidat zobrazení >** .

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **PeekMessage** a vyberte **Přidat**.

1. Otevřete `PeekMessage.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumník řešení**rozbalte položku **Zobrazení-> Sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Peek message", "PeekMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte možnost **prohlížet zprávu** a podívejte se, jaké výsledky budou vypadat podobně jako na následujícím snímku obrazovky:
  
    ![Prohlížet zprávu](./media/vs-storage-aspnet-getting-started-queues/peek-message-results.png)

## <a name="read-and-remove-a-message-from-a-queue"></a>Čtení a odebrání zprávy z fronty

V této části se dozvíte, jak číst a odebírat zprávy z fronty.   

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **ReadMessage** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult ReadMessage()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci metody **ReadMessage** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód: (Změňte  *&lt;název účtu úložiště >* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání objektu **CloudQueueClient** představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer** , který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Voláním metody **CloudQueue. GetMessage** si přečtěte první zprávu ve frontě. Metoda **CloudQueue. GetMessage** zpřístupňuje zprávu po dobu 30 sekund (ve výchozím nastavení) ostatním kódu, aby se zprávy nezměnily, takže žádný jiný kód nemůže upravovat ani odstraňovat zprávy při jejich zpracování. Chcete-li změnit dobu, po kterou je zpráva neviditelná, upravte parametr **visibilityTimeout** předaný metodě **CloudQueue. GetMessage** .

    ```csharp
    // This message will be invisible to other code for 30 seconds.
    CloudQueueMessage message = queue.GetMessage();     
    ```

1. Chcete-li odstranit zprávu z fronty, zavolejte metodu **CloudQueueMessage. Delete** .

    ```csharp
    queue.DeleteMessage(message);
    ```

1. Aktualizujte **ViewBag** pomocí odstraněné zprávy a názvu fronty.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Message = message.AsString;
    ```
 
1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte možnost **Přidat zobrazení >** .

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **ReadMessage** a vyberte **Přidat**.

1. Otevřete `ReadMessage.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

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

1. V **Průzkumník řešení**rozbalte položku **Zobrazení-> Sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Read/Delete message", "ReadMessage", "Queues")</li>
    ```

1. Spusťte aplikaci a výběrem Zobrazit **/odstranit zprávu** zobrazíte výsledky podobné následujícímu snímku obrazovky:
  
    ![Čtení a odstranění zprávy](./media/vs-storage-aspnet-getting-started-queues/read-message-results.png)

## <a name="get-the-queue-length"></a>Získání délky fronty

Tato část ukazuje, jak získat délku fronty (počet zpráv). 

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **GetQueueLength** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult GetQueueLength()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci metody **ReadMessage** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód: (Změňte  *&lt;název účtu úložiště >* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání objektu **CloudQueueClient** představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer** , který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Pro načtení atributů fronty (včetně její délky) zavolejte metodu **CloudQueue. FetchAttributes** . 

    ```csharp
    queue.FetchAttributes();
    ```

1. Pokud chcete získat délku fronty, přejděte k vlastnosti **CloudQueue. ApproximateMessageCount** .
 
    ```csharp
    int? nMessages = queue.ApproximateMessageCount;
    ```

1. Aktualizujte **ViewBag** s názvem fronty a její délkou.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ViewBag.Length = nMessages;
    ```
 
1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte možnost **Přidat zobrazení >** .

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **GetQueueLength** a vyberte **Přidat**.

1. Otevřete `GetQueueLengthMessage.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "GetQueueLength";
    }
    
    <h2>Get Queue Length results</h2>
    
    The queue '@ViewBag.QueueName' has a length of (number of messages): @ViewBag.Length
    ```

1. V **Průzkumník řešení**rozbalte položku **Zobrazení-> Sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Get queue length", "GetQueueLength", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **získat délku fronty** , aby se zobrazily výsledky podobné následujícímu snímku obrazovky:
  
    ![Získat délku fronty](./media/vs-storage-aspnet-getting-started-queues/get-queue-length-results.png)


## <a name="delete-a-queue"></a>Odstranění fronty
Tato část ukazuje, jak odstranit frontu. 

> [!NOTE]
> 
> V této části se předpokládá, že jste dokončili kroky [Nastavení vývojového prostředí](#set-up-the-development-environment). 

1. Otevřete soubor `QueuesController.cs`.

1. Přidejte metodu s názvem **DeleteQueue** , která vrátí **ActionResult**.

    ```csharp
    public ActionResult DeleteQueue()
    {
        // The code in this section goes here.

        return View();
    }
    ```
 
1. V rámci metody **DeleteQueue** získejte objekt **CloudStorageAccount** , který představuje informace o vašem účtu úložiště. K získání připojovacího řetězce a informací o účtu úložiště z konfigurace služby Azure použijte následující kód: (Změňte  *&lt;název účtu úložiště >* na název účtu úložiště Azure, ke kterému přistupujete.)
   
    ```csharp
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```
   
1. Získání objektu **CloudQueueClient** představuje klienta služby front.
   
    ```csharp
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
    ```

1. Získejte objekt **CloudQueueContainer** , který představuje odkaz na frontu. 
   
    ```csharp
    CloudQueue queue = queueClient.GetQueueReference("test-queue");
    ```

1. Chcete-li odstranit frontu reprezentovanou objektem **CloudQueue** , zavolejte metodu **CloudQueue. Delete** .

    ```csharp
    queue.Delete();
    ```

1. Aktualizujte **ViewBag** s názvem fronty a její délkou.

    ```csharp
    ViewBag.QueueName = queue.Name;
    ```
 
1. V **Průzkumník řešení**rozbalte složku **zobrazení** , klikněte pravým tlačítkem myši na **fronty**a v místní nabídce vyberte možnost **Přidat zobrazení >** .

1. V dialogovém okně **Přidat zobrazení** jako název zobrazení zadejte **DeleteQueue** a vyberte **Přidat**.

1. Otevřete `DeleteQueue.cshtml`a upravte jej tak, aby vypadal jako následující fragment kódu:

    ```csharp
    @{
        ViewBag.Title = "DeleteQueue";
    }
    
    <h2>Delete Queue results</h2>
    
    @ViewBag.QueueName deleted.
    ```

1. V **Průzkumník řešení**rozbalte položku **Zobrazení-> Sdílená** složka a otevřete `_Layout.cshtml`.

1. Za poslední **HTML. ActionLink**přidejte následující **HTML. ActionLink**:

    ```html
    <li>@Html.ActionLink("Delete queue", "DeleteQueue", "Queues")</li>
    ```

1. Spusťte aplikaci a vyberte **získat délku fronty** , aby se zobrazily výsledky podobné následujícímu snímku obrazovky:
  
    ![Odstranit frontu](./media/vs-storage-aspnet-getting-started-queues/delete-queue-results.png)

## <a name="next-steps"></a>Další kroky
Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.

  * [Začínáme se službou Azure Blob Storage a připojenými službami sady Visual Studio (ASP.NET)](../storage/vs-storage-aspnet-getting-started-blobs.md)
  * [Začínáme s Azure Table Storage a připojenými službami sady Visual Studio (ASP.NET)](vs-storage-aspnet-getting-started-tables.md)
