---
title: Vyrovnávání zatížení oddílu napříč několika instancemi – Azure Event Hubs | Microsoft Docs
description: Popisuje, jak vyrovnávat zatížení oddílů mezi několika instancemi aplikace pomocí procesoru událostí a sady Azure Event Hubs SDK.
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
ms.openlocfilehash: 1244fe64d0c23782fdae7a0f92415bada4bef55a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907653"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Vyrovnávání zatížení oddílu napříč několika instancemi vaší aplikace
Chcete-li škálovat aplikaci pro zpracování událostí, můžete spustit více instancí aplikace a vyrovnávat zatížení mezi sebou. Ve starších verzích vám [EventProcessorHost](event-hubs-event-processor-host.md) povolil vyrovnávat zatížení mezi několika instancemi programu a události kontrolního bodu při příjmu. V novějších verzích (5,0 a vyšší), **EventProcessorClient** (.NET a Java) nebo **EventHubConsumerClient** (Python a JavaScript) vám umožňuje provádět stejné. Vývojový model je jednodušší pomocí událostí. Přihlásíte se k odběru událostí, na které vás zajímáte, registrací obslužné rutiny události.

Tento článek popisuje vzorový scénář pro použití více instancí ke čtení událostí z centra událostí a pak vám poskytne podrobné informace o funkcích klienta procesoru událostí, který umožňuje přijímat události z více oddílů najednou a vyrovnávat zatížení s ostatními. spotřebitelé, kteří používají stejné centrum událostí a skupinu uživatelů.

