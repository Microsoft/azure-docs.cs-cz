---
title: Azure Service Bus – procházení zpráv
description: Procházení a prohlížení zpráv Service Bus umožňuje klientovi Azure Service Bus zobrazit výčet všech zpráv ve frontě nebo v předplatném.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520118"
---
# <a name="message-browsing"></a>Procházení zpráv
Procházení nebo prohlížení zpráv umožňuje klientovi Service Bus vytvořit výčet všech zpráv ve frontě nebo předplatném pro účely diagnostiky a ladění.

Operace prohlížet ve frontě nebo v předplatném vrátí maximálně požadovaný počet zpráv. V následující tabulce jsou uvedeny typy zpráv, které jsou vráceny operací prohlížet. 

| Typ zpráv | Obsaženy? | 
| ---------------- | ----- | 
| Aktivní zprávy | Yes |
| Nedoručené zprávy | No | 
| Uzamčené zprávy | Yes |
| Zprávy s vypršenou platností |  Může být (před nedoručenými písmeny) |
| Plánované zprávy | Ano pro fronty. Ne pro předplatná |

## <a name="dead-lettered-messages"></a>Nedoručené zprávy
Chcete-li se podívat na zprávy s **nedoručenými** zprávami ve frontě nebo předplatném, je třeba spustit funkci prohlížet ve frontě nedoručených zpráv přidružených k frontě nebo předplatnému. Další informace najdete v tématu [přístup k frontám nedoručených zpráv](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue).

## <a name="expired-messages"></a>Zprávy s vypršenou platností
Zprávy s vypršenou platností mohou být zahrnuty do výsledků vrácených z operace prohlížet. Spotřebované zprávy a zprávy s vypršenou platností jsou vyčištěny asynchronním spuštěním uvolňování paměti. Tento krok nemusí nutně nastat ihned po vypršení platnosti zpráv. Z tohoto důvodu může náhled operace vracet zprávy, jejichž platnost již vypršela. Tyto zprávy budou odebrány nebo nedoručeny při příštím vyvolání operace Receive v rámci fronty nebo předplatného. Při pokusu o obnovení odložených zpráv z fronty mějte na paměti toto chování. 

Zpráva s vypršenou platností již není oprávněná k pravidelnému načítání jakýmkoli jiným způsobem, a to ani v případě, že je vrátí náhled. Vrácení těchto zpráv je záměrné, protože je to nástroj pro diagnostiku, který odráží aktuální stav protokolu.

## <a name="locked-messages"></a>Uzamčené zprávy
Prohlížení také vrátí zprávy, které byly **uzamčeny** a aktuálně jsou zpracovávány jinými příjemci. Vzhledem k tomu, že funkce náhledu vrátí odpojený snímek, stav zámku zprávy nelze pozorovat při prohlížení zpráv.

## <a name="peek-apis"></a>Náhled rozhraní API
Náhled funguje ve frontách, předplatných a jejich frontách nedoručených zpráv. 

Při opakovaném volání operace prohlížet vypíše všechny zprávy ve frontě nebo předplatném, od nejnižšího dostupného pořadového čísla až po nejvyšší. Je to pořadí, ve kterém byly zprávy zařazeny do fronty, nikoli pořadí, ve kterém mohou být zprávy nakonec načteny.

Můžete také předat SequenceNumber k operaci prohlížet. Použije se k určení, kde začít prohlížet z. Můžete provést následná volání operace náhledu bez určení parametru, který se má dále zobrazit.

## <a name="next-steps"></a>Další kroky
Vyzkoušejte si ukázky v jazyce podle vašeho výběru a prozkoumejte funkci prohlížení nebo procházení zpráv:

- [Azure Service Bus ukázek klientských knihoven pro Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  Ukázka **zprávy s náhledem**
- [Azure Service Bus ukázek klientských knihoven pro Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  Ukázka **receive_peek. py**
- [Azure Service Bus ukázek klientských knihoven pro JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  Ukázka **browseMessages.js**
- [Azure Service Bus Ukázky klientské knihovny pro TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  Ukázka **browseMessages. TS**
- [Ukázky Azure. Messaging. ServiceBus pro .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) – viz náhled metod na třídách přijímače v [referenční dokumentaci](/dotnet/api/azure.messaging.servicebus).

Vyhledejte ukázky pro starší klientské knihovny .NET a Java níže:
- [Ukázky Microsoft. Azure. ServiceBus pro .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  Ukázka **procházení zpráv (Náhled)** 
- [ukázky Azure-ServiceBus pro Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  Ukázka **procházení zpráv** 
