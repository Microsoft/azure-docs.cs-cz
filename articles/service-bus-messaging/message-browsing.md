---
title: Azure Service Bus – procházení zpráv
description: Procházení a prohlížení zpráv Service Bus umožňuje klientovi Azure Service Bus zobrazit výčet všech zpráv ve frontě nebo v předplatném.
ms.topic: article
ms.date: 11/11/2020
ms.openlocfilehash: c52c9c967d4eada1a931e188ed4d25f7691cfb91
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94553637"
---
# <a name="message-browsing"></a>Procházení zpráv

Procházení nebo prohlížení zpráv umožňuje klientovi Service Bus vytvořit výčet všech zpráv ve frontě nebo předplatném pro účely diagnostiky a ladění.

Operace prohlížet ve frontě vrátí všechny zprávy ve frontě, nikoli pouze ty, které jsou k dispozici pro okamžité získání `Receive()` nebo `OnMessage()` smyčku. `State`Vlastnost každé zprávy obsahuje informace o tom, zda je zpráva aktivní (k dispozici pro příjem), [odložena](message-deferral.md)nebo [naplánována](message-sequencing.md). Operace prohlížet u předplatného vrátí všechny zprávy kromě naplánovaných zpráv v protokolu zpráv odběru. 

Spotřebované zprávy a zprávy s vypršenou platností jsou vyčištěny asynchronním spuštěním uvolňování paměti. Tento krok nemusí nutně nastat ihned po vypršení platnosti zpráv. To je důvod, proč `Peek` mohou vracet zprávy, jejichž platnost již vypršela. Tyto zprávy budou odebrány nebo nedoručeny při příštím vyvolání operace Receive v rámci fronty nebo předplatného. Při pokusu o obnovení odložených zpráv z fronty mějte na paměti toto chování. Zpráva s vypršenou platností již není oprávněná k pravidelnému načítání jakýmkoli jiným způsobem, a to ani v případě, že je vrátí náhled. Vrácení těchto zpráv je záměrné, protože je to nástroj pro diagnostiku, který odráží aktuální stav protokolu.

Prohlížení také vrátí zprávy, které byly uzamčeny a aktuálně jsou zpracovávány jinými příjemci. Vzhledem k tomu, že funkce náhledu vrátí odpojený snímek, stav zámku zprávy nelze pozorovat při prohlížení zpráv. Vlastnosti [LockedUntilUtc](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.lockeduntilutc) a [LockToken](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.locktoken#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_LockToken) vyvolávají chybu [InvalidOperationException](/dotnet/api/system.invalidoperationexception) , když se aplikace pokusí o jejich čtení.

## <a name="peek-apis"></a>Náhled rozhraní API

Metody [prohlížet/PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_PeekAsync) a [PeekBatch/PeekBatchAsync](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatchasync#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatchAsync_System_Int64_System_Int32_) existují v klientských knihovnách .NET a Java a na objektech přijímače: **MessageReceiver**, **MessageSession**. Náhled funguje na frontách, předplatných a jejich příslušných frontách nedoručených zpráv.

Při opakovaném pokusu je **Náhled** vyčísluje všechny zprávy ve frontě nebo v protokolu předplatného, od nejnižšího počtu dostupných pořadových čísel až po nejvyšší. Je to pořadí, ve kterém byly zprávy zařazeny do fronty, nikoli pořadí, ve kterém mohou být zprávy nakonec načteny.

[PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch#Microsoft_ServiceBus_Messaging_QueueClient_PeekBatch_System_Int32_) načte více zpráv a vrátí je jako výčet. Pokud nejsou k dispozici žádné zprávy, je objekt výčtu prázdný, nikoli null.

Můžete také použít přetížení metody s [SequenceNumber](/dotnet/api/microsoft.azure.servicebus.message.systempropertiescollection.sequencenumber#Microsoft_Azure_ServiceBus_Message_SystemPropertiesCollection_SequenceNumber) , na které se má spustit, a pak volat přetížení metody bez parametrů k dalšímu výčtu. Funkce **PeekBatch** jsou ekvivalentní, ale načítá sadu zpráv najednou.

## <a name="next-steps"></a>Další kroky

Další informace o Service Bus zasílání zpráv najdete v následujících tématech:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
