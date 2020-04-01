---
title: Co je služba Azure Event Hubs? - služba požití velkých dat | Dokumenty společnosti Microsoft
description: Přečtěte si o Azure Event Hubs, což je streamovací služba pro velké objemy dat, která může ingestovat miliony událostí za sekundu.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 45276ab59f1a3dabea42b904ff54bd37326fdeca
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398114"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs – platforma pro streamování velkých objemů dat a služba pro ingestování událostí
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba ingestování událostí. Dokáže přijímat a zpracovávat miliony událostí za sekundu. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání.

Následující scénáře jsou některé ze scénářů, kde můžete použít centra událostí:

- Detekce anomálií (podvody/odlehlé hodnoty)
- Protokolování aplikací
- Analytické kanály, jako například navštívené stránky
- Živé řídicí panely
- Archivace dat
- Zpracování transakcí
- Zpracování telemetrie uživatelů
- Streamování telemetrie zařízení

<iframe width="560" height="315" src="https://www.youtube.com/embed/45wgY-VSk9I" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## <a name="why-use-event-hubs"></a>Proč používat Event Hubs

Data jsou cenná tehdy, když se datové zdroje dají snadno zpracovat a dají se z nich získat včasné přehledy. Event Hubs poskytuje platformu pro zpracování distribuovaného datového proudu s nízkou latencí a bezproblémovou integrací s datovými a analytickými službami uvnitř i vně Azure, které vytvářejí kompletní kanál pro velké objemy dat.

Služba Event Hubs představuje „vstupní dveře“ pro kanál událostí, který se v architekturách řešení často nazývá *ingestor událostí*. Přijímač událostí je komponenta nebo služba, která se nachází mezi zdroji událostí a příjemci událostí a slouží k oddělení produkce datového proudu událostí od spotřeby těchto událostí. Event Hubs poskytuje jednotnou platformu pro streamování s vyrovnávací pamětí pro uchovávání času, která odděluje výrobce událostí od spotřebitelů událostí.

Následující části popisují klíčové funkce služby Azure Event Hubs:

## <a name="fully-managed-paas"></a>PaaS (platforma jako služba) s plnou správou

Event Hubs je plně spravovaná platforma jako služba (PaaS) s malou režií na konfiguraci nebo správu, takže se můžete soustředit na svá obchodní řešení. [Služba Event Hubs pro ekosystémy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) vám umožňuje využívat možnosti PaaS Kafka bez nutnosti spravovat, konfigurovat nebo mít spuštěné vaše clustery.

## <a name="support-for-real-time-and-batch-processing"></a>Podpora zpracování v reálném čase a dávkového zpracování