> [!NOTE]
> Klíč, který chcete škálovat pro službu Event Hubs je dělené příjemců. Rozdíl od [konkurenčních spotřebitelů](https://msdn.microsoft.com/library/dn568101.aspx) vzor, vzor oddělených příjemců je pravidlo umožňuje vysoce škálované odebráním kritický bod kolize a usnadnění začátku do konce paralelismu.

## <a name="example-scenario"></a>Příklad scénáře

Jako ukázkový scénář vezměte v úvahu domácí zabezpečení společnosti, která monitoruje 100 000 nemovitostí. Každou minutu získává data z různých senzorů, jako je detektor pohybu, otevřený senzor dveří nebo oken, detektor skla a tak dále, nainstalovaný v každé domácnosti. Společnost poskytuje webovou stránku pro obyvatele monitorovat aktivitu jejich domovské téměř v reálném čase.

Každý ze senzorů odesílá data do centra událostí. Centrum událostí je nakonfigurované s 16 oddíly. Na náročném konci budete potřebovat mechanismus, který může číst tyto události, konsolidovat je (filtrovat, agregovat atd.) a vypsat agregovanou hodnotu do objektu BLOB úložiště, který je pak navržený na uživatelsky přívětivou webovou stránku.

## <a name="write-the-consumer-application"></a>Napíšeme aplikaci příjemce

Při navrhování příjemce v distribuovaném prostředí, tento scénář musí zpracovávat následující požadavky:

1. **Škálování:** vytvořit několik příjemců, s všichni příjemci převzetí vlastnictví čtení z několika oddílů služby Event Hubs.
2. **Vyrovnávání zatížení:** zvětšete nebo zmenšete dynamicky spotřebitele. Například při přidání nového typu senzor (například uhelnatého k oxidu detektor) na každý domovskou stránku, zvyšuje počet událostí. V takovém případě – operátor (lidské) zvýší počet instancí příjemce. Potom fond příjemců dokáží obnovit rovnováhu počet oddílů, které vlastní, sdílení zatížení se nově přidané zákazníky.
3. **Bezproblémové obnovení při selhání:** Pokud se uživatel (**spotřebitel a**) nepovede (například virtuální počítač, který hostuje nějakého uživatele, dojde k výpadku), ostatní spotřebitelé si můžou vybrat oddíly vlastněné **zákazníkem** a a pokračovat. Navíc pokračování bod, volá se *kontrolního bodu* nebo *posun*, by měla být v okamžiku, kdy **příjemce A** neúspěšné, nebo o něco dříve.
4. **Spotřebovávat události:** Zatímco předchozí tři body se týkají správy příjemce, musí existovat kód pro využívání událostí a co nejužitečnější je s ním. Například agreguje a nahrajte ho do úložiště objektů BLOB.

## <a name="event-processor-or-consumer-client"></a>Procesor událostí nebo klient příjemce

K splnění těchto požadavků nemusíte sestavovat vlastní řešení. Tuto funkci poskytují sady Azure Event Hubs SDK. V sadách .NET nebo Java SDK použijete klienta procesoru událostí (EventProcessorClient) a sady SDK skriptů Pythonu a Java, použijete EventHubConsumerClient. Ve staré verzi sady SDK se jednalo o hostitele procesoru událostí (EventProcessorHost), který tyto funkce podporuje.

Pro většinu produkčních scénářů doporučujeme použít klienta procesoru událostí pro čtení a zpracování událostí. Klient procesoru má k dispozici robustní prostředí pro zpracování událostí ve všech oddílech centra událostí v rámci výkonného a odolného přenosu, který poskytuje prostředky pro kontrolní body. Klienti procesorů událostí můžou spolupracují i v kontextu skupiny uživatelů pro dané centrum událostí. Klienti budou automaticky spravovat distribuci a vyrovnávání práce, protože instance budou k dispozici nebo nebudou pro skupinu k dispozici.

## <a name="partition-ownership-tracking"></a>Oddíl vlastnictví sledování

Instance procesoru událostí obvykle vlastní a zpracovává události z jednoho nebo více oddílů. Vlastnictví oddílů je rovnoměrně distribuované mezi všemi instancemi aktivních procesorů událostí přidružených k kombinaci centra událostí a skupiny uživatelů. 

Každému procesoru událostí se předává jedinečný identifikátor a vlastnictví deklarací oddílů přidáním nebo aktualizací položky v úložišti kontrolních bodů. Všechny instance procesoru událostí komunikují s tímto úložištěm pravidelně a aktualizují svůj vlastní stav zpracování i informace o dalších aktivních instancích. Tato data se pak použijí pro vyrovnání zatížení mezi aktivními procesory. Pro horizontální navýšení kapacity se můžou nové instance připojit ke fondu zpracování. Když instance nastanou mimo provoz, buď z důvodu selhání, nebo pro horizontální navýšení kapacity, vlastnictví oddílu se řádně přenáší na jiné aktivní procesory.

Záznamy o vlastnictví oddílu v úložišti kontrolního bodu uchovávají informace o Event Hubs obor názvů, název centra událostí, skupinu uživatelů, identifikátor procesoru událostí (označovaný také jako vlastník), ID oddílu a čas poslední změny.



| Obor názvů služby Event Hubs               | Název centra událostí | **Skupina uživatelů** | Vlastník                                | ID oddílu | Čas poslední změny  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1\. místo            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Každá instance procesoru událostí získá vlastnictví oddílu a spustí zpracování oddílu z posledního známého [kontrolního bodu](# Checkpointing). Pokud dojde k chybě procesoru (virtuální počítač se vypne), vyhledá jiné instance tím, že prohlíží čas poslední změny. Jiné instance se pokusí získat vlastnictví oddílů, které dříve vlastnila neaktivní instance, a úložiště kontrolního bodu zaručuje, že se při deklaracích vlastnictví oddílu úspěšně vytvoří jenom jedna z těchto instancí. Takže v jakémkoli časovém okamžiku existuje maximálně jeden procesor, který přijímá události z oddílu.

## <a name="receive-messages"></a>Příjem zpráv

Když vytváříte procesor událostí, zadáváte funkce, které budou zpracovávat události a chyby. Každé volání funkce, která zpracovává události, zajišťuje jednu událost z konkrétního oddílu. Tato událost se zpracovává vaší zodpovědností. Pokud se chcete ujistit, že příjemce zpracuje alespoň jednou zprávu, musíte napsat vlastní kód s logikou opakování. Buďte ale opatrní v souvislosti s poškozenými zprávami.

Doporučujeme, abyste provedli relativně rychle. To znamená co nejmenší zpracování. Pokud potřebujete zapisovat do úložiště a udělat si nějaké směrování, je lepší použít dvě skupiny uživatelů a mít dva procesory událostí.

## <a name="checkpointing"></a>Vytváření kontrolních bodů

*Kontrolní bod* je proces, při kterém procesor událostí označí nebo potvrdí pozici poslední úspěšné zpracovávané události v rámci oddílu. Označení kontrolního bodu se obvykle provádí ve funkci, která zpracovává události a probíhá na jednotlivých oddílech v rámci skupiny příjemců. 

Pokud se procesor událostí z oddílu odpojí, může jiná instance pokračovat ve zpracování oddílu v kontrolním bodu, který byl dřív potvrzen posledním procesorem tohoto oddílu v této skupině příjemců. Když se procesor připojí, předá posun do centra událostí, aby určil umístění, ve kterém se má začít číst. Tímto způsobem můžete pomocí kontrolního bodu provádět označení událostí jako "dokončeno" v aplikacích pro příjem dat a zajištění odolnosti při výpadku procesoru událostí. Ke starším datům se je možné vrátit tak, že určíte nižší posun od tohoto kontrolního bodu. 

Při provádění kontrolního bodu za účelem označení události jako zpracovaného je položka v úložišti kontrolního bodu přidána nebo aktualizována pomocí posunu události a čísla sekvence. Uživatelé by se měli rozhodnout frekvence aktualizace kontrolního bodu. Aktualizace po všech úspěšně zpracovaných událostech může mít dopad na výkon a náklady, protože aktivuje operaci zápisu do základního úložiště kontrolního bodu. Vytváření kontrolních bodů pro každou jednotlivou událost je také informativní. vzor zasílání zpráv ve frontě, pro který Service Bus frontu, může být lepší volbou než centrum událostí. Myšlenku za služby Event Hubs je, že dostanete "alespoň jednou" ve velmi velkém měřítku. Tím, že vaše podřízených systémů idempotentní, je snadné zotavení z chyb nebo restartuje výsledek ve stejné události, které přijímají více než jednou.

## <a name="thread-safety-and-processor-instances"></a>Vlákno bezpečnosti a procesor instance

Ve výchozím nastavení je procesor událostí nebo spotřebitel bezpečný pro přístup z více vláken a pracuje synchronním způsobem. Po doručení událostí pro oddíl se zavolá funkce, která zpracovává události. Následné zprávy a volání této funkce zařadí do fronty na pozadí, protože čerpadlo zpráv pokračuje v běhu na pozadí v jiných vláknech. Zabezpečení tohoto vlákna eliminuje potřebu kolekce bezpečné pro vlákna a výrazně zvyšuje výkon.

## <a name="next-steps"></a>Další kroky
Podívejte se na následující rychlé starty:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
