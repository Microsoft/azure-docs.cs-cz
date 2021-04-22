---
title: Azure Service Bus – odložení zprávy
description: Tento článek vysvětluje, jak odložit doručování Azure Service Busch zpráv. Zpráva zůstane ve frontě nebo předplatném, ale je vyhrazená.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869486"
---
# <a name="message-deferral"></a>Odložení zpráv
Když klient fronty nebo předplatného obdrží zprávu, která je ochotná zpracovat, ale zpracování není momentálně možné z důvodu zvláštních okolností, má možnost odvodit načtení zprávy do pozdějšího bodu. Zpráva zůstane ve frontě nebo předplatném, ale je vyhrazená.

> [!NOTE]
> Odložené zprávy se [po vypršení platnosti](./service-bus-dead-letter-queues.md#time-to-live)automaticky nepřesunou do fronty nedoručených zpráv. Toto chování je záměrné.

## <a name="sample-scenarios"></a>Ukázkové scénáře
Odložení je funkce vytvořená speciálně pro scénáře zpracování pracovního postupu. Prostředí pracovních postupů mohou vyžadovat zpracování určitých operací v určitém pořadí. Může trvat odložení zpracování některých přijatých zpráv, dokud se nedokončí předepsané předchozí práce, kterou informují jiné zprávy.

Jednoduchým ilustrativním příkladem je pořadí zpracování objednávky, ve kterém se v systému zobrazí oznámení o platbě od externího zprostředkovatele plateb, než se z obchodu dokončí výšení odpovídajícího nákupního Objednávky do systému plnění. V takovém případě může systém plnění odložit zpracování oznámení o platbě, dokud nebude existovat objednávka, ke které se má přidružit. Ve scénářích Rendezvous, kde zprávy z různých zdrojů doplňují pracovní postup, může být v reálném čase pořadí spouštění skutečně správné, ale zprávy odrážející výsledky mohou být doručeny mimo pořadí.

Časově rozlišené prostředky v konečném uspořádání zpráv z pořadí doručení do pořadí, ve kterém je lze zpracovat, ale tyto zprávy bez obav ponechají v úložišti zpráv, pro které je nutné zpracování odložit.

Pokud se zpráva nedá zpracovat, protože konkrétní prostředek pro zpracování této zprávy je dočasně nedostupný, ale zpracování zprávy by se po pár minutách pozastavilo, je třeba si zapamatovat pořadové číslo [naplánované zprávy](message-sequencing.md) během několika minut a znovu načíst odloženou zprávu při doručení naplánované zprávy. Pokud obslužná rutina zprávy závisí na databázi pro všechny operace a tato databáze je dočasně nedostupná, neměla by používat odložení, ale místo toho může pozastavit příjem zpráv, dokud nebude znovu dostupná databáze. 

## <a name="retrieving-deferred-messages"></a>Načítají se odložené zprávy.
Odložené zprávy zůstávají v hlavní frontě společně se všemi ostatními aktivními zprávami (na rozdíl od zpráv s nedoručenými zprávami, které jsou v podfrontě uložené), ale už se nedají přijímat pomocí běžných operací příjmu. Odložené zprávy lze zjistit pomocí [procházení zpráv](message-browsing.md) , pokud aplikace ztratí jejich sledování.

Chcete-li načíst odloženou zprávu, její vlastník je zodpovědný za zapamatování pořadového čísla při jeho odložení. Každý příjemce, který zná pořadové číslo odložené zprávy, může později zprávu přijmout pomocí metod Receive, které přebírají pořadové číslo jako parametr. Další informace o pořadových číslech najdete v tématu sekvence [zpráv a časová razítka](message-sequencing.md).

## <a name="next-steps"></a>Další kroky
Vyzkoušejte si ukázky v jazyce podle vašeho výběru, abyste prozkoumali Azure Service Bus funkce. 

- [Azure Service Bus ukázek klientských knihoven pro Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus ukázek klientských knihoven pro Python](/samples/azure/azure-sdk-for-python/servicebus-samples/) – viz Ukázka **receive_deferred_message_queue. py** . 
- [Azure Service Bus ukázek klientských knihoven pro JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/) – viz Ukázka **Rozšířené/deferral.js** . 
- [Azure Service Bus ukázek klientských knihoven pro TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/) – viz Ukázka **Rozšířené/odložení. TS** . 
- [Ukázky Azure. Messaging. ServiceBus pro .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) – viz Ukázka **Vyrovnávání zpráv** . 

Vyhledejte ukázky pro starší klientské knihovny .NET a Java níže:
- [Microsoft. Azure. ServiceBus Samples for .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) – viz Ukázka **odložení** . 
- [Ukázky Azure-ServiceBus pro Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
