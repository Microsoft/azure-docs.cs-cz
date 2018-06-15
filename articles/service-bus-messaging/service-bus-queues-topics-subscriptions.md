---
title: Přehled fronty, témata a odběry pro zasílání zpráv Azure Service Bus | Microsoft Docs
description: Přehled služby Service Bus entity pro zasílání zpráv.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: a306ced4-74e9-47c6-990a-d9c47efa31d5
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2017
ms.author: sethm
ms.openlocfilehash: 5bea3b56cea81362b25e696a672bf2a00e26d3ef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2017
ms.locfileid: "24029503"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>Fronty, témata a odběry služby Service Bus

Microsoft Azure Service Bus podporuje sadu založené na cloudu, orientovaný na zprávy middleware technologie včetně služby Řízení front zpráv spolehlivé a trvanlivé publikování a přihlášení k odběru zasílání zpráv. Tyto funkce "zprostředkovaného" přenosu zpráv můžete představit jako odpojené zasílání zpráv funkce, které podpora publikování a odběru, časové oddělení a scénáře s využitím zatížení zasílání zpráv Service Bus Vyrovnávání zatížení. Oddělená komunikace má mnoho výhod – klienti a servery se například můžou spojit podle potřeby a provádět své operace asynchronním způsobem.

Entit pro zasílání zpráv, které tvoří základní možnosti zasílání zpráv ve sběrnici Service Bus jsou fronty, témata a odběry a pravidla nebo akce.

## <a name="queues"></a>Fronty

Fronty nabízejí *First In, Out první* doručování zpráv (metodou FIFO) na jeden nebo několik konkurenčních spotřebitelů. To znamená zprávy se očekává obvykle přijímají a zpracovávají v pořadí, ve kterém byly přidány do fronty, a každou zprávu přijme a zpracuje jenom jeden příjemce zprávy. Klíčovou výhodou použití front je dosáhnout "časové oddělení" součástí aplikace. Jinými slovy producenti (odesílatelé) a spotřebitelé (příjemci) nemusí být odesílání a přijímání zpráv ve stejnou dobu, protože zprávy jsou bezpečně uložené ve frontě. Kromě toho Autor nemusí čekat na odpověď od příjemce, aby bylo možné pokračovat se zpracováním a odesláním zprávy.

Výhodou je, "vyrovnávání zátěže," což umožňuje odesílatelům a spotřebitelům umožňuje odesílat a přijímat zprávy různými rychlostmi. V mnoha aplikacích zatížení systému se liší v čase; ale zpracování čas potřebný pro jednotlivé jednotky práce je obvykle stálá. Propojovací producenti a spotřebitelé zpráv s frontou znamená, že spotřebitelskou aplikaci pouze musí být zřízená být schopna zpracovávat průměrnou zátěž místo zátěž ve špičce. S měnící se příchozí zátěží se mění hloubka fronty. To znamená přímou úsporu nákladů s ohledem na množství infrastruktury nutné pro zvládání zatížení aplikace. Při rostoucí zátěži, lze přidat další pracovní procesy ke čtení z fronty. Každou zprávu zpracovává jen jeden pracovní proces. Kromě toho tato Vyrovnávání zatížení založené na operaci pull umožňuje optimální využívání pracovních počítačů i v případě, že pracovní počítače liší s ohledem na výpočetní výkon, jak se budou načítat zprávy na svou vlastním maximální rychlostí. Toto chování se často říká vzor "neslučitelných příjemce".

Pomocí fronty pro zprostředkující mezi producenti a spotřebitelé zpráv poskytuje vyplývajících volné párování mezi součástmi. Protože producenti a spotřebitelé nemají informace o sobě navzájem, bez nutnosti nijak neprojeví na Autor lze upgradovat příjemce.

