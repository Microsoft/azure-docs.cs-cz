---
title: Architektura nabízených oznámení Notification Hubs Enterprise
description: Další informace o používání Azure Notification Hubs v podnikovém prostředí
services: notification-hubs
documentationcenter: ''
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 018315b7ed468e24fb922337848d14703ffdcd4d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003622"
---
# <a name="enterprise-push-architectural-guidance"></a>Doprovodné materiály k architektuře nabízení v podnicích

Podniky v současné době se postupně pohybují k vytváření mobilních aplikací pro koncové uživatele (externí) nebo pro zaměstnance (interní). Mají stávající back-endové systémy na místě IT sálové nebo některé obchodní aplikace, které musí být integrovány do architektury mobilní aplikace. V této příručce se dozvíte, jak nejlépe provést tuto integraci, aby bylo možné řešení doporučit běžným scénářům.

Častým požadavkem je odesílat nabízená oznámení uživatelům prostřednictvím své mobilní aplikace, když dojde k nějakému zájmu v back-end systémech. Například zákazník banky, který má bankovní aplikaci bank na iPhonu, chce upozornit, když se na účet navýší debet nad určitou částku z účtu nebo intranetového scénáře, kdy zaměstnanec z finančního oddělení, který má aplikaci pro schvalování rozpočtu na Windows Phone, chce oznámit přijetí žádosti o schválení.

Bankovní účet nebo zpracování schválení se pravděpodobně provede v některém back-endu systému, který musí iniciovat uživateli nabízené oznámení. Může existovat několik takových back-end systémů, které musí všechny sestavovat stejný druh logiky, aby bylo možné je odeslat, když událost aktivuje oznámení. Složitost zde spočívá v integraci několika back-end systémů spolu s jediným systémem nabízených oznámení, kde se koncoví uživatelé můžou přihlásit k odběru různých oznámení a můžou existovat i několik mobilních aplikací. Například intranetové mobilní aplikace, ve kterých jedna mobilní aplikace může chtít dostávat oznámení z několika takových back-end systémů. Systémy back-end neznají ani nepotřebují znát sémantiku a technologii nabízených oznámení, takže běžné řešení je tradičně zavedlo k zavedení komponenty, která se dotáže back-endové systémy na všechny události, které vás zajímají, a zodpovídá za odeslání nabízených zpráv klientovi.

Lepším řešením je použití modelu Azure Service Bus-téma/předplatného, což snižuje složitost a zároveň usnadňuje škálovatelné řešení.

Tady je obecná Architektura řešení (generalizovaná s více mobilními aplikacemi, ale i v případě, že je k dispozici jenom jedna mobilní aplikace).

## <a name="architecture"></a>Architektura

![Diagram podnikové architektury znázorňující tok prostřednictvím událostí, předplatných a nabízených zpráv.][1]

Klíčový kámen v tomto diagramu architektury je Azure Service Bus, který poskytuje témata nebo programovací model předplatných (Další informace najdete v tématu [Service Bus publikování do Pub/sub]). Příjemce, což je v tomto případě mobilní back-end (obvykle [mobilní služba Azure], která iniciuje nabízení oznámení mobilním aplikacím), nepřijímá zprávy přímo ze systémů back-end, ale místo toho poskytuje zprostředkující vrstvu abstrakce poskytovanou [Azure Service Bus], která umožňuje mobilnímu back-endu přijímat zprávy z jednoho nebo více back-end systémů. Je třeba vytvořit Service Bus téma pro každý back-end systém, například účet, HR, finance, který je v podstatě "důležité" témata, která zahájí odeslání zprávy jako nabízené oznámení. Back-endové systémy odesílají zprávy do těchto témat. Mobilní back-end se může přihlásit k odběru jednoho nebo několika takových témat vytvořením předplatného Service Bus. Umožňuje mobilnímu back-endu přijímat oznámení z odpovídajícího systému back-end. Mobilní back-end nadále naslouchá zprávám ve svých předplatných a Jakmile přijde zpráva, přepíná a odesílá oznámení do centra oznámení. Centra oznámení nakonec doručovat zprávu do mobilní aplikace. Tady je seznam klíčových součástí:

1. Back-endové systémy (LoB/starší systémy)
   * Vytvoří Service Bus téma.
   * Odesílá zprávu.
1. Mobilní back-end
   * Vytvoří předplatné služby.
   * Přijímá zprávu (ze systému back-end)
   * Odesílá oznámení klientům (prostřednictvím centra oznámení Azure).
1. Mobilní aplikace
   * Přijímá a zobrazuje oznámení.

### <a name="benefits"></a>Výhody

