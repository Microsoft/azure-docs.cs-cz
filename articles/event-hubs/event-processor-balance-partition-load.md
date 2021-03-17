---
title: Vyrovnávání zatížení oddílu napříč několika instancemi – Azure Event Hubs | Microsoft Docs
description: Popisuje, jak vyrovnávat zatížení oddílů mezi několika instancemi aplikace pomocí procesoru událostí a sady Azure Event Hubs SDK.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: af307058d0eda6b96c0811bccc245c09e2bdd27d
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025040"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Vyrovnávání zatížení oddílu napříč několika instancemi vaší aplikace
Chcete-li škálovat aplikaci pro zpracování událostí, můžete spustit více instancí aplikace a vyrovnávat zatížení mezi sebou. Ve starších verzích vám [EventProcessorHost](event-hubs-event-processor-host.md) povolil vyrovnávat zatížení mezi několika instancemi programu a události kontrolního bodu při příjmu. V novějších verzích (5,0 a vyšší), **EventProcessorClient** (.NET a Java) nebo **EventHubConsumerClient** (Python a JavaScript) vám umožňuje provádět stejné. Vývojový model je jednodušší pomocí událostí. Přihlásíte se k odběru událostí, na které vás zajímáte, registrací obslužné rutiny události. Pokud používáte starou verzi klientské knihovny, přečtěte si následující Příručky k migraci: [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md)a [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md).

Tento článek popisuje vzorový scénář pro použití více instancí ke čtení událostí z centra událostí a pak vám poskytne podrobné informace o funkcích klienta procesoru událostí, který umožňuje přijímat události z více oddílů najednou a vyrovnávat zatížení s ostatními uživateli, kteří používají stejné centrum událostí a skupinu uživatelů.

> [!NOTE]
> Klíčem k horizontálnímu navýšení kapacity pro Event Hubs je nápad rozdělit uživatele na oddíly. Na rozdíl od vzorce [konkurenčních spotřebitelů](/previous-versions/msp-n-p/dn568101(v=pandp.10)) umožňuje vzor rozděleného uživatele vysoké škálování odebráním kritických bodů pro spor a zjednodušením koncového paralelismu.

## <a name="example-scenario"></a>Ukázkový scénář

Jako ukázkový scénář si představte firemní zabezpečení, které monitoruje 100 000 domů. Každou minutu získává data z různých senzorů, jako je detektor pohybu, otevřený senzor dveří nebo oken, detektor skla a tak dále, nainstalovaný v každé domácnosti. Společnost poskytuje web pro rezidenty, které sledují činnost svých domů téměř v reálném čase.

Každý senzor nahraje data do centra událostí. Centrum událostí je nakonfigurované s 16 oddíly. Na náročném konci budete potřebovat mechanismus, který může číst tyto události, konsolidovat je (filtrovat, agregovat atd.) a vypsat agregovanou hodnotu do objektu BLOB úložiště, který je pak navržený na uživatelsky přívětivou webovou stránku.

## <a name="write-the-consumer-application"></a>Zápis aplikace příjemce

Při návrhu spotřebitele v distribuovaném prostředí musí tento scénář splňovat následující požadavky:

1. **Měřítko:** Vytvořte více uživatelů, přičemž každý příjemce převezme vlastnictví čtení z několika oddílů Event Hubs.
2. **Vyrovnávání zatížení:** Dynamické zvýšení nebo snížení počtu příjemců Například při přidání nového typu snímače (např. oxidu uhelnat) do každé domů se zvýší počet událostí. V takovém případě operátor (lidské) zvyšuje počet instancí příjemců. Fond příjemců pak může znovu vyrovnávat počet oddílů, které vlastní, a sdílet zatížení s nově přidanými spotřebiteli.
3. **Bezproblémové obnovení při selhání:** Pokud se uživatel (**spotřebitel a**) nepovede (například virtuální počítač, který hostuje nějakého uživatele, dojde k výpadku), ostatní spotřebitelé si můžou vybrat oddíly vlastněné **zákazníkem** a a pokračovat. Bod pokračování, označovaný jako *kontrolní bod* nebo *posun*, by měl být v přesném okamžiku, kdy se **příjemce a** nezdařil, nebo mírně předtím.
4. **Spotřebovávat události:** Zatímco předchozí tři body se týkají správy příjemce, musí existovat kód pro využívání událostí a co nejužitečnější je s ním. Například agreguje a nahrajte ho do úložiště objektů BLOB.

