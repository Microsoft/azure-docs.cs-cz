---
title: Architektura podnikového nabízení centra oznámení
description: Informace o používání Center oznámení Azure v podnikovém prostředí
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
ms.openlocfilehash: 0104547a432f7f78d74731e11926bcd82088cef7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76264029"
---
# <a name="enterprise-push-architectural-guidance"></a>Doprovodné materiály k architektuře nabízení v podnicích

Podniky dnes postupně směřují k vytváření mobilních aplikací buď pro své koncové uživatele (externí), nebo pro zaměstnance (interní). Mají stávající back-endové systémy na místě, ať už je to sálové počítače nebo některé lob aplikace, které musí být integrovány do architektury mobilních aplikací. Tato příručka popisuje, jak nejlépe provést tuto integraci a doporučit možné řešení běžných scénářů.

Častým požadavkem je odesílání nabízených oznámení uživatelům prostřednictvím jejich mobilní aplikace, když dojde k události zájmu v back-endových systémech. Například zákazník banky, který má bankovní aplikaci banky na iPhonu, chce být upozorněn, když je debetní částka vyšší než určitá částka z účtu nebo intranetového scénáře, kdy zaměstnanec z finančního oddělení, který má aplikaci pro schválení rozpočtu na Windows Phone, chce být oznámena při obdržení žádosti o schválení.

Bankovní účet nebo schválení zpracování je pravděpodobné, že bude provedeno v některých back-end systému, který musí zahájit push pro uživatele. Může existovat více takových back-endových systémů, které musí všechny vytvořit stejný druh logiky, aby se spustila, když událost spustí oznámení. Složitost zde spočívá v integraci několika back-endových systémů spolu s jediným systémem push, kde se koncoví uživatelé mohli přihlásit k různým oznámením a může dokonce existovat více mobilních aplikací. Například intranetové mobilní aplikace, kde jedna mobilní aplikace může chtít přijímat oznámení z více takových back-endových systémů. Back-endové systémy neznají nebo potřebují vědět o push sémantice / technologii, takže společným řešením zde tradičně bylo zavést komponentu, která provádí dotazování back-endových systémů pro všechny události zájmu a je zodpovědná za odesílání nabízených zpráv klientovi.

Lepším řešením je použití modelu Azure Service Bus – topic/subscription, který snižuje složitost a zároveň činí řešení škálovatelným.

Zde je obecná architektura řešení (zobecněná s více mobilními aplikacemi, ale stejně použitelná, pokud existuje pouze jedna mobilní aplikace)

## <a name="architecture"></a>Architektura

![][1]

Klíčovým bodem v tomto architektonickém diagramu je Azure Service Bus, který poskytuje témata/předplatná programovací model (více o něm na [Service Bus Pub/Sub programování).] Příjemce, který v tomto případě je mobilní back-end (obvykle [Azure Mobile Service], který iniciuje nabízený příkaz k mobilním aplikacím) nepřijímá zprávy přímo z back-endových systémů, ale místo toho zprostředkující vrstvu abstrakce poskytovanou [službou Azure Service Bus], která umožňuje mobilní back-end přijímat zprávy z jednoho nebo více back-endových systémů. Pro každý back-endový systém, například účet, HR, finance, což je v podstatě "témata" zájmu, které iniciuje zprávy, které mají být odeslány jako nabízené oznámení, musí být vytvořeno pro každý back-endový systém, například Účet, HR, Finance, což je v podstatě "témata" zájmu, která iniciuje zprávy, které mají být odeslány jako nabízené oznámení. Back-endové systémy odesílají zprávy do těchto témat. Mobilní back-end se může přihlásit k odběru jednoho nebo více takových témat vytvořením předplatného service bus. Opravňuje mobilní back-end k přijímání oznámení z odpovídajícího back-endového systému. Mobilní back-end pokračuje v naslouchání zpráv ve svých předplatných a jakmile přijde zpráva, vrátí se zpět a odešle ji jako oznámení do svého centra oznámení. Centra oznámení pak nakonec doručit zprávu do mobilní aplikace. Zde je seznam klíčových složek:

1. Backendové systémy (LoB/Starší systémy)
   * Vytvoří téma sběrnice
   * Odešle zprávu
1. Mobilní backend
   * Vytvoří předplatné služby
   * Přijímá zprávu (ze systému back-endu)
   * Odešle oznámení klientům (přes Azure Notification Hub)
1. Mobilní aplikace
   * Přijímá a zobrazuje oznámení

### <a name="benefits"></a>Výhody

1. Oddělení mezi přijímačem (mobilní aplikace/služba přes Notification Hub) a odesílatelem (back-endové systémy) umožňuje integraci dalších back-endových systémů s minimálními změnami.
1. Je také scénář více mobilních aplikací, které mohou přijímat události z jednoho nebo více back-endových systémů.  

## <a name="sample"></a>Ukázka

