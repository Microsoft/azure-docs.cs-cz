---
title: Relace Azure Service Bus zpráv | Microsoft Docs
description: Tento článek vysvětluje, jak pomocí relací povolit společné a seřazené zpracování neohraničených sekvencí souvisejících zpráv.
ms.topic: article
ms.date: 01/20/2021
ms.openlocfilehash: 6d316571d69d2e1e73ddca4ccca53c116ee8fa5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680749"
---
# <a name="message-sessions"></a>Relace zpráv
Microsoft Azure Service Bus relace umožňují společné a seřazené zpracování neohraničených sekvencí souvisejících zpráv. Relace se dají použít v vzorcích **First in, First out (FIFO)** a **Request-response** . Tento článek popisuje, jak používat relace k implementaci těchto vzorů při použití Service Bus. 

> [!NOTE]
> Základní Service Bus úrovně nepodporuje relace. Úrovně Standard a Premium podporují relace. Rozdíly mezi těmito úrovněmi najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).

## <a name="first-in-first-out-fifo-pattern"></a>Vzor first-in, First out (FIFO)
Pokud chcete v Service Bus realizovat jistotu FIFO, použijte relace. Service Bus není podrobnější informace o povaze vztahu mezi zprávami a také nedefinuje konkrétní model pro zjištění, kde začíná nebo končí sekvence zpráv.

Každý odesilatel může vytvořit relaci při odesílání zpráv do tématu nebo do fronty nastavením vlastnosti [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId) na některý identifikátor definovaný aplikací, který je pro relaci jedinečný. Na úrovni protokolu AMQP 1,0 se tato hodnota mapuje na vlastnost *Group-ID* .

