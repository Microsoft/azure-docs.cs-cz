---
title: Co je služba Azure Event Hubs? – Služba pro příjem velkých objemů dat | Microsoft Docs
description: Přečtěte si o Azure Event Hubs, což je streamovací služba pro velké objemy dat, která může ingestovat miliony událostí za sekundu.
ms.topic: overview
ms.date: 01/13/2021
ms.openlocfilehash: 36eeb38d9ed1696c9524ae9b346065756ce49c46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98195754"
---
# <a name="azure-event-hubs--a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs – platforma pro zpracování velkých objemů dat a služba pro příjem událostí
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro příjem událostí. Dokáže přijímat a zpracovávat miliony událostí za sekundu. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání.

Následující scénáře jsou některé z scénářů, ve kterých můžete použít Event Hubs:

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

Data jsou cenná tehdy, když se datové zdroje dají snadno zpracovat a dají se z nich získat včasné přehledy. Event Hubs poskytuje platformu pro zpracování distribuovaných datových proudů s nízkou latencí a bezproblémovou integrací s datovými a analytickými službami uvnitř a mimo Azure za účelem vytvoření kompletního kanálu s velkými objemy dat.

Služba Event Hubs představuje „vstupní dveře“ pro kanál událostí, který se v architekturách řešení často nazývá *ingestor událostí*. Přijímač událostí je komponenta nebo služba, která se nachází mezi zdroji událostí a příjemci událostí a slouží k oddělení produkce datového proudu událostí od spotřeby těchto událostí. Event Hubs poskytuje jednotnou platformu pro streamování s vyrovnávací pamětí pro uchovávání času, odpojuje výrobce událostí od uživatelů událostí.

Následující části popisují klíčové funkce služby Azure Event Hubs:

## <a name="fully-managed-paas"></a>PaaS (platforma jako služba) s plnou správou

Event Hubs je plně spravovaná platforma jako služba (PaaS) s nízkou režií v oblasti konfigurace nebo správy, takže se můžete soustředit na obchodní řešení. [Služba Event Hubs pro ekosystémy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) vám umožňuje využívat možnosti PaaS Kafka bez nutnosti spravovat, konfigurovat nebo mít spuštěné vaše clustery.

## <a name="support-for-real-time-and-batch-processing"></a>Podpora zpracování v reálném čase a dávkového zpracování