## <a name="event-processor-or-consumer-client"></a>Procesor událostí nebo klient příjemce

K splnění těchto požadavků nemusíte sestavovat vlastní řešení. Tuto funkci poskytují sady Azure Event Hubs SDK. V sadách .NET nebo Java SDK použijete klienta procesoru událostí (EventProcessorClient) a sady SDK v Pythonu a JavaScriptu, použijete EventHubConsumerClient. Ve staré verzi sady SDK se jednalo o hostitele procesoru událostí (EventProcessorHost), který tyto funkce podporuje.

Pro většinu produkčních scénářů doporučujeme použít klienta procesoru událostí pro čtení a zpracování událostí. Klient procesoru má k dispozici robustní prostředí pro zpracování událostí ve všech oddílech centra událostí v rámci výkonného a odolného přenosu, který poskytuje prostředky pro kontrolní body. Klienti procesorů událostí můžou spolupracovat v rámci kontextu skupiny uživatelů pro dané centrum událostí. Klienti budou automaticky spravovat distribuci a vyrovnávání práce, protože instance budou k dispozici nebo nebudou pro skupinu k dispozici.

## <a name="partition-ownership-tracking"></a>Sledování vlastnictví oddílu

Instance procesoru událostí obvykle vlastní a zpracovává události z jednoho nebo více oddílů. Vlastnictví oddílů je rovnoměrně distribuované mezi všemi instancemi aktivních procesorů událostí přidružených k kombinaci centra událostí a skupiny uživatelů. 

Každému procesoru událostí se předává jedinečný identifikátor a vlastnictví deklarací oddílů přidáním nebo aktualizací položky v úložišti kontrolních bodů. Všechny instance procesoru událostí komunikují s tímto úložištěm pravidelně a aktualizují svůj vlastní stav zpracování i informace o dalších aktivních instancích. Tato data se pak použijí pro vyrovnání zatížení mezi aktivními procesory. Pro horizontální navýšení kapacity se můžou nové instance připojit ke fondu zpracování. Když instance dostanou mimo provoz, a to z důvodu selhání nebo snížení kapacity, vlastnictví oddílu se řádně přenáší na jiné aktivní procesory.

Záznamy o vlastnictví oddílu v úložišti kontrolního bodu udržují přehled o Event Hubs obor názvů, název centra událostí, skupinu uživatelů, identifikátor procesoru událostí (označovaný také jako vlastník), ID oddílu a čas poslední změny.



