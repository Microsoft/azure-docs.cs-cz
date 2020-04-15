---
title: Relace zpráv Azure Service Bus | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak použít relace povolit společné a uspořádané zpracování neohraničené sekvence souvisejících zpráv.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 1e22641e9d4f9959c26cd2043ea2acd7e260e0f0
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314044"
---
# <a name="message-sessions"></a>Relace zpráv
Relace služby Microsoft Azure Service Bus umožňují společné a seřazené zpracování neomezených sekvencí souvisejících zpráv. Relace lze použít v první dovnitř, první ven (FIFO) a požadavky na odpověď vzory. Tento článek ukazuje, jak používat relace k implementaci těchto vzorů při použití service bus. 

## <a name="first-in-first-out-fifo-pattern"></a>Vzor prvního vak, prvního outu (FIFO)
Chcete-li realizovat záruku FIFO v service bus, použijte relace. Service Bus není normativní o povaze vztahu mezi zprávami a také nedefinuje konkrétní model pro určení, kde sekvence zpráv začíná nebo končí.

> [!NOTE]
> Základní úroveň Service Bus nepodporuje relace. Standardní a prémiové úrovně podporují relace. Rozdíly mezi těmito úrovněmi najdete v tématu [Ceny service bus](https://azure.microsoft.com/pricing/details/service-bus/).

Každý odesílatel může vytvořit relaci při odesílání zpráv do tématu nebo fronty nastavením [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) vlastnost některé aplikace definované identifikátor, který je jedinečný pro relaci. Na úrovni protokolu AMQP 1.0 se tato hodnota mapuje na vlastnost *id skupiny.*

Ve frontách nebo předplatných s ohledem na relace dojde k relacím, když je alespoň jedna zpráva s [sessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)relace . Jakmile relace existuje, neexistuje žádný definovaný čas nebo rozhraní API pro kdy relace vyprší nebo zmizí. Teoreticky může být přijata zpráva pro relaci dnes, další zpráva v roce čas a pokud **SessionId** odpovídá, relace je stejný z hlediska sběrnice služby.

Aplikace má však obvykle jasnou představu o tom, kde začíná a končí sada souvisejících zpráv. Service Bus nenastavuje žádná konkrétní pravidla.

Příkladem vymezení sekvence pro přenos souboru je nastavení **vlastnosti Label** pro první **zprávu,** která má být zahájena , pro zprostředkující zprávy na **obsah**a pro poslední zprávu, která má **být ukončena**. Relativní pozici obsahu zpráv lze vypočítat jako aktuální zpráva *SequenceNumber* delta z **počáteční** zprávy *SequenceNumber*.

Funkce relace v Service Bus umožňuje konkrétní operaci příjmu, ve formě [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) v C# a Java API. Funkci povolíte nastavením vlastnosti [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) ve frontě nebo předplatném prostřednictvím Správce prostředků Azure nebo nastavením příznaku na portálu. Je nutné před pokusem o použití související chod rozhraní API.

Na portálu nastavte příznak s následujícím zaškrtávacím políčkem:

![][2]

> [!NOTE]
> Pokud jsou relace povoleny ve frontě nebo předplatném, klientské aplikace ***již*** nemohou odesílat a přijímat běžné zprávy. Všechny zprávy musí být odeslány jako součást relace (nastavením ID relace) a přijaty přijetím relace.

Api pro relace existují na frontách a předplacených klientech. Existuje imperativní model, který řídí při přijetí relací a zpráv, a model založený na obslužné rutině, podobně jako *OnMessage*, který skryje složitost správy smyčky příjmu.

### <a name="session-features"></a>Funkce relace

Relace poskytují souběžné de-multiplexování prokládání datových proudů zpráv při zachování a zaručení objednaného doručení.

![][1]

Příjemce [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) je vytvořen klientem, který přijímá relaci. Klient volá [QueueClient.AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) nebo [QueueClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) v c#. V modelu zpětného volání reaktivní registruje obslužnou rutinu relace.

Když [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objekt je přijat a při jeho držení klientem, tento klient drží výhradní zámek na všechny zprávy s [sessionId](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) relace, které existují ve frontě nebo předplatné a také na všechny zprávy s **sessionId,** které stále dorazí, zatímco relace je držena.

Zámek je uvolněna **při Close** nebo **CloseAsync** jsou volány, nebo když vyprší platnost zámku v případech, ve kterých aplikace není schopen provést operaci zavření. Zámek relace by měl být zpracován jako výhradní zámek v souboru, což znamená, že aplikace by měla ukončit relaci, jakmile ji již nepotřebuje a/nebo neočekává žádné další zprávy.

Při více souběžných příjemců vytáhnout z fronty, zprávy, které patří do určité relace jsou odeslány na konkrétní příjemce, který aktuálně drží zámek pro tuto relaci. S tímto provozem prokládání zprávy proud v jedné frontě nebo odběr je čistě de-multiplexní na různé příjemce a tyto přijímače mohou také žít na různých klientských počítačích, protože správa zámku se stane service-side, uvnitř Service Bus.

Předchozí obrázek znázorňuje tři souběžné relace přijímače. Jedna relace `SessionId` s = 4 nemá žádného aktivního klienta, což znamená, že z této konkrétní relace nejsou doručovány žádné zprávy. Relace funguje mnoha způsoby jako dílčí fronty.

Zámek relace v držení příjemce relace je zastřešující pro uzamčení zprávy používá *peek-lock* režimu vyrovnání. Příjemce nemůže mít dvě zprávy současně "v letu", ale zprávy musí být zpracovány v pořadí. Novou zprávu lze získat pouze po dokončení předchozí zprávy nebo nedoručené zprávě. Opuštění zprávy způsobí, že stejná zpráva má být znovu doručena s další operaci příjmu.

### <a name="message-session-state"></a>Stav relace zprávy

Při zpracování pracovních postupů v cloudových systémech s vysokou dostupností s vysokou dostupností musí být obslužná rutina pracovního postupu přidružená k určité relaci schopna zotavit se z neočekávaných selhání a pokračovat v částečně dokončené práci na jiném procesu nebo počítači, než kde práce začala.

Zařízení stavu relace umožňuje aplikaci definované poznámky relace zprávy uvnitř zprostředkovatele, takže zaznamenaný stav zpracování vzhledem k této relaci bude okamžitě k dispozici, když je relace získána novým procesorem.

Z hlediska service bus stav relace zprávy je neprůhledný binární objekt, který může obsahovat data o velikosti jedné zprávy, což je 256 KB pro Service Bus Standard a 1 MB pro Service Bus Premium. Stav zpracování vzhledem k relaci může být držen uvnitř stavu relace nebo stav relace může překážet na některé umístění úložiště nebo záznam databáze, který tyto informace obsahuje.

Api pro správu stavu relace [SetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState), lze nalézt na [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektu v C# a Java API. Relace, která dříve žádná sada stavu relace vrátí **nulový** odkaz pro **GetState**. Vymazání dříve nastavený stav relace se provádí s [SetState(null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Stav relace zůstane tak dlouho, dokud není vymazán (vrácení **null**), i v případě, že jsou spotřebovány všechny zprávy v relaci.

Všechny existující relace ve frontě nebo předplatném mohou být uvedeny pomocí metody **SessionBrowser** v rozhraní Java API a [s GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) na [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) a [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) v klientovi rozhraní .NET Framework.

Stav relace podržený ve frontě nebo v předplatném se započítává do kvóty úložiště této entity. Po dokončení aplikace s relace, proto se doporučuje pro aplikaci vyčistit jeho zachovaného stavu, aby se zabránilo externí náklady na správu.

### <a name="impact-of-delivery-count"></a>Dopad počtu dodávek

Definice počtu doručení na zprávu v kontextu relací se mírně liší od definice v nepřítomnosti relací. Zde je tabulka shrnující, kdy se počet dodávek zintálí.

| Scénář | Je počet doručení zprávy přírůstý |
|----------|---------------------------------------------|
| Relace je akceptována, ale platnost zámku relace vyprší (kvůli vypršení časového limitu). | Ano |
| Relace je přijata, zprávy v rámci relace nejsou dokončeny (i když jsou uzamčeny) a relace je uzavřena | Ne |
| Relace je přijata, zprávy jsou dokončeny a potom je relace explicitně uzavřena. | Není k němu k ono (Je to standardní tok. Zde zprávy jsou odstraněny z relace) |

## <a name="request-response-pattern"></a>Vzor požadavku a odpovědi
[Vzor odpověď na požadavek](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) je dobře zavedený vzor integrace, který umožňuje aplikaci odesílatele odeslat požadavek a poskytuje způsob, jak příjemce správně odeslat odpověď zpět do aplikace odesílatele. Tento vzor obvykle potřebuje krátkodobou frontu nebo téma pro aplikaci k odeslání odpovědí. V tomto scénáři relace poskytují jednoduché alternativní řešení se srovnatelnou sémantiku. 

Více aplikací může odesílat své požadavky do fronty jednoho požadavku s určitým parametrem záhlaví nastaveným pro jedinečnou identifikaci aplikace odesílatele. Aplikace příjemce může zpracovávat požadavky přicházející do fronty a odesílat odpovědi ve frontě s povolenými relacemi a nastavit ID relace na jedinečný identifikátor, který odesílatel odeslal na zprávu požadavku. Aplikace, která odeslala požadavek, pak může přijímat zprávy na konkrétní ID relace a správně zpracovat odpovědi.

> [!NOTE]
> Aplikace, která odešle počáteční požadavky by měl vědět `SessionClient.AcceptMessageSession(SessionID)` o ID relace a použít k uzamčení relace, na které očekává odpověď. Je vhodné použít identifikátor GUID, který jednoznačně identifikuje instanci aplikace jako ID relace. Ve frontě by `AcceptMessageSession(timeout)` neměla být žádná obslužná rutina relace ani ve frontě, aby bylo zajištěno, že odpovědi budou k dispozici pro uzamčení a zpracování určitými příjemci.

## <a name="next-steps"></a>Další kroky

- Ukázky [microsoft.azure.servicebusu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) nebo [microsoft.servicebus.messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) najdete v příkladu, který ke zpracování zpráv podporujících relace používá klienta rozhraní .NET Framework. 

Další informace o zasílání zpráv služby Service Bus najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