Vytvoření fronty je několika krocích. Provádět operace správy pro Service Bus (fronty a témata) entity pro zasílání zpráv prostřednictvím [Microsoft.ServiceBus.NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) třídy, která je vytvořená zadáním bázové adresy oboru názvů Service Bus a přihlašovací údaje uživatele. [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager#microsoft_servicebus_namespacemanager) poskytuje metody pro vytvoření, výčet a odstranění entit přenosu zpráv. Po vytvoření [Microsoft.ServiceBus.TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider#microsoft_servicebus_tokenprovider) objekt objektu pomocí názvu SAS a klíč a správu oboru názvů služby, můžete použít [Microsoft.ServiceBus.NamespaceManager.CreateQueue](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateQueue_System_String_)metodu pro vytvoření fronty. Například:

```csharp
// Create management credentials
TokenProvider credentials = TokenProvider.CreateSharedAccessSignatureTokenProvider(sasKeyName,sasKeyValue);
// Create namespace client
NamespaceManager namespaceClient = new NamespaceManager(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials);
```

Potom můžete vytvořit objekt fronty a objekt zasílání zpráv s identifikátor URI služby Service Bus jako argument. Například:

```csharp
QueueDescription myQueue;
myQueue = namespaceClient.CreateQueue("TestQueue");
MessagingFactory factory = MessagingFactory.Create(ServiceBusEnvironment.CreateServiceUri("sb", ServiceNamespace, string.Empty), credentials); 
QueueClient myQueueClient = factory.CreateQueueClient("TestQueue");
```

Pak můžete odesílat zprávy do fronty. Například, pokud máte seznam zprostředkovaných zpráv názvem `MessageList`, podobně jako v následujícím příkladu se zobrazí kód:

```csharp
for (int count = 0; count < 6; count++)
{
    var issue = MessageList[count];
    issue.Label = issue.Properties["IssueTitle"].ToString();
    myQueueClient.Send(issue);
}
```

Potom zobrazí zprávy z fronty následujícím způsobem:

```csharp
while ((message = myQueueClient.Receive(new TimeSpan(hours: 0, minutes: 0, seconds: 5))) != null)
    {
        Console.WriteLine(string.Format("Message received: {0}, {1}, {2}", message.SequenceNumber, message.Label, message.MessageId));
        message.Complete();

        Console.WriteLine("Processing message (sleeping...)");
        Thread.Sleep(1000);
    }
```

V [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) režim, operace příjmu je jednorázová; to znamená, když Service Bus přijme požadavek, označí zprávu jako spotřebovávanou a vrátí ji do aplikace. **ReceiveAndDelete** režimu je nejjednodušší model a funguje nejlépe ve scénářích, kde aplikace může tolerovat selhání se zpráva nezpracuje. Pro lepší vysvětlení si představte scénář, ve kterém spotřebitel vyšle požadavek na přijetí, ale než ji může zpracovat, dojde v něm k chybě a ukončí se. Vzhledem k tomu, že Service Bus, označí zprávu jako spotřebovávanou, když se aplikace restartuje a začne znovu přijímat zprávy, se neuskutečnily zprávu, která se spotřebovala před havárii.

V [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu operace příjmu stane dvoufázová, které umožňuje podporuje aplikace, které nemůžou tolerovat vynechání zpráv. Když Service Bus přijme požadavek, najde zprávu, který se má používat, uzamkne ji proti spotřebování jinými odběrateli příjem ho a vrátí ji do aplikace. Když aplikace dokončí zpracování zprávy (nebo ji bezpečně uloží pro pozdější zpracování), zavolá na přijatou zprávu [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete), a tím potvrdí dokončení druhé fáze přijetí. Když Service Bus uvidí **Complete** volání, označí zprávu jako spotřebovávanou.

Pokud aplikace nemůže zpracovat zprávu z nějakého důvodu, může zavolat [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) metoda na přijatou zprávu (místo [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete)). To umožňuje služba Service Bus zprávu odemkne a zpřístupní ji pro další přijetí, příjemci stejné nebo jiné konkurence mezi spotřebiteli. Za druhé je vypršení časového limitu přidružené zámek a aplikace nepodaří zprávu zpracovat vyprší časový limit uzamčení (například pokud aplikace spadne), pak Service Bus zprávu odemkne a je-li k dispozici být přijata znovu) v podstatě provádění [Abandon](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Abandon) operace ve výchozím nastavení).

Všimněte si, že v případě, že aplikace spadne po zpracování zprávy, ale předtím, než **Complete** požadavku, zpráva se víckrát do aplikace odešle znovu. To se často označuje jako *nejméně jednou* zpracování; to znamená, že každá zpráva se zpracuje alespoň jednou. Ale v některých situacích může doručit víckrát stejnou zprávu. Pokud scénář nemůže tolerovat duplicitní zpracování, pak je potřeba další logiku v aplikaci duplicity, které lze dosáhnout na základě **MessageId** vlastnosti zprávy, která zůstává konstantní napříč pokusy o doručení. To se označuje jako *právě jednou* zpracování.

## <a name="topics-and-subscriptions"></a>Témata a předplatná
Na rozdíl od front, ve kterých každou zprávu zpracuje jeden spotřebitel, *témata* a *odběry* poskytovat ve formuláři na více komunikace, *publikování a přihlášení k odběru* vzor. Tato možnost je užitečná pro škálování pro velmi velký počet příjemců, každá publikovaná zpráva je k dispozici všem odběrům registrovaným pro příslušné téma. Zprávy odeslané do tématu se doručí na jeden nebo více přidružených odběrů, v závislosti na pravidla filtru, které lze nastavit na základě za předplatné. Odběry můžete použít další filtry a omezit zprávy, které chcete dostávat. Se odesílají zprávy do tématu stejným způsobem, se odešlou do fronty, ale nejsou v tématu přímo přijaty zprávy. Místo toho jsou přijímány z předplatného. Předplatné tématu se podobá virtuální frontě, která obdrží kopii zprávy, které jsou odeslány do tématu. Zprávy se přijaté z odběru stejně jako způsob příjmu z fronty.

