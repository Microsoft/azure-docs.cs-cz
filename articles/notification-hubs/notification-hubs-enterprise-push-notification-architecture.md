---
title: Architektura Push Notification Hubs – Enterprise
description: Pokyny k používání Azure Notification Hubs v podnikovém prostředí
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d7066b58330d35e5dba66cfe6ed5cfaddff4b68a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="enterprise-push-architectural-guidance"></a>Doprovodné materiály k architektuře nabízení v podnicích
Podniky jsou dnes postupně přesunutí směrem vytváření mobilních aplikací buď pro své koncové uživatele (externí) nebo pro zaměstnance (interní). Mají existující back-end systémy zavedené je jej sálové počítače nebo některé obchodní aplikace, které musí být integrován do architektury mobilních aplikací. Tato příručka pojednává o tom, jak vhodné provést této integrace doporučujeme možná řešení pro běžné scénáře.

Časté požadavek je pro odesílání nabízených oznámení uživatelům prostřednictvím jejich mobilních aplikací při výskytu události týkající se v systémech back-end. například chce být informován při MD nad určitou míru z účtu nebo o intranetu scénář, kde zaměstnanci z finančního oddělení, který má aplikace schválení na nároky na Windows Phone chce zákazník bank, který má tato banka bankovní aplikace pro zařízení typu iPhone  Chcete-li být upozorněni, když je přijal žádost o schválení.

Bankovní účet nebo zpracování schválení je pravděpodobné, v některých systém back-end, který musí inicializovat oznámení pro uživatele. Může existovat více takové back-end systémy, které musíte sestavit stejný druh logiku pro oznámení, když událost se aktivuje oznámení. Složitost zde spočívá v integraci několik systémů back-end společně s jeden nabízené systému, kde koncoví uživatelé mohou mít předplatné jiný oznámení a mohou existovat i více mobilních aplikací. Například intranetu mobilní aplikace, kde jeden mobilních aplikací chtít dostávat oznámení z více takového systému back-end. Back-end systémy vědět, nebo potřebujete vědět nabízené sémantiku/technologie tak běžné řešení tradičně byl zavádět součást, který posuzuje systémy back-end pro všechny události, které vás zajímají a je odpovědná za zasílání nabízených zprávy, které mají klient.

Lepším řešením je použití Azure Service Bus - téma/odběr modelu, což snižuje složitost při vytváření škálovatelné řešení.

Tady je obecné architektuře řešení (zobecněný s více mobilních aplikací, ale platí po jenom jedna mobilní aplikace)

## <a name="architecture"></a>Architektura
![][1]

