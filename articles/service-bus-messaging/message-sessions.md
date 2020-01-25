---
title: Relace Azure Service Bus zpráv | Microsoft Docs
description: Zpracování sekvencí Azure Service Busch zpráv s relacemi.
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
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 7da3c3de5074df80c676238e4d43dbd677b0a3b4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720227"
---
# <a name="message-sessions-first-in-first-out-fifo"></a>Relace zpráv: první v, první (FIFO) 

Microsoft Azure Service Bus relace umožňují společné a seřazené zpracování neohraničených sekvencí souvisejících zpráv. Pokud chcete v Service Bus realizovat jistotu FIFO, použijte relace. Service Bus není začínat na povaze vztahu mezi zprávami a také nedefinuje konkrétní model pro zjištění, kde začíná nebo končí sekvence zpráv.

> [!NOTE]
> Úroveň Basic Service Bus nepodporuje relace. Úrovně Standard a Premium podporují relace. Další informace najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).

Každý odesilatel může vytvořit relaci při odesílání zpráv do tématu nebo do fronty nastavením vlastnosti [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) na některý identifikátor definovaný aplikací, který je pro relaci jedinečný. Na úrovni protokolu AMQP 1,0 se tato hodnota mapuje na vlastnost *Group-ID* .

U front a předplatných, které pracují s relacemi, se relace nacházejí v případě, že existuje alespoň jedna zpráva s [identifikátorem SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)relace. Po existující relaci není k dispozici žádný definovaný čas nebo rozhraní API pro dobu, kdy relace vyprší nebo zmizí. Teoreticky se dá přijmout zpráva pro relaci dnes, další zpráva v roce a v případě, že se **SessionID** shodují, relace je stejná jako v Service Bus perspektivě.

Obvykle však aplikace má jasný pojem, kde se spustí a končí sada souvisejících zpráv. Service Bus nenastavuje žádná konkrétní pravidla.

Příkladem, jak určit sekvenci pro přenos souboru, je nastavit vlastnost **popisek** první zprávy na **Start**, pro mezilehlé zprávy na **obsah**a na **konec**poslední zprávy. Relativní umístění zpráv obsahu lze vypočítat jako aktuální rozdíl *SequenceNumber* zprávy ze *SequenceNumber* **spuštění** zprávy.

Funkce Session v Service Bus umožňuje určitou operaci přijímání, ve formě [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) v rozhraních API C# a Java. Tuto funkci povolíte nastavením vlastnosti [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) ve frontě nebo předplatném pomocí Azure Resource Manager nebo nastavením příznaku na portálu. To se vyžaduje předtím, než se pokusíte použít související operace rozhraní API.

Na portálu nastavte příznak následujícím zaškrtávacím políčkem:

![][2]

> [!NOTE]
> Když jsou povoleny relace ve frontě nebo v předplatném, klientské aplikace ***již*** nemohou odesílat a přijímat pravidelné zprávy. Všechny zprávy musí být odesílány v rámci relace (nastavením ID relace) a přijaty přijetím relace.

Rozhraní API pro relace existují na klientech front a předplatných. Existuje imperativní model, který řídí, kdy jsou přijímány relace a zprávy, a model založený na obslužných rutinách, podobně jako- *Message*, který skrývá složitost správy přijímací smyčky.

## <a name="session-features"></a>Funkce relace

Relace poskytují souběžné demultiplexování datových proudů zpráv při zachování a zaručení objednaného doručení.

![][1]

Příjemce [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) je vytvořen klientem, který přijímá relaci. Klient volá [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) nebo [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) v C#. V modelu reaktivního zpětného volání zaregistruje obslužnou rutinu relace.

Když je objekt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) přijatý a když ho uchovává klient, má tento klient exkluzivní zámek na všech zprávách s [identifikátorem SessionID](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) relace, který existuje ve frontě nebo předplatném, a také na všech zprávách s **identifikátorem SessionID** , který je stále v době, kdy se relace koná.

Zámek se uvolní při volání funkce **Close** nebo **CloseAsync** nebo po vypršení platnosti zámku v případech, kdy aplikace nemůže provést operaci Zavřít. Zámek relace by měl být zpracován jako výhradní zámek u souboru, což znamená, že aplikace by měla relaci ukončit, jakmile ji už nepotřebuje, nebo neočekává žádné další zprávy.