| Obor názvů služby Event Hubs               | Název centra událostí | **Skupina uživatelů** | Vlastník                                | ID oddílu | Čas poslední změny  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Každá instance procesoru událostí získá vlastnictví oddílu a spustí zpracování oddílu z posledního známého [kontrolního bodu](# Checkpointing). Pokud dojde k chybě procesoru (virtuální počítač je vypnutý), vyhledá jiné instance tím, že prohlíží čas poslední změny. Jiné instance se pokusí získat vlastnictví oddílů, které dříve vlastnila neaktivní instance, a úložiště kontrolního bodu zaručuje, že se při deklaracích vlastnictví oddílu úspěšně vytvoří jenom jedna z těchto instancí. Takže v jakémkoli časovém okamžiku je k dispozici maximálně jeden procesor, který přijímá události z oddílu.

## <a name="receive-messages"></a>Příjem zpráv

Když vytváříte procesor událostí, zadáváte funkce, které budou zpracovávat události a chyby. Každé volání funkce, která zpracovává události, zajišťuje jednu událost z konkrétního oddílu. Tato událost se zpracovává vaší zodpovědností. Pokud se chcete ujistit, že příjemce zpracuje alespoň jednou zprávu, musíte napsat vlastní kód s logikou opakování. Buďte ale opatrní v souvislosti s poškozenými zprávami.

Doporučujeme, abyste provedli relativně rychle. To znamená co nejmenší zpracování. Pokud potřebujete zapisovat do úložiště a udělat si nějaké směrování, je lepší použít dvě skupiny uživatelů a mít dva procesory událostí.

## <a name="checkpointing"></a>Vytváření kontrolních bodů

*Kontrolní bod* je proces, při kterém procesor událostí označí nebo potvrdí pozici poslední úspěšné zpracovávané události v rámci oddílu. Označení kontrolního bodu se obvykle provádí ve funkci, která zpracovává události a probíhá na jednotlivých oddílech v rámci skupiny příjemců. 

Pokud se procesor událostí z oddílu odpojí, může jiná instance pokračovat ve zpracování oddílu v kontrolním bodu, který byl dřív potvrzen posledním procesorem tohoto oddílu v této skupině příjemců. Když se procesor připojí, předá posun do centra událostí, aby určil umístění, ve kterém se má začít číst. Tímto způsobem můžete pomocí kontrolního bodu provádět označení událostí jako "dokončeno" v aplikacích pro příjem dat a zajištění odolnosti při výpadku procesoru událostí. Je možné vrátit se ke starším datům zadáním nižšího posunu od tohoto procesu vytváření kontrolního bodu. 

Při provádění kontrolního bodu za účelem označení události jako zpracovaného je položka v úložišti kontrolního bodu přidána nebo aktualizována pomocí posunu události a čísla sekvence. Uživatelé by se měli rozhodnout frekvence aktualizace kontrolního bodu. Aktualizace po všech úspěšně zpracovaných událostech může mít dopad na výkon a náklady, protože aktivuje operaci zápisu do základního úložiště kontrolního bodu. Vytváření kontrolních bodů pro každou jednotlivou událost je také informativní. vzor zasílání zpráv ve frontě, pro který Service Bus frontu, může být lepší volbou než centrum událostí. Nápad za Event Hubs je, že se vám dostanete aspoň jednou doručování ve skvělém měřítku. Díky tomu, že vaše systémy pro příjem dat idempotentní, je snadné se zotavit z chyb nebo restartovat, což vede k tomu, že se stejné události přijímají vícekrát.

> [!NOTE]
> Pokud používáte Azure Blob Storage jako úložiště kontrolního bodu v prostředí, které podporuje jinou verzi sady SDK pro úložiště objektů blob, než jaké jsou běžně dostupné v Azure, budete muset použít kód ke změně verze rozhraní API služby úložiště na konkrétní verzi podporovanou tímto prostředím. Pokud například používáte [Event Hubs v centru Azure Stack verze 2002](/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze služby úložiště je verze 2017-11-09. V takovém případě je nutné použít kód pro cílení na verzi rozhraní API služby úložiště na 2017-11-09. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v těchto ukázkách na GitHubu: 
> - [Rozhraní .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript) nebo  [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>Bezpečnost vlákna a instance procesoru

Ve výchozím nastavení je funkce, která zpracovává události, volána sekvenčně pro daný oddíl. Následné události a volání této funkce ze stejného oddílu se zařadí do fronty na pozadí, protože čerpadlo událostí pokračuje v běhu na pozadí v jiných vláknech. Události z různých oddílů se dají souběžně zpracovat a veškerý sdílený stav, ke kterému se dá dostat přes oddíly, se musí synchronizovat.

## <a name="next-steps"></a>Další kroky
Podívejte se na následující rychlé starty:

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