Ingestování, ukládání do vyrovnávací paměti, dlouhodobé ukládání a zpracování streamu je možné v reálném čase, takže můžete využívat užitečné přehledy hned. Event Hubs používá [model příjemce s partitioned](event-hubs-scalability.md#partitions), který umožňuje více aplikacím souběžně zpracovávat datový proud a umožňuje řídit rychlost zpracování.

[Zachyťte](event-hubs-capture-overview.md) svá data téměř v reálném čase v [úložišti Azure Blob](https://azure.microsoft.com/services/storage/blobs/) nebo [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/) pro dlouhodobé uchovávání nebo mikrodávkové zpracování. Toto chování můžete dosáhnout ve stejném datovém proudu, který používáte pro odvození analýzy v reálném čase. Nastavení sběru dat událostí je rychlé. Neexistují žádné administrativní náklady na jeho spuštění a automaticky se škáluje s [jednotkami propustnosti](event-hubs-scalability.md#throughput-units)centra událostí . Centra událostí umožňuje zaměřit se na zpracování dat spíše než na sběr dat.

Azure Event Hubs se také dá integrovat s [Azure Functions](/azure/azure-functions/) jako architektura bez serveru.

## <a name="scalable"></a>Škálovatelné

Pomocí Event Hubs můžete začít se streamy v megabajtech a přejít na gigabajty nebo terabajty. Funkce [Automatické rozšiřování](event-hubs-auto-inflate.md) je jednou z mnoha dostupných možností škálování počtu jednotek propustnosti, aby to vyhovovalo vašim potřebám.

## <a name="rich-ecosystem"></a>Ekosystém plný možností

[Event Huby pro ekosystémy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) umožňují klientům [apache kafka (1.0 a novější)](https://kafka.apache.org/) a aplikacím mluvit s Event Hubs. Není nutné nastavovat, konfigurovat a spravovat vlastní clustery Kafka.

Díky širokému ekosystému dostupnému v různých jazycích [.NET](https://github.com/Azure/azure-sdk-for-net/), [Java](https://github.com/Azure/azure-sdk-for-java/), [Pythonu](https://github.com/Azure/azure-sdk-for-python/), [JavaScriptu](https://github.com/Azure/azure-sdk-for-js/)můžete snadno začít zpracovávat streamy z Event Hubů. Všechny podporované jazyky klientů poskytují integraci na nízké úrovni. Ekosystém vám také poskytuje bezproblémovou integraci se službami Azure, jako jsou Azure Stream Analytics a Azure Functions, a umožňuje tak vytvářet architektury bez serveru.

## <a name="key-architecture-components"></a>Klíčové komponenty architektury
Event Hubs obsahuje následující [klíčové komponenty](event-hubs-features.md):

- **Poskytovatelé událostí**: Entita, která posílá data do centra událostí. Vydavatelé událostí můžou publikovat události pomocí protokolu HTTPS nebo AMQP 1.0 nebo Apache Kafka (1.0 nebo vyšší).
- **Oddíly**: Každý příjemce čte jenom konkrétní podmnožinu, neboli oddíl, streamu zprávy.
- **Skupiny příjemců**: Zobrazení (stav, pozice nebo posun) celého centra událostí. Skupiny spotřebitelů umožňují náročné aplikace pro každý mají samostatné zobrazení datového proudu událostí. Stream čtou nezávisle vlastním tempem a s vlastními kompenzacemi.
- **Jednotky propustnosti**: Předem koupené jednotky kapacity, které řídí kapacitu propustnosti služby Event Hubs.
- **Příjemci událostí**: Entita, která čte data událostí z centra událostí. Všichni spotřebitelé event hubů se připojují prostřednictvím relace AMQP 1.0. Služba Event Hubs poskytuje události prostřednictvím relace, jakmile budou k dispozici. Všichni příjemci Kafka se připojují přes protokol Kafka 1.0 nebo novější.

Následující obrázek znázorňuje architekturu zpracování datového proudu Event Hubs:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)

## <a name="event-hubs-on-azure-stack-hub"></a>Centra událostí v centru Azure Stack Hub
Centra událostí v centru Azure Stack Hub umožňují realizovat scénáře hybridního cloudu. Streamovací a eventová řešení jsou podporovaná pro místní i cloudové zpracování Azure. Bez ohledu na to, zda je váš scénář hybridní (připojený) nebo odpojený, vaše řešení může podporovat zpracování událostí a datových proudů ve velkém měřítku. Váš scénář je vázán pouze velikost clusteru Event Hubs, které můžete zřídit podle vašich potřeb. 

Edice Event Hubs (v Azure Stack Hubu a v Azure) nabízejí vysoký stupeň parity funkcí. Tato parita znamená, že sady SDK, ukázky, prostředí PowerShell, CLI a portály nabízejí podobné prostředí s několika rozdíly. 

Centra událostí v zásobníku jsou během náhledu public zdarma. Další informace najdete v [tématu Centra událostí na Azure Stack Hub přehled](https://docs.microsoft.com/azure-stack/user/event-hubs-overview).


## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat Centra událostí, přečtěte si kurzy **pro odesílání a přijímání událostí:**

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Přejít](event-hubs-go-get-started-send.md)
- [C (jenom odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (jenom příjem)](event-hubs-storm-getstarted-receive.md)


Další informace o službě Event Hubs najdete v následujících článcích:

- [Přehled funkcí Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy](event-hubs-faq.md).


