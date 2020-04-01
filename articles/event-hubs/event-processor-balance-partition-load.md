---
title: Vyvažte zatížení oddílů mezi více instancemi – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Popisuje, jak vyvážit zatížení oddílu mezi více instancemi vaší aplikace pomocí procesoru událostí a sady Azure Event Hubs SDK.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: bf90120157bf64bd62a3b5ec9d8a6b2c6260e024
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398300"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Vyvážení zatížení oddílu mezi více instancemi aplikace
Chcete-li škálovat aplikaci pro zpracování událostí, můžete spustit více instancí aplikace a nechat ji vyvážit zatížení mezi sebou. Ve starších verzích [EventProcessorHost](event-hubs-event-processor-host.md) vám umožnilo vyvážit zatížení mezi více instancemi programu a událostmi kontrolního bodu při příjmu. V novějších verzích (5.0 a dále), **EventProcessorClient** (.NET a Java) nebo **EventHubConsumerClient** (Python a JavaScript) umožňuje provést totéž. Vývojový model je jednodušší pomocí událostí. Události, které vás zajímají, se přihlásíte registrací obslužné rutiny události.

Tento článek popisuje ukázkový scénář pro použití více instancí ke čtení událostí z centra událostí a potom vám poskytne podrobnosti o funkcích klienta procesoru událostí, který umožňuje přijímat události z více oddílů najednou a vyrovnávání zatížení s ostatními příjemci, kteří používají stejné centrum událostí a skupinu spotřebitelů.