Klíčovou do tohoto diagramu, architektury je Azure Service Bus, která poskytuje témata nebo odběry programovací model (Další informace o jeho v [Service Bus Pub nebo Sub programování]). Příjemce, který v tomto případě je mobilního back-endu (obvykle [Azure Mobile Service], který iniciuje oznámení na mobilní aplikace) nejsou doručovány zprávy přímo z back-end systémy ale místo toho abstrakci zprostředkující vrstva poskytované [Azure Service Bus, která umožňuje mobilní back-end pro příjem zpráv z jednoho nebo více systémů back-end. Téma sběrnice je potřeba vytvořit pro každou z back-end systémy, třeba účet, oddělení lidských zdrojů, Finance, která je v podstatě "témata", které vás zajímají, který zahájí zpráv k odeslání jako nabízené oznámení. Back-end systémy odesílání zpráv na tato témata. Back-end Mobile se mohou přihlásit na jeden nebo více těchto témata vytvořením odběru služby Service Bus. Opravňuje mobilní back-end pro příjem oznámení z odpovídající systému back-end. Mobilní back-end i nadále přijímat zprávy o svých předplatných a ihned po doručení zprávy změní zpět a odešle ji jako oznámení jeho centra oznámení. Centra oznámení potom nakonec doručení zprávy do mobilní aplikace. Tady je seznam klíčové komponenty:

1. Back-end systémy (systémy LoB nebo starší)
   * Vytvoří téma sběrnice
   * Odešle zprávu
2. Mobilní back-end
   * Vytvoří předplatné služby
   * Přijme zprávu (z back-end systému)
   * Odešle oznámení klientům (prostřednictvím centra oznámení Azure)
3. Mobilní aplikace
   * Přijímá a zobrazovat oznámení

### <a name="benefits"></a>Výhody:
1. Oddělení mezi příjemce (mobilní aplikaci nebo službě prostřednictvím centra oznámení) a odesílatele (back-end systémy) umožňuje další back-end systémy, kterou je prováděna integrace s minimální změny.
2. Také díky scénář více mobilních aplikací, bude možné přijímat události z jednoho nebo více systémů back-end.  

## <a name="sample"></a>Ukázka:
### <a name="prerequisites"></a>Požadavky
Projděte si následující kurzy seznamte s koncepty, jakož i běžné kroky pro vytvoření a konfigurace:

1. [Service Bus Pub nebo Sub programování] – tento kurz vysvětluje podrobnosti o práci s Service Bus témata nebo předplatných, postup vytvoření oboru názvů tak, aby obsahovala témata nebo předplatného, jak odesílat a přijímat zprávy z nich.
2. [Kurzu centra oznámení – Windows Universal] – tento kurz vysvětluje, jak nastavit aplikace pro Windows Store a používat Notification Hubs k registraci a pak přijímat oznámení.

### <a name="sample-code"></a>Ukázka kódu
Úplný ukázkový kód je k dispozici na [ukázky centra oznámení]. Ho je rozdělená do tří součástí:

1. **EnterprisePushBackendSystem**
   
    a. Používá tento projekt *WindowsAzure.ServiceBus* balíček NuGet a je založena na [Service Bus Pub nebo Sub programování].
   
    b. Tato aplikace je jednoduchý C# konzole aplikace k simulaci LoB systému, který zahájí zprávu, která se bude doručen do mobilní aplikace.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` slouží k vytvoření tématu Service Bus.
   
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
   
    d. `SendMessage` se používá k odeslání zprávy do tohoto tématu Service Bus. Tento kód jednoduše odešle sadu náhodné zprávy do tématu pravidelně pro účely ukázky. Obvykle je systém back-end, který odesílá zprávy, když dojde k události.
   
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
2. **ReceiveAndSendNotification**
   
    a. Používá tento projekt *WindowsAzure.ServiceBus* a *Microsoft.Web.WebJobs.Publish* NuGet balíčků a je založena na [Service Bus Pub nebo Sub programování].
   
    b. Spuštění následující aplikace konzoly jako [webové úlohy Azure] vzhledem k tomu, že je spouštět nepřetržitě přijímat zprávy ze systémů LoB nebo back-end. Tato aplikace je součástí mobilního back-endu.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription that receives messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` slouží k vytvoření odběru služby Service Bus pro téma kde back-end systému odesílá zprávy. V závislosti na podnikový scénář, tato součást vytvoří jeden nebo více odběrů na odpovídající témata (například některé může být přijímání zpráv ze systému oddělení lidských zdrojů, některé z finančního systému a tak dále)
   
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
   
    d. ReceiveMessageAndSendNotification se používá k tuto zprávu přečíst v tématu pomocí svého předplatného a pokud čtení úspěšné potom vytvořit oznámení (v ukázkovém scénáři Windows nativní informační) k odeslání do mobilní aplikace pomocí Azure Notification Hubs.
   
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
   
    e. Pro tuto aplikaci jako publikování **webové úlohy**, klikněte pravým tlačítkem na řešení v sadě Visual Studio a vyberte **publikovat jako webová úloha**
   
    ![][2]
   
    f. Vyberte svůj profil publikování a vytvoření nového webu Azure, pokud ho ještě neexistuje, který je hostitelem této webové úlohy, a až pak máte na webu **publikovat**.
   
    ![][3]
   
    g. Nakonfigurujte úlohu, která má být "Spouštět nepřetržitě" tak, aby při přihlášení do [portál Azure] měli byste vidět něco podobného jako následující:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Tato aplikace je aplikace Windows Store, která přijímá informační zprávy z webové úlohy spuštěna jako součást mobilního back-endu a zobrazit ji. Tento kód je založen na [Kurzu centra oznámení – Windows Universal].  
   
    b. Ujistěte se, že aplikace je povoleno přijímat oznámení informačního nápisu.
   
    c. Ujistěte se, že následující Notification Hubs registrační kód je volána v aplikaci spuštění (po nahrazení *HubName* a *DefaultListenSharedAccessSignature*:
   
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

### <a name="running-sample"></a>Spuštění ukázkové:
1. Zajistěte, aby vaše webová úloha pracuje správně a spouštět nepřetržitě podle plánu.
2. Spustit ** EnterprisePushMobileApp, který se spustí aplikace Windows Store.
3. Spustit **EnterprisePushBackendSystem** konzolovou aplikaci, která simuluje LoB back-end a spustí odesílání zpráv a měli byste vidět informační zprávy, které jsou uvedeny jako na následujícím obrázku:
   
    ![][5]
4. Zprávy byly původně odeslána do témat Service Bus, které byl monitorován odběry služby Service Bus ve webové úlohy. Jakmile byla přijata zpráva, oznámení se vytváří a odesílají do mobilní aplikace. Můžete zobrazit prostřednictvím protokolů webové úlohy potvrďte zpracování při přechodu na odkaz protokoly v [portál Azure] pro webové úlohy:
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[ukázky centra oznámení]: https://github.com/Azure/azure-notificationhubs-samples
[Azure Mobile Service]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub nebo Sub programování]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[webové úlohy Azure]: ../app-service/web-sites-create-web-jobs.md
[Kurzu centra oznámení – Windows Universal]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[portál Azure]: https://portal.azure.com/