Prostřednictvím porovnání funkci zasílání zpráv fronty mapuje přímo do tématu a jeho funkce příjem zpráv se mapuje na předplatné. Kromě jiných věcí, to znamená, že odběry podporují stejné vzory dříve popisované v této části s ohledem na fronty: konkurence mezi spotřebiteli, časové oddělení, Vyrovnávání zatížení a vyrovnávání zatížení.

Vytvoření tématu je podobná vytváření fronty, jak je znázorněno v příkladu v předchozí části. Vytvořit URI služby a pak použít [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) třídy za účelem vytvoření oboru názvů klienta. Potom můžete vytvořit téma pomocí [CreateTopic](/dotnet/api/microsoft.servicebus.namespacemanager#Microsoft_ServiceBus_NamespaceManager_CreateTopic_System_String_) metoda. Například:

```csharp
TopicDescription dataCollectionTopic = namespaceClient.CreateTopic("DataCollectionTopic");
```

V dalším kroku přidejte odběry podle potřeby:

```csharp
SubscriptionDescription myAgentSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Inventory");
SubscriptionDescription myAuditSubscription = namespaceClient.CreateSubscription(myTopic.Path, "Dashboard");
```

Potom můžete vytvořit téma klienta. Například:

```csharp
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider);
TopicClient myTopicClient = factory.CreateTopicClient(myTopic.Path)
```

Pomocí odesílatele zprávy, můžete odesílat a přijímat zprávy do tématu, jak je uvedeno v předchozí části. Například:

```csharp
foreach (BrokeredMessage message in messageList)
{
    myTopicClient.Send(message);
    Console.WriteLine(
    string.Format("Message sent: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

Podobně jako u front, jsou přijaty zprávy pomocí předplatného [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) objektu místo [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) objektu. Vytvoření klienta předplatné, předávání název tématu, názvu předplatné a (volitelně) režimu receive jako parametry. Například s **inventáře** předplatného:

```csharp
// Create the subscription client
MessagingFactory factory = MessagingFactory.Create(serviceUri, tokenProvider); 

SubscriptionClient agentSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Inventory", ReceiveMode.PeekLock);
SubscriptionClient auditSubscriptionClient = factory.CreateSubscriptionClient("IssueTrackingTopic", "Dashboard", ReceiveMode.ReceiveAndDelete); 

while ((message = agentSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Inventory...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
    message.Complete();
}          

// Create a receiver using ReceiveAndDelete mode
while ((message = auditSubscriptionClient.Receive(TimeSpan.FromSeconds(5))) != null)
{
    Console.WriteLine("\nReceiving message from Dashboard...");
    Console.WriteLine(string.Format("Message received: Id = {0}, Body = {1}", message.MessageId, message.GetBody<string>()));
}
```

### <a name="rules-and-actions"></a>Pravidla a akce
V mnoha případech je nutné zpracovat zprávy, které mají určité charakteristické vlastnosti různými způsoby. Chcete-li povolit, můžete nakonfigurovat odběry najít zprávy, které mají požadovaných vlastností a pak provést určité úpravy tyto vlastnosti. Při odběry služby Service Bus zobrazit všechny zprávy odeslané do tématu, kopírovat můžete pouze podmnožinu těchto zpráv do fronty virtuální odběru. To se provádí pomocí filtrů odběrů. Tyto změny se nazývají *akce filtru*. Když je vytvořen předplatné, můžete zadat výraz filtru, který funguje na vlastnosti zprávy, vlastnosti systému (například **popisek**) a vlastností vlastní aplikaci (například  **StoreName**.) Výraz filtru SQL je volitelné v tomto případě; bez výraz filtru SQL filtru akce definované na předplatné bude třeba provést na všechny zprávy pro toto předplatné.

Použijeme předchozí příklad, k filtrování zprávy přicházející pouze z **Store1**, by vytvořit řídicí panel předplatné takto:

```csharp
namespaceManager.CreateSubscription("IssueTrackingTopic", "Dashboard", new SqlFilter("StoreName = 'Store1'"));
```

K tomuto filtru předplatné v místě, jen zprávy, které mají `StoreName` vlastnost nastavena na hodnotu `Store1` se zkopírují do virtuální fronty pro `Dashboard` předplatné.

Další informace o možných filtru hodnoty, najdete v dokumentaci pro [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) a [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) třídy. Další informace naleznete [zprostředkované zasílání zpráv: rozšířené filtry](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749) a [tématu filtry](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/TopicFilters) ukázky.

## <a name="next-steps"></a>Další kroky
Najdete zde advanced témata pro další informace a příklady použití zasílání zpráv Service Bus.

* [Přehled přenosu zpráv ve službě Service Bus](service-bus-messaging-overview.md)
* [Kurz .NET pro zprostředkované zasílání zpráv ve službě Service Bus](service-bus-brokered-tutorial-dotnet.md)
* [Zprostředkované zasílání zpráv kurz REST](service-bus-brokered-tutorial-rest.md)
* [Zprostředkované zasílání zpráv: Ukázka filtrů Advanced](http://code.msdn.microsoft.com/Brokered-Messaging-6b0d2749)