> [!NOTE]
> Klíčem k škálování pro event huby je myšlenka rozdělených spotřebitelů. Na rozdíl od [konkurenčních spotřebitelů](https://msdn.microsoft.com/library/dn568101.aspx) vzor, rozdělený spotřebitelský vzor umožňuje vysoké měřítko odstraněním konflikty úzkým hrdlem a usnadnění min.-end paralelismus.

## <a name="example-scenario"></a>Příklad scénáře

Jako příklad scénáře zvažte domácí bezpečnostní společnost, která monitoruje 100 000 domácností. Každou minutu získává data z různých senzorů, jako je detektor pohybu, senzor otevření dveří / oken, detektor rozbití skla a tak dále, instalovaný v každé domácnosti. Společnost poskytuje webové stránky pro obyvatele sledovat činnost svého domova v téměř reálném čase.

Každý senzor odesílá data do centra událostí. Centrum událostí je nakonfigurováno s 16 oddíly. Na náročné mši potřebujete mechanismus, který může číst tyto události, konsolidovat je (filtr, agregovat a tak dále) a vypisovat agregaci do objektu blob úložiště, který se pak promítá na uživatelsky přívětivou webovou stránku.

## <a name="write-the-consumer-application"></a>Napsat spotřebitelskou aplikaci

Při navrhování příjemce v distribuovaném prostředí scénář musí zpracovat následující požadavky:

1. **Měřítko:** Vytvořte více spotřebitelů, přičemž každý spotřebitel převezme vlastnictví čtení z několika oddílů Centra událostí.
2. **Vyvážení zatížení:** Zvyšujte nebo snižujte spotřebitele dynamicky. Například při přidání nového typu senzoru (například detektoru oxidu uhelnatého) do každé domácnosti se počet událostí zvyšuje. V takovém případě operátor (člověk) zvyšuje počet instancí příjemce. Potom fond spotřebitelů můžete vyvážit počet oddílů, které vlastní, sdílet zatížení s nově přidané spotřebitele.
3. **Bezproblémový životopis při selhání:** Pokud spotřebitel (**spotřebitel A)** selže (například virtuální počítač hostující spotřebitele náhle havaruje), pak ostatní spotřebitelé mohou vyzvednout oddíly vlastněné **spotřebitelem A** a pokračovat. Také bod pokračování, nazvaný *kontrolní bod* nebo *posun*, by měl být v přesném bodě, ve kterém **příjemce A** selhal, nebo mírně před tím.
4. **Spotřebovávat události:** Zatímco předchozí tři body se zabývají správou spotřebitele, musí existovat kód, který spotřebuje události a udělá s ním něco užitečného. Agregujte ho například a nahrajte do úložiště objektů blob.

## <a name="event-processor-or-consumer-client"></a>Procesor událostí nebo klient příjemce

Nemusíte vytvářet vlastní řešení pro splnění těchto požadavků. Sady SDK centra událostí Azure poskytují tuto funkci. V sadách SDK .NET nebo Java používáte klienta procesoru událostí (EventProcessorClient) a v sadách SDK skriptů Pythonu a Java používáte klient EventHubConsumerClient. Ve staré verzi sady SDK tyto funkce podporoval hostitel procesoru událostí (EventProcessorHost).

Pro většinu produkčních scénářů doporučujeme použít klienta procesoru událostí pro čtení a zpracování událostí. Klient procesoru je určen k poskytování robustní prostředí pro zpracování událostí ve všech oddílech centra událostí v performant a odolné proti chybám způsobem a zároveň poskytuje prostředky pro kontrolní bod jeho průběhu. Klienti zpracovatelů událostí jsou také schopni pracovat kooperativně v rámci skupiny spotřebitelů pro dané centrum událostí. Klienti budou automaticky spravovat distribuci a vyvažování práce, jakmile budou instance pro skupinu k dispozici nebo nebudou k dispozici.

## <a name="partition-ownership-tracking"></a>Sledování vlastnictví oddílu

Instance procesoru událostí obvykle vlastní a zpracovává události z jednoho nebo více oddílů. Vlastnictví oddílů je rovnoměrně rozděleno mezi všechny instance aktivního procesoru událostí přidružené k centru událostí a kombinaci skupiny spotřebitelů. 

Každý procesor událostí je uveden jedinečný identifikátor a nároky vlastnictví oddílů přidáním nebo aktualizací položky v úložišti kontrolních bodů. Všechny instance procesoru událostí pravidelně komunikují s tímto úložištěm, aby aktualizovaly svůj vlastní stav zpracování a také se dozvěděly o dalších aktivních instancích. Tato data se pak používají k vyvážení zatížení mezi aktivními procesory. Nové instance můžete připojit do fondu zpracování navertovat. Když instance přejít dolů, z důvodu selhání nebo vertikálně, vlastnictví oddílu je řádně převedena na jiné aktivní procesory.

Záznamy vlastnictví oddílů v úložišti kontrolních bodů zaznamenávají obor názvů Centra událostí, název centra událostí, skupinu spotřebitelů, identifikátor procesoru událostí (označovaný také jako vlastník), ID oddílu a čas poslední změny.



| Obor názvů služby Event Hubs               | Název centra událostí | **Skupina uživatelů** | Vlastník                                | ID oddílu | Čas poslední změny  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Každá instance procesoru událostí získá vlastnictví oddílu a spustí zpracování oddílu z posledního známého [kontrolního bodu](# Checkpointing). Pokud procesor selže (virtuální hod se vypne), pak ostatní instance zjistit tím, že při pohledu na čas poslední změny. Jiné instance se pokusí získat vlastnictví oddílů dříve vlastněných neaktivní instance a úložiště kontrolních bodů zaručuje, že pouze jedna z instancí se podaří nárokovat vlastnictví oddílu. Takže v daném okamžiku je maximálně jeden procesor příjem událostí z oddílu.

## <a name="receive-messages"></a>Příjem zpráv

Při vytváření procesoru událostí určíte funkce, které budou zpracovávat události a chyby. Každé volání funkce, která zpracovává události, přináší jednu událost z určitého oddílu. Je vaší zodpovědností zvládnout tuto událost. Pokud chcete, aby se ujistil, že spotřebitel zpracovává každou zprávu alespoň jednou, musíte napsat vlastní kód s logikou opakování. Ale buďte opatrní ohledně otrávených zpráv.

Doporučujeme, abyste dělali věci poměrně rychle. To znamená, že dělat co nejméně zpracování, jak je to možné. Pokud potřebujete zapisovat do úložiště a provést nějaké směrování, je lepší použít dvě skupiny spotřebitelů a mít dva procesory událostí.

## <a name="checkpointing"></a>Vytváření kontrolních bodů

*Vytváření kontrolních bodů* je proces, při kterém procesor událostí označí nebo potvrdí pozici poslední úspěšně zpracované události v rámci oddílu. Označení kontrolního bodu se obvykle provádí v rámci funkce, která zpracovává události a dochází na základě oddílu v rámci skupiny spotřebitelů. 

Pokud se procesor událostí odpojí od oddílu, jiná instance může pokračovat ve zpracování oddílu v kontrolním bodu, který byl dříve potvrzen posledním procesorem tohoto oddílu v této skupině spotřebitelů. Když se procesor připojí, předá posun do centra událostí a určí umístění, ve kterém má být možné začít číst. Tímto způsobem můžete použít vytváření kontrolních bodů k označení událostí jako "úplné" pro příjem dat aplikací a poskytnout odolnost proti chybám při zpracování procesoru událostí přejde dolů. Ke starším datům se je možné vrátit tak, že určíte nižší posun od tohoto kontrolního bodu. 

Při provádění kontrolního bodu k označení události jako zpracované, je přidána nebo aktualizována položka v úložišti kontrolního bodu s posunem události a pořadovým číslem události. Uživatelé by měli rozhodnout o četnosti aktualizace kontrolního bodu. Aktualizace po každé úspěšně zpracované události může mít vliv na výkon a náklady, protože aktivuje operaci zápisu do základního úložiště kontrolních bodů. Také kontrolní body každou jednotlivou událost svědčí o vzorky zasílání zpráv ve frontě, pro které fronta service bus může být lepší volbou než centrum událostí. Myšlenka event hubů spoáčita je, že dostanete doručení "alespoň jednou" ve velkém měřítku. Tím, že vaše navazující systémy idempotentní, je snadné obnovit z chyb nebo restartování, které mají za následek stejné události přijaté vícekrát.

> [!NOTE]
> Pokud používáte Azure Blob Storage jako úložiště kontrolních bodů v prostředí, které podporuje jinou verzi sady Storage Blob SDK než ty, které jsou obvykle dostupné v Azure, budete muset použít kód ke změně verze rozhraní API služby úložiště na konkrétní verzi podporovanou tímto prostředím. Například pokud používáte [Centra událostí na Azure Stack Hub verze 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze pro službu Storage je verze 2017-11-09. V takovém případě musíte použít kód k cílení verze rozhraní API služby úložiště na 2017-11-09. Příklad, jak cílit na konkrétní verzi rozhraní API úložiště, najdete v těchto ukázkách na GitHubu: 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithOlderStorageVersion.java)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.js) nebo [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/receiveEventsWithDownleveledStorage.ts)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/event_processor_blob_storage_example_with_storage_api_version.py)

## <a name="thread-safety-and-processor-instances"></a>Instance bezpečnosti vláken a procesoru

Ve výchozím nastavení je procesor událostí nebo příjemce bezpečný pro přístup z více vláken a chová se synchronně. Když události dorazí pro oddíl, je volána funkce, která zpracovává události. Následné zprávy a volání této funkce fronty na pozadí jako čerpadlo zprávy nadále běžet na pozadí na jiných vláknech. Tato bezpečnost podprocesu odstraňuje potřebu kolekce bezpečné pro přístup z více vláken a výrazně zvyšuje výkon.

## <a name="next-steps"></a>Další kroky
Podívejte se na následující rychlé spuštění:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
