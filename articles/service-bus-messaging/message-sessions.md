---
title: Relace zpráv Azure Service Bus | Dokumentace Microsoftu
description: Zpracování pořadí zpráv Azure Service Bus s relacemi.
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
ms.openlocfilehash: cc7ef24a3e1781969284a3d1e683fe73ce48b551
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216389"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Zpráva relací: první, nejprve-out (FIFO) 

Microsoft Azure Service Bus relace povolí zpracování smíšený a seřazených nevázaných sekvencí souvisejících zpráv. Jak začít využívat ve službě Service Bus se zárukou FIFO, použijte relace. Service Bus není doporučené ohledně povahy vztah mezi zprávy a také nedefinuje konkrétní vzor určující, kde pořadí zpráv začíná nebo končí.

Žádné odesilatele, může znamenat relace při odesílání zpráv do tématu nebo fronty tak, že nastavíte [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) vlastnost některé definovaného aplikací jedinečný identifikátor pro relaci. Na úrovni protokolu AMQP 1.0, tato hodnota odpovídá *id skupiny* vlastnost.

V relaci používající front nebo odběrů, relace začne existence po minimálně jednu zprávu s relace [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId). Jakmile existuje relace neexistuje žádné definované časové nebo rozhraní API pro při vypršení platnosti relace, nebo zmizí. Teoreticky můžete přijata zpráva pro relaci v současné době na další zprávu v čase za rok a pokud **SessionId** odpovídá, relace je stejný z perspektivy služby Service Bus.

Obvykle ale má aplikace vymazat pojem kde sadu související zprávy začíná a končí. Service Bus se nenastaví žádná konkrétní pravidla.

Příklad toho, jak zobrazit pořadí pro přenos souboru, je nastavena **popisek** vlastnost pro první zprávy **start**, zprostředkující zprávy **obsah**, a pro poslední zprávy **end**. Relativní umístění obsahu zprávy nelze vypočítat jako aktuální zprávu *SequenceNumber* delta z **start** zpráva *SequenceNumber*.

Funkci relace v Service Bus umožňuje konkrétní operace, přijetí ve formě [popsaným](/dotnet/api/microsoft.servicebus.messaging.messagesession) v C# a rozhraní Java API. Povolit funkci tak, že nastavíte [vlastnost requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) vlastnosti fronty nebo odběru prostřednictvím Azure Resource Manageru, nebo nastavte příznak na portálu. To je potřeba předtím, než se pokusíte použít souvisejících operací rozhraní API.

Na portálu nastavte příznak s následující políčko:

![][2]

Rozhraní API pro relace existovat na klientských počítačích fronty a předplatné. Imperativní model, který řídí, při doručení zprávy a relace a modelu na základě obslužná rutina podobný *OnMessage*, že skrývá složitost správy příjem smyčky.

## <a name="session-features"></a>Funkce relace

Relací zadejte souběžných zrušit multiplexing streamovaných prokládané zpráva při zachování a uzavřené objednané dodání.

![][1]

A [popsaným](/dotnet/api/microsoft.servicebus.messaging.messagesession) příjemce se vytvoří klient přijímá relaci. Volání klienta [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) nebo [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) v jazyce C#. Registruje obslužné rutiny relace v modelu reaktivní zpětného volání.

Když [popsaným](/dotnet/api/microsoft.servicebus.messaging.messagesession) přijmout objekt a dokud je držen klientem, tento klient obsahuje výhradní zámek pro všechny zprávy s danou relaci [SessionId](/en-us/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) , které existují ve frontě nebo odběru, a také na všechny zprávy s ním **SessionId** doručení, která stále dokud je držen relace.

Zámek je uvolněn po **zavřete** nebo **CloseAsync** jsou volány, nebo když vyprší platnost zámku v případech, ve kterých je aplikace nejde provést operaci zavření. Uzamčení relace by se měla zpracovávat jako výhradní zámek na souboru, což znamená, že aplikace by měla ukončit relaci ihned poté, co už kapacitu nebo neočekává žádné další zprávy.

Když několik souběžných příjemců o přijetí změn z fronty, se odešlou, filtrovaly zprávy, které patří do konkrétní relace konkrétní příjemci, který právě pro tuto relaci je držitelem zámku. Pomocí této operace datového proudu prokládané zpráv, které se nacházejí v jedné fronty nebo odběru je čistě zrušit multiplexní různým příjemcům a tyto příjemce může také existovat v jiné klientské počítače, protože správa zámku se stane straně služby, uvnitř Service Bus.

Předchozí obrázek znázorňuje tři příjemci souběžných relací. Jedna relace s `SessionId` = 4 nemá žádné aktivní, vlastnící klientovi, což znamená, že žádné zprávy doručovaly z této konkrétní relace. Relace funguje podobně jako u front sub mnoha způsoby.

Relace uzamčení relace příjemce se zastřešující pro zámek zprávy používané *neboli peek-lock* vyrovnání režimu. Příjemce nemůže současně obsahovat dvě zprávy "v letu", ale je potřeba zpracovat zprávy v pořadí. Novou zprávu lze získat pouze když byla dokončena nebo dead lettered předchozí zprávu. Zrušení zpráva způsobí, že stejná zpráva se obsluhovat znovu s následujícím operace přijetí.

## <a name="message-session-state"></a>Stav relace zpráv

Když pracovní postupy jsou zpracovány v rozsáhlé škálování, vysokou dostupnost cloudových systémů, obslužné rutiny pracovní postup spojený s konkrétní relací musí být schopen zotavení z neočekávaných chyb a také být schopné opětovném spuštění částečně dokončené práce na jiný proces nebo počítač z kde zahájení práce.

Zařízení stavu relace umožňuje anotaci definovaného aplikací relace zpráv do zprostředkovatele, tak, aby stav zaznamenané zpracování vzhledem k této relaci se změní okamžitě k dispozici při relaci se získávají pomocí nové procesoru.

Stav relace zpráv z perspektivy služby Service Bus je neprůhledný binární objekt, který může obsahovat data o velikosti jedna zpráva, která je 256 KB pro Service Bus úrovně Standard a 1 MB pro Service Bus úrovně Premium. Stav zpracování relativně vzhledem k relaci se můžou uchovávat ve stavu relace nebo stav relace může odkazovat na některé umístění úložiště nebo záznam v databázi, která obsahuje tyto informace.

Rozhraní API pro správu stavu relace [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), můžete najít na [popsaným](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektu v jazyce C# i rozhraní Java API. Relace, který měl předtím žádný stav relace nastavit vrátí **null** odkaz pro **GetState**. Vymazání stavu dříve nastaveného relace se provádí pomocí [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Všimněte si, že stav relace zůstane, dokud se nevymaže (vrácení **null**), i když jsou využité všechny zprávy v relaci.

Mohou být uvedené všechny existující relace ve frontě nebo odběru s **SessionBrowser** metoda v rozhraní API Java a s [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) na [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) a [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) v klientovi .NET.

Stav relace se nachází ve frontě nebo v, že předplatné započítává kvóta úložiště v dané entitě. Po dokončení aplikace s relací se proto doporučuje pro aplikace k vyčištění stavu uchovanou vyhnout externí správu nákladů.

## <a name="next-steps"></a>Další postup

- [Kompletní příklad](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient) odesílání a příjem zpráv na základě relace ze služby Service Bus zařadí do fronty pomocí knihovny .NET Standard.
- [Ukázka](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) , který používá klienta rozhraní .NET Framework pro zpracování zpráv s ohledem na relaci. 

Další informace o zasílání zpráv Service Bus, najdete v následujících tématech:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png