### <a name="prerequisites"></a>Požadavky

Vyplňte následující kurzy, abyste se seznámili s koncepty a také s běžnými kroky pro vytváření & konfiguraci:

1. [Service Bus Pub/Sub programování] - Tento kurz vysvětluje podrobnosti o práci s Service Bus Témata/Odběry, jak vytvořit obor názvů obsahovat témata/odběry, jak odesílat & přijímat zprávy od nich.
2. [Centra oznámení – univerzální kurz systému Windows] – tento kurz vysvětluje, jak nastavit aplikaci pro Windows Store a pomocí center oznámení zaregistrovat a pak přijímat oznámení.

### <a name="sample-code"></a>Ukázka kódu

Úplný ukázkový kód je k dispozici na [ukázkách centra oznámení]. Je rozdělena do tří složek:

1. **Systém EnterprisePushBackendSystem**

    a. Tento projekt používá balíček **WindowsAzure.ServiceBus** NuGet a je založen na [service bus pub/sub programování].

    b. Tato aplikace je jednoduchá konzolová aplikace C# pro simulaci systému LoB, který iniciuje zprávu, která má být doručena do mobilní aplikace.

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

    c. `CreateTopic`se používá k vytvoření tématu Service Bus.

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

    d. `SendMessage`slouží k odesílání zpráv do tohoto tématu sběrnice. Tento kód jednoduše odesílá sadu náhodných zpráv na téma pravidelně pro účely vzorku. Obvykle je back-endový systém, který odesílá zprávy, když dojde k události.

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

    a. Tento projekt používá balíčky *WindowsAzure.ServiceBus* a **Microsoft.Web.WebJobs.Publish** NuGet a je založen na [programování Service Bus Pub/Sub].

    b. Následující konzolová aplikace běží jako [Azure WebJob,] protože má běžet nepřetržitě naslouchat zprávy z LoB/back-endové systémy. Tato aplikace je součástí vašeho mobilního backendu.

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

    c. `CreateSubscription`slouží k vytvoření předplatného service bus pro téma, kde back-endový systém odesílá zprávy. V závislosti na obchodním scénáři tato komponenta vytvoří jedno nebo více odběrů odpovídajících témat (například některé mohou přijímat zprávy ze systému hr, některé z finančního systému a tak dále)

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

    d. `ReceiveMessageAndSendNotification`Slouží ke čtení zprávy z tématu pomocí jeho předplatného a pokud je čtení úspěšné, vytvořte oznámení (v ukázkovém scénáři nativní informační zprávy systému Windows), které se odešle do mobilní aplikace pomocí centra Oznámení Azure.

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

    e. Pro publikování této aplikace jako **webjob**, klikněte pravým tlačítkem myši na řešení v sadě Visual Studio a vyberte **publikovat jako webjob**

    ![][2]

    f. Vyberte svůj profil publikování a vytvořte nový web Azure WebSite, pokud ještě neexistuje, který je hostitelem této webové úlohy a jakmile máte webovou stránku, pak **publikovat**.

    ![][3]

    g. Nakonfigurujte úlohu tak, aby se při přihlášení k [portálu Azure] měla zobrazit něco jako následující:

    ![][4]

3. **Aplikace EnterprisePushMobileApp**

    a. Tato aplikace je aplikace pro Windows Store, která přijímá informační zprávy z webjob běží jako součást vašeho mobilního back-endu a zobrazit ji. Tento kód je založen na [oznámení huby - Windows Universal tutorial].  

    b. Ujistěte se, že vaše aplikace je povolena pro příjem informačních zpráv.

    c. Ujistěte se, že následující kód registrace centra oznámení je volána `HubName` `DefaultListenSharedAccessSignature` při spuštění aplikace (po nahrazení hodnoty a:

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

1. Ujistěte se, že vaše webová úloha je spuštěna úspěšně a naplánováno spuštění nepřetržitě.
2. Spusťte **aplikaci EnterprisePushMobileApp**, která spustí aplikaci pro Windows Store.
3. Spusťte konzolovou aplikaci **EnterprisePushBackendSystem,** která simuluje back-end LoB a začne odesílat zprávy, a měla by se zobrazit informační zprávy, která se zobrazují jako následující obrázek:

    ![][5]

4. Zprávy byly původně odeslány do témat služby Service Bus, která byla sledována odběry služby Service Bus ve vaší webové úloze. Po přijetí zprávy bylo vytvořeno oznámení a odesláno do mobilní aplikace. Můžete se podívat do protokolů webjobů a potvrdit zpracování, když přejdete na odkaz Protokoly na [portálu Azure] pro webovou úlohu:

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
[Mobilní služba Azure]: https://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: https://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Service Bus Pub/Dílčí programování]: https://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Webová úloha Azure]: ../app-service/webjobs-create.md
[Oznamovací centra – univerzální kurz windows]: https://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portál Azure]: https://portal.azure.com/
