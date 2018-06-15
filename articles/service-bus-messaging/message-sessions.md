---
title: Azure Service Bus zprávu relací | Microsoft Docs
description: Zpracování zpráv Azure Service Bus s relací pořadí.
services: service-bus-messaging
documentationcenter: ''
author: clemensv
manager: timlt
editor: ''
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: sethm
ms.openlocfilehash: 551432cd13c16fdd5423c46ed9c6f740353808f8
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30181635"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Zpráva relací: první, nejprve out (FIFO) 

Microsoft Azure Service Bus relací povolte společné a seřazené zpracování bez vazby pořadí související zprávy. Pokud chcete záruku FIFO v Service Bus, použijte relací. Service Bus není doporučený o povaze vztah mezi zprávy a také nedefinuje konkrétním modelu pro určení, kde sekvenci zpráv začíná nebo končí.

Všechny odesílatele můžete vytvořit relaci při odeslání zprávy do tématu nebo fronty nastavením [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) vlastnost na identifikátor některé definované aplikací, které jsou jedinečné pro relace. Na úrovni protokolu AMQP 1.0, tato hodnota mapuje *id skupiny* vlastnost.

Podporující relace fronty nebo předplatných, relace začalo existence po minimálně jednu zprávu s relace [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Jakmile existuje relace, neexistuje žádné definované časové nebo rozhraní API pro při vypršení platnosti relace, nebo zmizí. Teoreticky, můžete být přijata zpráva pro relaci v současné době další zprávy v průběhu času v roce a pokud **SessionId** odpovídá, relace je stejný z hlediska Service Bus.

Obvykle ale má aplikace zrušte představu o kde sada související zprávy spuštění a ukončení. Service Bus nenastaví žádná konkrétní pravidla.

Příklad toho, jak zobrazit pořadí pro přenos souboru je nastavit **popisek** vlastnost pro první zprávu, která se **spustit**, pro zprostředkující zprávy a pokuste se **obsah**, a pro poslední zprávy **end**. Relativní umístění obsahu zprávy můžete vypočítat jako aktuální zprávu *SequenceNumber* rozdílů z **spustit** zpráva *SequenceNumber*.

Funkci relace v Service Bus umožňuje konkrétní přijímat operace, ve formě [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) v C# a rozhraní API Java. Povolit funkci nastavením [vlastnost requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) vlastnost fronty nebo předplatné prostřednictvím Správce Azure Resource Manager, nebo nastavením příznaku na portálu. To je potřeba, před dalším pokusem o použití související operace rozhraní API.

Na portálu nastavte příznak s následující zaškrtávací políčko:

![][2]

Rozhraní API pro relace existovat na klientských počítačích fronty a předplatné. Imperativní modelu, kterými se řídí při příjmu zprávy a relací a model na základě obslužná rutina podobná *OnMessage*, že skryje složitost správy je přijetí cykly.

## <a name="session-features"></a>Funkce relace

Relace zadejte souběžných zrušte multiplexní streamů prokládaná zpráva při zachování a zaručující seřazené doručení.

![][1]

A [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) příjemce je vytvoření klient přijetí relaci. Volání klienta [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) nebo [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) v jazyce C#. V modelu reaktivní zpětného volání zaregistruje ho obslužnou rutinu relace.

Když [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektu je přijímán a při udržované klientem, že klient má výhradní zámek na všechny zprávy s danou relaci [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) které existovat v fronta nebo předplatného, a také na všechny zprávy s třídou **SessionId** , stále dorazí během relace trvá.

Vydání zámek při **zavřete** nebo **CloseAsync** se nazývají, nebo když vyprší platnost zámek v případech, ve kterých je aplikace zavřít operaci nelze provést. Zámek relace zacházeno jako výhradní zámek na soubor, což znamená, že aplikace má ukončení relace Jakmile již musí ho a neočekává žádné další zprávy.

Když více souběžných příjemci načítat z fronty, jsou odesílány zprávy, které patří do konkrétní relace do konkrétní příjemce, který má v současnosti zámek pro tuto relaci. V této operaci datového proudu prokládaná zpráv, které se nacházejí v jednom fronty nebo předplatné je řádně zrušte multiplexní k různým příjemcům a tyto příjemci můžete také za provozu na různých klientských počítačů, protože správa zámku se straně služby, stane uvnitř Service Bus.

Tři příjemci souběžných relací na předchozím obrázku. Jedna relace s `SessionId` = 4 nemá žádné aktivní, vlastnícím klienta, což znamená, že se dodávají žádné zprávy z této konkrétní relace. Relaci funguje v mnoha směrech jako fronty sub.

Relace uzamčení relace příjemce je zastřešující pro zámek zprávy používané *funkce Náhled zámku* vyrovnání režimu. Příjemce nemůže současně mít dvě zprávy "v cestě", ale zprávy musí být zpracovány v pořadí. Nové zprávy se dá získat jenom při předchozí zpráva byla dokončena nebo lettered zpráv. Zrušení zpráva příčiny, stejná zpráva obsluhovat znovu s další operace příjmu.

## <a name="message-session-state"></a>Stav relace zprávy

Když pracovní postupy jsou zpracovány v velkého rozsahu, vysokou dostupnost cloudové systémy, pracovní postup obslužná rutina přidružená konkrétní relace musí být schopna obnovit z neočekávaných chyb a také být schopná obnovení částečně dokončené práce na jiné proces nebo počítač z kde začal práce.

Zařízení stavu relace umožňuje anotaci definované aplikací relace zpráva uvnitř broker, tak, aby stav zaznamenaná zpracování relativně k této relaci bude okamžitě dostupná, když relace se získávají pomocí nové procesoru.

Z hlediska Service Bus je stav relace zpráva neprůhledného binární objekt, který může obsahovat data velikosti jednu zprávu, která je 256 KB pro Service Bus Standard a 1 MB pro Service Bus Premium. Stav zpracování relativně k relaci může uchovávat uvnitř stavu relace nebo stavu relace může ukazovat na některé umístění úložiště nebo záznam databáze, která obsahuje tyto informace.

Rozhraní API pro správu stavu relace, [setstate –](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), naleznete na [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt v C# i rozhraní API Java. Relace, který měl předtím žádný stav relace nastavit vrátí **null** reference pro **GetState**. Vymazání stavu dříve nastavené relace se provádí s [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Všimněte si, že stav relace zůstane, pokud se nevymaže (vrácení **null**) i v případě, že se spotřebovávají všechny zprávy v relaci.

Všechny existující relace v fronty nebo předplatného, mohou být uvedené s **SessionBrowser** metoda v rozhraní API Java a [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) na [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) a [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) v rozhraní .NET klienta.

Stav relace uchovávat v frontu nebo v, že předplatné, se započítává kvótu úložiště dané entity. Po dokončení aplikace s relací se proto doporučuje pro aplikace, vyčistěte stav zachované vyhnout náklady na externí správu.

## <a name="next-steps"></a>Další postup

- [Úplný příklad](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) odesílat a přijímat na základě relace zprávy ze sběrnice fronty pomocí rozhraní .NET standardní knihovny.
- [Ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) používající klienta rozhraní .NET Framework pro zpracování deklaracemi relace zprávy. 

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png