Pokud je z fronty vyžádané více souběžných přijímačů, zprávy patřící do určité relace jsou odesílány na konkrétního příjemce, který v současné době zámek pro danou relaci obsahuje. V této operaci se prokládaný datový proud zpráv umístěný v jedné frontě nebo předplatném čistí čistě proti různým přijímačům a jejich přijímače můžou být také živé v různých klientských počítačích, protože správa zámků se stane na straně služby uvnitř Service Bus.

Na předchozím obrázku vidíte tři přijímače souběžných relací. Jedna relace s `SessionId` = 4 nemá žádné aktivní, vlastnícího klienta, což znamená, že z této konkrétní relace nebudou doručovány žádné zprávy. Relace funguje mnoha způsoby jako podfrontu.

Zámek relace uchovávaný příjemcem relace je zastřešující pro zámky zpráv používané režimem vyrovnávání *proti prohlížení* . Přijímač nemůže mít současně dvě zprávy "v letu", ale zprávy musí být zpracovány v daném pořadí. Novou zprávu lze získat pouze v případě, že byla předchozí zpráva dokončena nebo byla nedoručena. Zrušením zprávy dojde k opětovnému doručení stejné zprávy s další operací Receive.

## <a name="message-session-state"></a>Stav relace zprávy

Když jsou pracovní postupy zpracovávány ve vysoce škálovatelných cloudových systémech s vysokou dostupností, musí být obslužná rutina pracovního postupu přidružená k určité relaci schopna zotavení z neočekávaných selhání a také může obnovit částečně dokončenou práci na jiném proces nebo počítač, ze kterého práce začala.

Zařízení stavu relace umožňuje v rámci zprostředkovatele zadat anotaci zprávy definované aplikací, aby zaznamenaný stav zpracování relativní k této relaci byl okamžitě dostupný, když je relace získána novým procesorem.

Z Service Bus perspektivy je stav relace zprávy neprůhledný binární objekt, který může uchovávat data velikosti jedné zprávy, což je 256 KB pro Service Bus Standard a 1 MB pro Service Bus Premium. Stav zpracování relativní vůči relaci lze uchovávat v rámci stavu relace nebo stav relace může ukazovat na některé umístění úložiště nebo databázový záznam, který obsahuje tyto informace.

Rozhraní API pro správu stavu relace, [setstate](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)lze nalézt v objektu [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) v rozhraních API C# a Java. Relace, která dříve neměla nastaven stav relace, vrací odkaz s **hodnotou null** pro **GetState**. Vymazání dříve nastaveného stavu relace se provádí s [setstate (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Všimněte si, že stav relace zůstává tak dlouho, dokud se nevymaže (vrací **hodnotu null**) i v případě, že jsou všechny zprávy v relaci spotřebovány.

U všech existujících relací ve frontě nebo předplatném se dá vytvořit výčet pomocí metody **SessionBrowser** v rozhraní Java API a [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) v [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) a [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) v klientovi .NET.

Stav relace uložený ve frontě nebo v předplatném se počítá s kvótou úložiště této entity. Až se aplikace dokončí s relací, doporučuje se, aby aplikace vyčistila svůj zachovaná stav, aby nedocházelo k externím nákladům na správu.

## <a name="impact-of-delivery-count"></a>Dopad počtu doručení

Definice počtu doručení na zprávu v kontextu relací se mírně liší od definice při absenci relací. Zde je souhrn tabulky při zvýšení počtu doručení.

| Scénář | Zvyšuje se počet doručení zprávy |
|----------|---------------------------------------------|
| Relace je přijata, ale zámek relace vyprší (z důvodu vypršení časového limitu). | Ano |
| Relace je přijata, zprávy v relaci nejsou dokončeny (i v případě, že jsou uzamčené) a relace je zavřena. | Ne |
| Relace je přijata, zprávy jsou dokončeny a relace je explicitně zavřena. | Není k dispozici (Jedná se o standardní tok. Z relace se odeberou tyto zprávy.) |

## <a name="next-steps"></a>Další kroky

- Příklad, který používá klienta .NET Framework ke zpracování zpráv s podporou relací, najdete v ukázkách [Microsoft. Azure. ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) nebo [Microsoft. ServiceBus. Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) . 

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