1. Oddělení mezi příjemcem (mobilní aplikace/služba prostřednictvím centra oznámení) a odesilatelem (back-end systémy) umožňuje integraci dalších back-end systémů s minimálními změnami.
1. Také umožňuje situaci, kdy více mobilních aplikací dokáže přijímat události z jednoho nebo více back-end systémů.  

## <a name="sample"></a>Ukázka

### <a name="prerequisites"></a>Požadavky

Dokončete následující kurzy a seznamte se s koncepty a běžnými vytvářeními & kroky konfigurace:

1. [Service Bus program pro publikování a následné] zpracování – v tomto kurzu se dozvíte, jak pomocí Service Bus témata a předplatná vytvořit obor názvů, který bude obsahovat témata nebo odběry, jak odesílat & zpráv z nich.
2. [Notification Hubs – podrobný kurz pro Windows] – v tomto kurzu se dozvíte, jak nastavit aplikaci pro Windows Store a jak používat Notification Hubs k registraci a následnému doručování oznámení.

### <a name="sample-code"></a>Ukázka kódu

Úplný vzorový kód je k dispozici v [ukázkách centra oznámení]. Je rozdělen na tři komponenty:

1. **EnterprisePushBackendSystem**

    a. Tento projekt používá balíček NuGet **windowsazure. ServiceBus** a je založen na [Service Bus program pro publikování a dílčí programování].

    b. Tato aplikace je jednoduchá Konzolová aplikace v jazyce C# pro simulaci systému LoB, která zahájí doručení zprávy do mobilní aplikace.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
            CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the topic
        CreateTopic(connectionString);

        // Send message
        SendMessage(connectionString);
    }
    ```

    c. `CreateTopic` slouží k vytvoření Service Busho tématu.

    ```csharp
    public static void CreateTopic(string connectionString)
    {
        // Create the topic if it does not exist already

        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.TopicExists(sampleTopic))
        {
            namespaceManager.CreateTopic(sampleTopic);
        }
    }
    ```

    d. `SendMessage` slouží k odeslání zpráv do tohoto Service Bus tématu. Tento kód jednoduše pošle do tématu sadu náhodných zpráv pro účely ukázky. Obvykle existuje systém back-end, který odesílá zprávy, když dojde k události.

    ```csharp
    public static void SendMessage(string connectionString)
    {
        TopicClient client =
            TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

        // Sends random messages every 10 seconds to the topic
        string[] messages =
        {
            "Employee Id '{0}' has joined.",
            "Employee Id '{0}' has left.",
            "Employee Id '{0}' has switched to a different team."
        };

        while (true)
        {
            Random rnd = new Random();
            string employeeId = rnd.Next(10000, 99999).ToString();
            string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

            // Send Notification
            BrokeredMessage message = new BrokeredMessage(notification);
            client.Send(message);

            Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

            System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
        }
    }
    ```
2. **ReceiveAndSendNotification**

    a. Tento projekt využívá balíčky NuGet *windowsazure. ServiceBus* a **Microsoft. Web. WebJobs. Publish** a vychází z [Service Busho programování]pro publikování na bázi.

    b. Následující aplikace konzoly se spouští jako [Webová úloha Azure] , protože musí běžet nepřetržitě, aby naslouchala zpráv ze systémů LOB a back-endu. Tato aplikace je součástí vašeho mobilního back-endu.

    ```csharp
    static void Main(string[] args)
    {
        string connectionString =
                 CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

        // Create the subscription that receives messages
        CreateSubscription(connectionString);

        // Receive message
        ReceiveMessageAndSendNotification(connectionString);
    }
    ```

    c. `CreateSubscription` slouží k vytvoření předplatného Service Bus pro téma, kde back-end systém odesílá zprávy. V závislosti na obchodním scénáři Tato součást vytvoří jedno nebo více předplatných odpovídajících tématům (například některé můžou přijímat zprávy ze systému HR, některé z finančních systémů a tak dále).

    ```csharp
    static void CreateSubscription(string connectionString)
    {
        // Create the subscription if it does not exist already
        var namespaceManager =
            NamespaceManager.CreateFromConnectionString(connectionString);

        if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
        {
            namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
        }
    }
    ```

    d. `ReceiveMessageAndSendNotification` se používá ke čtení zprávy z tématu pomocí svého předplatného, a pokud je čtení úspěšné, pak se vytvoří oznámení (ve vzorovém scénáři je nativní oznámení systému Windows), které se odešle do mobilní aplikace pomocí Azure Notification Hubs.

    ```csharp
    static void ReceiveMessageAndSendNotification(string connectionString)
    {
        // Initialize the Notification Hub
        string hubConnectionString = CloudConfigurationManager.GetSetting
                ("Microsoft.NotificationHub.ConnectionString");
        hub = NotificationHubClient.CreateClientFromConnectionString
                (hubConnectionString, "enterprisepushservicehub");

        SubscriptionClient Client =
            SubscriptionClient.CreateFromConnectionString
                    (connectionString, sampleTopic, sampleSubscription);

        Client.Receive();

        // Continuously process messages received from the subscription
        while (true)
        {
            BrokeredMessage message = Client.Receive();
            var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

            if (message != null)
            {
                try
                {
                    Console.WriteLine(message.MessageId);
                    Console.WriteLine(message.SequenceNumber);
                    string messageBody = message.GetBody<string>();
                    Console.WriteLine("Body: " + messageBody + "\n");

                    toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                    SendNotificationAsync(toastMessage);

                    // Remove message from subscription
                    message.Complete();
                }
                catch (Exception)
                {
                    // Indicate a problem, unlock message in subscription
                    message.Abandon();
                }
            }
        }
    }
    static async void SendNotificationAsync(string message)
    {
        await hub.SendWindowsNativeNotificationAsync(message);
    }
    ```

    e. Pokud chcete tuto aplikaci publikovat jako **webovou úlohu**, klikněte pravým tlačítkem na řešení v aplikaci Visual Studio a vyberte **Publikovat jako webovou úlohu** .

    ![Snímek obrazovky s možnostmi napravo od pravého tlačítka, která se zobrazuje s možností publikovat jako Azure WebJob, je popsaný červeně.][2]

    f. Vyberte svůj profil publikování a vytvořte nový web Azure, pokud už neexistuje, který je hostitelem této webové úlohy, a až potom web **publikujte**.

    :::image type="complex" source="./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png" alt-text="Snímek obrazovky znázorňující pracovní postup k vytvoření webu v Azure":::
    Snímek obrazovky dialogového okna Publikovat web se zvolenou možností Microsoft Azure Websites, zelená šipka ukazující na dialogové okno vybrat existující web s novou možností označenou červenou a zelenou šipkou ukazující na dialogové okno vytvořit web v Microsoft Azure s názvem webu a možnostmi popsanými červeně.
    :::image-end:::

    například Nakonfigurujte úlohu tak, aby běžela průběžně, takže když se přihlásíte k [Azure Portal] , mělo by se zobrazit něco jako v následujícím seznamu:

    ![Snímek obrazovky s portálem Azure Portal se zobrazenými WebJobs back-endu (Enterprise push back-end) a hodnotami názvů, plánů a protokolů popsaných červeně][4]

3. **EnterprisePushMobileApp**

    a. Tato aplikace je aplikace pro Windows Store, která přijímá informační zprávy z webové úlohy spuštěné jako součást mobilního back-endu a zobrazuje je. Tento kód vychází z [Notification Hubsho kurzu univerzálního pro Windows].  

    b. Ujistěte se, že je ve vaší aplikaci povolený příjem oznámení informačními zprávami.

    c. Zajistěte, aby se při spuštění aplikace volala následující registrační kód Notification Hubs (po nahrazení `HubName` `DefaultListenSharedAccessSignature` hodnot a:

    ```csharp
    private async void InitNotificationsAsync()
    {
        var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
        var result = await hub.RegisterNativeAsync(channel.Uri);

        // Displays the registration ID so you know it was successful
        if (result.RegistrationId != null)
        {
            var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }
    ```

### <a name="running-the-sample"></a>Spuštění ukázky

1. Ujistěte se, že je vaše webová úloha úspěšně spuštěná a že je naplánováno jejich nepřetržité spuštění.
2. Spusťte **EnterprisePushMobileApp**, ve kterém se spustí aplikace pro Windows Store.
3. Spusťte konzolovou aplikaci **EnterprisePushBackendSystem** , která simuluje back-end LOB a začne odesílat zprávy a měla by se zobrazit informační zpráva, která se zobrazí jako na následujícím obrázku:

    ![Snímek obrazovky s konzolou, ve které je spuštěná systémová aplikace nabízeného back-end a zpráva odeslaná aplikací][5]

4. Zprávy byly původně odeslány pro Service Bus témata, která byla monitorována Service Bus odběry ve webové úloze. Po přijetí zprávy se vytvoří oznámení a pošle se do mobilní aplikace. Můžete procházet protokoly webové úlohy a potvrdit zpracování při přechodu na odkaz protokoly v [Azure Portal] pro vaši webovou úlohu:

    ![Snímek obrazovky dialogového okna průběžné podrobnosti WebJob se zprávou, která je odeslána červeně.][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Ukázky centra oznámení]: https://github.com/Azure/azure-notificationhubs-samples
[Mobilní služba Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: ../service-bus-messaging/service-bus-messaging-overview.md
[Service Bus pro publikování a následné programování]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[Webová úloha Azure]: ../app-service/webjobs-create.md
[Notification Hubs – kurz univerzální pro Windows]: ./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[Azure Portal]: https://portal.azure.com/
