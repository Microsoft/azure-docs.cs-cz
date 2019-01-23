---
title: Notification Hubs – podniková architektura nabízených oznámení
description: Pokyny k použití v podnikovém prostředí Azure Notification Hubs
services: notification-hubs
documentationcenter: ''
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: c8e204aef8bea26394c7180a72eb8ed8f62bbdc4
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447254"
---
# <a name="enterprise-push-architectural-guidance"></a>Doprovodné materiály k architektuře nabízení v podnicích

Podniky jsou dnes postupně přesun směrem k vytvoření mobilní aplikace pro buď jejich koncoví uživatelé (externí) nebo pro zaměstnance (interní). Mají stávající back-end systémy na místě už to jsou sálové počítače nebo některé obchodní aplikace, které musí být integrován do architektury mobilních aplikací. Tato příručka pojednává o jak vhodné provést tuto integraci, doporučujeme z možných řešení pro běžné scénáře.

Časté požadavek je pro zasílání nabízených oznámení uživatelům prostřednictvím jejich mobilní aplikace při výskytu události zájmu v back-endovým systémům. například zákazník banky, který má banky bankovnictví aplikaci na Iphonu chce být informován při MD nad určitou dobu z účtu nebo scénáři intranetu, ve kterém chce, aby zaměstnance z finančního oddělení, který má aplikace schválení rozpočtu na Windows Phone  upozornění, až je přijata žádost o schválení.

Bankovním účtu nebo schválení zpracování bude pravděpodobně třeba provést v některých back-end systém, který musí inicializovat nabízených oznámení pro uživatele. Může existovat více tyto back-end systémy, které musíte sestavit stejný druh logiky nabídne, jakmile se událost se aktivuje upozornění. Složitost zde spočívá v integraci několika back-endových systémů spolu s jeden nabízených systému, kde koncoví uživatelé mohou předplacenou jiný oznámení a můžou existovat ještě více mobilních aplikací. Například intranet mobilní aplikace kde může být vhodné jeden mobilní aplikace pro příjem oznámení z více takových systémů back-endu. Back-endových systémůf neznáte nebo muset znát sémantiku/technologie nabízených oznámení, abyste běžným řešením tradičně bylo zavést komponentu, která dotazuje systémů back-end pro všechny události a je odpovědná za zasílání zprávy nabízených oznámení, které mají klient.

Lepším řešením je použití služby Azure Service Bus – model tématu/odběru, což snižuje složitost při vytváření škálovatelných řešení.

Tady je obecné architektuře řešení (zobecněný pomocí více mobilních aplikací, ale vztahuje rovněž, pokud existuje jenom jedna mobilní aplikace)

## <a name="architecture"></a>Architektura

![][1]

Je důležitá v tento diagram architektury služby Azure Service Bus, která poskytuje témata/předplatná programovacího modelu (Další informace o ji do [Programování služby Service Bus Pub/Sub]). Příjemce, který v tomto případě je mobilní back-end (obvykle [Azure Mobile Service], která inicializuje nabízených oznámení do mobilní aplikace) přijme zprávy přímo z back-endových systémůf ale místo toho abstrakci zprostředkující vrstvy poskytované [Azure Service Bus], což umožňuje mobilním back-endu pro příjem zpráv z jednoho nebo více back-endovým systémům. Téma služby Service Bus je potřeba vytvořit pro každý back-end systém, třeba účet, HR, Finance, což je v podstatě "témata", které vás zajímají, který zahájí zpráv k odeslání jako nabízené oznámení. Back-endovým systémům odesílají zprávy službě tato témata. Mobilní back-endu můžete přihlásit k odběru těchto témat vytvořením odběru služby Service Bus. Opravňuje mobilní back-end pro příjem oznámení z odpovídající systém back-endu. Mobilní back-end i nadále naslouchat zprávám ve svých předplatných a co nejdříve po přijetí e-mailu, znovu aktivuje a odešle jako oznámení pro své Centrum oznámení. Notification hubs pak nakonec doručení zprávy do mobilní aplikace. Tady je seznam klíčových komponent:

1. Back-endových systémůf (systémy obchodní/starší verze)
   * Vytvoří téma služby Service Bus
   * Odešle zprávu
1. Mobilní back-end
   * Vytvoří předplatné služby
   * Přijme zprávu (z back-end systém)
   * Odešle oznámení klientům (prostřednictvím centra oznámení Azure)
1. Mobilní aplikace
   * Přijme a zobrazí oznámení

### <a name="benefits"></a>Výhody

1. Oddělení mezi příjemce (mobilní aplikace nebo služby přes Centrum oznámení) a odesílatele (back-end systémy) umožňuje další back-endových systémůf integrujete minimální změny.
1. Také udržuje scénář více mobilní aplikace nebudou moct přijímat události z jednoho nebo více back-endových systémů.  

## <a name="sample"></a>Ukázka