Ingestování, ukládání do vyrovnávací paměti, dlouhodobé ukládání a zpracování streamu je možné v reálném čase, takže můžete využívat užitečné přehledy hned. Event Hubs používá model koncového [uživatele](event-hubs-scalability.md#partitions), který umožňuje více aplikacím souběžně zpracovávat proud a umožnit vám řídit rychlost zpracování.

Data můžete [zachytit](event-hubs-capture-overview.md) prakticky v reálném čase v [úložišti objektů BLOB v Azure](https://azure.microsoft.com/services/storage/blobs/) nebo [Azure Data Lake Storage](https://azure.microsoft.com/services/data-lake-store/)   pro dlouhodobé uchovávání nebo zpracování mikrodávkování. Toto chování můžete dosáhnout na stejném datovém proudu, který používáte pro odvození analýz v reálném čase. Nastavení zachycení dat událostí je rychlé. Neexistují žádné náklady na správu, které by bylo možné spustit, a automaticky se škálují Event Hubs [jednotky propustnosti](event-hubs-scalability.md#throughput-units). Event Hubs vám umožní soustředit se na zpracování dat, nikoli na shromažďování dat.

Azure Event Hubs se také dá integrovat s [Azure Functions](../azure-functions/index.yml) jako architektura bez serveru.

## <a name="scalable"></a>Škálovatelné

Pomocí Event Hubs můžete začít se streamy v megabajtech a přejít na gigabajty nebo terabajty. Funkce [Automatické rozšiřování](event-hubs-auto-inflate.md) je jednou z mnoha dostupných možností škálování počtu jednotek propustnosti, aby to vyhovovalo vašim potřebám.

## <a name="rich-ecosystem"></a>Ekosystém plný možností

Díky širokému ekosystému založenému na standardním protokolu AMQP 1,0 a k dispozici v různých jazycích [.NET](https://github.com/Azure/azure-sdk-for-net/), [Java](https://github.com/Azure/azure-sdk-for-java/), [Python](https://github.com/Azure/azure-sdk-for-python/), [JavaScript](https://github.com/Azure/azure-sdk-for-js/)můžete snadno začít zpracovávat datové proudy z Event Hubs. Všechny podporované jazyky klientů poskytují integraci na nízké úrovni. Ekosystém taky poskytuje bezproblémovou integraci se službami Azure, jako jsou Azure Stream Analytics a Azure Functions, takže umožňuje vytvářet architektury bez serveru.

[Event Hubs pro Apache Kafka ekosystémy](event-hubs-for-kafka-ecosystem-overview.md) navíc umožní klientům a aplikacím [Apache Kafka (1,0 a novějším)](https://kafka.apache.org/) komunikovat s Event Hubs. Nemusíte nastavovat, konfigurovat a spravovat vlastní clustery Kafka a Zookeeper ani používat některé nabídky Kafka jako služby, které nejsou nativní pro Azure.
## <a name="key-architecture-components"></a>Klíčové komponenty architektury
Event Hubs obsahuje následující [klíčové komponenty](event-hubs-features.md):

- **Poskytovatelé událostí**: Entita, která posílá data do centra událostí. Vydavatelé událostí můžou publikovat události pomocí protokolu HTTPS nebo AMQP 1.0 nebo Apache Kafka (1.0 nebo vyšší).
- **Oddíly**: Každý příjemce čte jenom konkrétní podmnožinu, neboli oddíl, streamu zprávy.
- **Skupiny příjemců**: Zobrazení (stav, pozice nebo posun) celého centra událostí. Skupiny uživatelů umožňují využívání aplikací pro každý z nich samostatné zobrazení datového proudu událostí. Čtou Stream nezávisle na vlastním tempu a s jejich vlastními posuny.
- **Jednotky propustnosti**: Předem koupené jednotky kapacity, které řídí kapacitu propustnosti služby Event Hubs.
- **Příjemci událostí**: Entita, která čte data událostí z centra událostí. Všichni Event Hubs spotřebitelé se připojují prostřednictvím relace AMQP 1,0. Služba Event Hubs doručuje události prostřednictvím relace, jakmile budou k dispozici. Všichni příjemci Kafka se připojují přes protokol Kafka 1.0 nebo novější.

Následující obrázek znázorňuje architekturu zpracování datového proudu Event Hubs:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.svg)

## <a name="event-hubs-on-azure-stack-hub"></a>Event Hubs v Azure Stack Hubu
Event Hubs v centru Azure Stack vám umožní realizovat hybridní cloudové scénáře. Podpora streamování a řešení založeného na událostech je podporovaná pro místní i cloudové zpracování Azure. Bez ohledu na to, jestli je váš scénář hybridní (připojený) nebo odpojený, může vaše řešení podporovat zpracování událostí nebo datových proudů ve velkém měřítku. Váš scénář je vázaný jenom na velikost clusteru Event Hubs, kterou můžete zřídit podle svých potřeb. 

Edice Event Hubs (v centru Azure Stack a v Azure) nabízejí vysoký stupeň parity funkcí. Tato parita znamená, že sady SDK, ukázky, PowerShellu, rozhraní příkazového řádku a portálů nabízejí podobné prostředí s několika rozdíly. 

Event Hubs v zásobníku jsou v rámci verze Public Preview zdarma. Další informace najdete v tématu [Event Hubs v článku Přehled centra Azure Stack](/azure-stack/user/event-hubs-overview).


## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat Event Hubs, přečtěte si kurzy pro **události odeslání a příjem** :

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Přejít](event-hubs-go-get-started-send.md)
- [C (jenom odesílání)](event-hubs-c-getstarted-send.md)
- [Apache Storm (jenom příjem)](event-hubs-storm-getstarted-receive.md)


Další informace o službě Event Hubs najdete v následujících článcích:

- [Přehled funkcí Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy](event-hubs-faq.md)