U front a předplatných, které pracují s relacemi, se relace nacházejí v případě, že existuje alespoň jedna zpráva s [identifikátorem SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid#Microsoft_Azure_ServiceBus_Message_SessionId)relace. Po existující relaci není k dispozici žádný definovaný čas nebo rozhraní API pro dobu, kdy relace vyprší nebo zmizí. Teoreticky se dá přijmout zpráva pro relaci dnes, další zpráva v roce a v případě, že se **SessionID** shodují, relace je stejná jako v Service Bus perspektivě.

Obvykle však aplikace má jasný pojem, kde se spustí a končí sada souvisejících zpráv. Service Bus nenastavuje žádná konkrétní pravidla.

Příkladem, jak určit sekvenci pro přenos souboru, je nastavit vlastnost **popisek** první zprávy na **Start**, pro mezilehlé zprávy na **obsah** a na **konec** poslední zprávy. Relativní umístění zpráv obsahu lze vypočítat jako aktuální rozdíl *SequenceNumber* zprávy ze *SequenceNumber* **spuštění** zprávy.

Funkce Session v Service Bus umožňuje konkrétní operace Receive ve formě [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) v rozhraních API jazyka C# a Java. Tuto funkci povolíte nastavením vlastnosti [requiresSession](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) ve frontě nebo předplatném pomocí Azure Resource Manager nebo nastavením příznaku na portálu. Je nutné, abyste se pokusili použít související operace rozhraní API.

Na portálu nastavte příznak následujícím zaškrtávacím políčkem:

![Snímek obrazovky dialogového okna vytvořit front s vybranou možností povolit relace a popsaný červeně][2]

> [!NOTE]
> Když jsou povoleny relace ve frontě nebo v předplatném, klientské aplikace ***již*** nemohou odesílat a přijímat pravidelné zprávy. Všechny zprávy musí být odesílány v rámci relace (nastavením ID relace) a přijaty přijetím relace.

Rozhraní API pro relace existují na klientech front a předplatných. Existuje imperativní model, který řídí, kdy jsou přijímány relace a zprávy, a model založený na obslužných rutinách, podobně jako- *Message*, který skrývá složitost správy přijímací smyčky.

### <a name="session-features"></a>Funkce relace

Relace poskytují souběžné demultiplexování datových proudů zpráv při zachování a zaručení objednaného doručení.

![Diagram znázorňující, jak funkce relace zachovává seřazené doručení.][1]

Příjemce [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) je vytvořen klientem, který přijímá relaci. Klient volá v jazyce C# [QueueClient. AcceptMessageSession](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesession#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSession) nebo [QueueClient. AcceptMessageSessionAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.acceptmessagesessionasync#Microsoft_ServiceBus_Messaging_QueueClient_AcceptMessageSessionAsync) . V modelu reaktivního zpětného volání zaregistruje obslužnou rutinu relace.

Když je objekt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) přijatý a i když ho drží klient, má tento klient exkluzivní zámek na všech zprávách s [identifikátorem SessionID](/dotnet/api/microsoft.servicebus.messaging.messagesession.sessionid#Microsoft_ServiceBus_Messaging_MessageSession_SessionId) relace, který existuje ve frontě nebo předplatném, a také na všech zprávách s **identifikátorem SessionID** , který je stále přítomen v době, kdy se relace koná.

Zámek se uvolní při volání funkce **Close** nebo **CloseAsync** nebo po vypršení platnosti zámku v případech, kdy aplikace nemůže provést operaci Zavřít. Zámek relace by měl být považován za výhradní zámek u souboru, což znamená, že aplikace by měla relaci ukončit, jakmile ji již nepotřebuje a/nebo neočekává žádné další zprávy.

Pokud je z fronty vyžádané více souběžných přijímačů, zprávy patřící do určité relace jsou odesílány na konkrétního příjemce, který v současné době zámek pro danou relaci obsahuje. V takovém případě se prokládaný datový proud zpráv v jedné frontě nebo předplatném čistí čistě proti různým přijímačům a jejich přijímače můžou být živé i v různých klientských počítačích, protože správa zámků nastane na straně služby v rámci Service Bus.

Na předchozím obrázku vidíte tři přijímače souběžných relací. Jedna relace s `SessionId` = 4 nemá žádné aktivní, vlastnícího klienta, což znamená, že z této konkrétní relace nebudou doručovány žádné zprávy. Relace funguje mnoha způsoby jako podfrontu.

Zámek relace uchovávaný příjemcem relace je zastřešující pro zámky zpráv používané režimem vyrovnávání *proti prohlížení* . Pouze jeden přijímač může mít zámek na relaci. Přijímač může obsahovat mnoho zpráv v letadle, ale zprávy budou přijaty v daném pořadí. Zrušením zprávy dojde k opětovnému doručení stejné zprávy s další operací Receive.

### <a name="message-session-state"></a>Stav relace zprávy

Když jsou pracovní postupy zpracovávány ve vysoce škálovatelných cloudových systémech s vysokou dostupností, musí být obslužná rutina pracovního postupu přidružená k určité relaci schopna zotavení z neočekávaných selhání a může obnovit částečně dokončenou práci na jiném procesu nebo počítači, ze kterého byla práce zahájena.

Zařízení stavu relace umožňuje v rámci zprostředkovatele zadat anotaci zprávy definované aplikací, aby zaznamenaný stav zpracování relativní k této relaci byl okamžitě dostupný, když je relace získána novým procesorem.

Z Service Bus perspektivy je stav relace zprávy neprůhledný binární objekt, který může uchovávat data velikosti jedné zprávy, což je 256 KB pro Service Bus Standard a 1 MB pro Service Bus Premium. Stav zpracování relativní vůči relaci lze uchovávat v rámci stavu relace nebo stav relace může ukazovat na některé umístění úložiště nebo databázový záznam, který obsahuje tyto informace.

Rozhraní API pro správu stavu relace, [setstate](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_) a [GetState](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate#Microsoft_ServiceBus_Messaging_MessageSession_GetState)lze nalézt v objektu [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) v rozhraních API jazyka C# i Java. Relace, která dříve neměla nastaven stav relace, vrací odkaz s **hodnotou null** pro **GetState**. Vymazání dříve nastaveného stavu relace se provádí s [setstate (null)](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate#Microsoft_ServiceBus_Messaging_MessageSession_SetState_System_IO_Stream_).

Stav relace zůstane tak dlouho, dokud se nevymaže (vrátí **hodnotu null**) i v případě, že jsou všechny zprávy v relaci spotřebovány.

U všech existujících relací ve frontě nebo předplatném se dá vytvořit výčet pomocí metody **SessionBrowser** v rozhraní Java API a [GetMessageSessions](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions#Microsoft_ServiceBus_Messaging_QueueClient_GetMessageSessions) na [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) a [SubscriptionClient](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient) v klientovi .NET Framework.

Stav relace uložený ve frontě nebo v předplatném se počítá s kvótou úložiště této entity. Až se aplikace dokončí s relací, doporučuje se, aby aplikace vyčistila svůj zachovaná stav, aby nedocházelo k externím nákladům na správu.

### <a name="impact-of-delivery-count"></a>Dopad počtu doručení

Definice počtu doručení na zprávu v kontextu relací se mírně liší od definice při absenci relací. Zde je souhrn tabulky při zvýšení počtu doručení.

| Scenario | Zvyšuje se počet doručení zprávy |
|----------|---------------------------------------------|
| Relace je přijata, ale zámek relace vyprší (z důvodu vypršení časového limitu). | Yes |
| Relace je přijata, zprávy v relaci nejsou dokončeny (i v případě, že jsou uzamčené) a relace je zavřena. | No |
| Relace je přijata, zprávy jsou dokončeny a relace je explicitně zavřena. | Není k dispozici (Jedná se o standardní tok. Z relace se odeberou tyto zprávy.) |

## <a name="request-response-pattern"></a>Vzor požadavku a odpovědi
[Vzor požadavku a odpovědi](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) je dobře zavedený model integrace, který umožňuje aplikaci odesílatele odeslat žádost a dát příjemci možnost správně odeslat odpověď zpět do aplikace odesílatele. Tento vzor obvykle vyžaduje krátkodobou frontu nebo téma, aby aplikace odesílala odpovědi na. V tomto scénáři představují relace jednoduché alternativní řešení s srovnatelnou sémantikou. 

Více aplikací může odesílat požadavky do jediné fronty požadavků, přičemž konkrétní parametr hlavičky je nastaven pro jedinečnou identifikaci aplikace odesílatele. Aplikace příjemce může zpracovat žádosti přicházející ve frontě a odesílat odpovědi na frontu s povolenými relacemi a nastavit ID relace na jedinečný identifikátor, který odesilatel poslal ve zprávě požadavku. Aplikace, která odeslala požadavek, může následně přijímat zprávy o konkrétním ID relace a správně zpracovat odpovědi.

> [!NOTE]
> Aplikace, která odesílá počáteční požadavky, by měla znát ID relace a použít `SessionClient.AcceptMessageSession(SessionID)` ji k uzamknutí relace, na které očekává odpověď. Je vhodné použít identifikátor GUID, který jedinečně identifikuje instanci aplikace jako ID relace. Ve frontě by neměl být žádná obslužná rutina relace ani `AcceptMessageSession(timeout)` fronta, aby bylo možné zajistit, aby byly odpovědi k dispozici pro uzamknutí a zpracování konkrétními přijímači.

## <a name="next-steps"></a>Další kroky

- Příklad, který používá klienta .NET Framework ke zpracování zpráv s podporou relací, najdete v ukázkách [Microsoft. Azure. ServiceBus](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/Sessions) nebo [Microsoft. ServiceBus. Messaging](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/Sessions) . 

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[1]: ./media/message-sessions/sessions.png
[2]: ./media/message-sessions/queue-sessions.png