### <a name="prerequisites"></a>Požadavky

Proveďte následující kurzy a seznámit se s koncepty, stejně jako běžné kroky vytvoření a konfigurace:

1. [Programování služby Service Bus Pub/Sub] – tento kurz vysvětluje podrobnosti o práci se Service Bus tématy nebo předplatnými, postup vytvoření oboru názvů tak, aby obsahovala tématy nebo předplatnými, odesílání a příjem zpráv z nich.
2. [Notification Hubs – kurz pro univerzální aplikace Windows] – tento kurz vysvětluje, jak nastavit aplikaci Windows Store a používat Notification Hubs k registraci a pak přijímat oznámení.

### <a name="sample-code"></a>Ukázka kódu

Úplný ukázkový kód je k dispozici na [ukázky centra oznámení]. Je rozdělit do tří součástí:

1. **EnterprisePushBackendSystem**

    a. Tento projekt používá **WindowsAzure.ServiceBus** balíčku NuGet a je založena na [Programování služby Service Bus Pub/Sub].

    b. Tato aplikace je jednoduchá C# konzolové aplikace pro simulaci systému LoB, který zahájí zpráv, který bude doručen do mobilní aplikace.

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

    c. `CreateTopic` slouží k vytvoření tématu služby Service Bus.

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

    d. `SendMessage` slouží k odesílání zpráv pro toto téma služby Service Bus. Tento kód jednoduše odešle sadu náhodné zpráv do tématu pravidelně pro účely ukázky. Obvykle je back-end systém, který odesílá zprávy při výskytu události.

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

    a. Tento projekt používá *WindowsAzure.ServiceBus* a **Microsoft.Web.WebJobs.Publish** NuGet balíčky a je založena na [Programování služby Service Bus Pub/Sub].

    b. Následující Konzolová aplikace spouští jako [Azure WebJob] vzhledem k tomu, že má spouštět nepřetržitě naslouchat zprávám ze systémů LoB/back-endu. Tato aplikace je součástí mobilní back-end.

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

    c. `CreateSubscription` slouží k vytvoření odběru služby Service Bus pro téma kde back-end systém posílání zpráv. V závislosti na podnikový scénář, tato komponenta vytvoří jeden nebo více odběrů do příslušných témat (například některé mohou přijímat zprávy ze systému řízení zdrojů, některé ze systému Finance a tak dále)

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

    d. `ReceiveMessageAndSendNotification` slouží ke čtení zprávy z tématu pomocí svého předplatného a pokud je úspěšné čtení pak si vytvořte oznámení (v ukázkovém scénáři Windows nativní informační zpráva) k odeslání do mobilní aplikace pomocí Azure Notification Hubs.

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

    e. Pro publikování této aplikace jako **WebJob**, klikněte pravým tlačítkem na řešení v sadě Visual Studio a vyberte **publikovat jako webovou úlohu**

    ![][2]

    f. Vyberte svůj profil publikování a vytvořit nový web Azure, pokud ho ještě neexistuje, který je hostitelem této webové úlohy, a až budete mít na web potom **publikovat**.

    ![][3]

    g. Konfigurace úlohy být "Spustit průběžně", aby při přihlášení k [Azure Portal] by měl vypadat přibližně takto:

    ![][4]

3. **EnterprisePushMobileApp**

    a. Tato aplikace je aplikace Windows Store, která přijímá informační zprávy z úlohy WebJob spuštěna jako součást mobilní back-end a zobrazit ji. Tento kód je založený na [Notification Hubs – kurz pro univerzální aplikace Windows].  

    b. Ujistěte se, že vaše aplikace je povoleno přijímat oznámení informačního nápisu.

    c. Ujistěte se, že následující Notification Hubs registrační kód je volána v aplikaci spustit (po nahrazení `HubName` a `DefaultListenSharedAccessSignature` hodnoty:

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

1. Ujistěte se, že vaše webová úloha je úspěšně spuštěná a průběžně spuštění je naplánované.
2. Spustit ** EnterprisePushMobileApp, který spouští aplikaci Windows Store.
3. Spustit **EnterprisePushBackendSystem** konzolovou aplikaci, která simuluje back-end obchodní a spustí odesílání zpráv a měla by se zobrazit informační zprávy uvedené jako na následujícím obrázku:

    ![][5]

4. Zprávy byly původně odeslána do témat Service Bus, které byl monitorován odběry služby Service Bus v webová úloha. Jakmile byla přijata zpráva, oznámení se vytvoří a odešle do mobilní aplikace. Můžete si projít protokoly úlohy WebJob k potvrzení zpracování, když přejdete na odkaz na protokoly v [Azure Portal] pro webové úlohy:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Ukázky centra oznámení]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programování služby Service Bus Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: ../app-service/webjobs-create.md
[Notification Hubs – kurz pro univerzální aplikace Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal]: https://portal.azure.com/
