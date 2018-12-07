---
title: Co je služba Azure Event Hubs? | Dokumenty Microsoft
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
ms.openlocfilehash: d7f2bd5487a5eab248f08b408341470dcaf04a93
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017283"
---
# <a name="azure-event-hubs---a-big-data-streaming-platform-and-event-ingestion-service"></a>Azure Event Hubs – velké objemy dat, platformy a události služba pro ingestování datových proudů

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. 

Služba Event Hubs se používá v některých následujících běžných situacích:

- Detekce anomálií (podvody/odlehlé hodnoty)
- Protokolování aplikace
- Analytické kanály, jako například navštívené stránky
- Živé řídicí panely
- Archivace dat
- Zpracování transakcí
- Zpracování telemetrie uživatelů
- Streamování telemetrie zařízení 

## <a name="why-use-event-hubs"></a>Proč používat Event Hubs

Data jsou cenná tehdy, když se datové zdroje dají snadno zpracovat a dají se z nich získat včasné přehledy. Služba Event Hubs poskytuje distribuovanou platformu pro zpracování streamů s nízkou latencí a bezproblémovou integrací a obsahuje datové a analytické služby uvnitř i mimo Azure, které dohromady tvoří kanál pro velké objemy dat.

Služba Event Hubs představuje „vstupní dveře“ pro kanál událostí, který se v architekturách řešení často nazývá *ingestor událostí*. Přijímač událostí je komponenta nebo služba, která se nachází mezi zdroji událostí a příjemci událostí a slouží k oddělení produkce datového proudu událostí od spotřeby těchto událostí. Služba Event Hubs poskytuje jednotnou streamovací platformu s vyrovnávací pamětí pro uchovávání v čase, což umožňuje oddělit poskytovatele událostí od příjemců událostí. 

Následující části popisují klíčové funkce služby Azure Event Hubs: 

## <a name="fully-managed-paas"></a>PaaS (platforma jako služba) s plnou správou 

Event Hubs je spravovaná služba s minimální režií na konfiguraci nebo správu, takže se soustředíte na vaše obchodní řešení. [Služba Event Hubs pro ekosystémy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) vám umožňuje využívat možnosti PaaS Kafka bez nutnosti spravovat, konfigurovat nebo mít spuštěné vaše clustery.

## <a name="support-for-real-time-and-batch-processing"></a>Podpora zpracování v reálném čase a dávkového zpracování

Ingestování, ukládání do vyrovnávací paměti, dlouhodobé ukládání a zpracování streamu je možné v reálném čase, takže můžete využívat užitečné přehledy hned. Služba Event Hubs používá [model oddělených příjemců](event-hubs-features.md#partitions), což umožňuje, aby stream zpracovávalo současně více aplikací a vy jste mohli řídit rychlost zpracování.

[Zachycujte](event-hubs-capture-overview.md) data pomocí funkce Capture téměř v reálném čase v úložištích [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) nebo [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) , kde můžou být dlouhodobě nebo kde se můžou zpracovávat po mikrodávkách. Tyto možnosti jsou dostupné pro stejný stream, který používáte pro odvozování analýzy v reálném čase. Nastavení funkce Capture je rychlé, se správou jejího běhu nejsou spojené žádné náklady a automaticky se škáluje pomocí  [jednotek propustnosti](event-hubs-features.md#throughput-units) služby Event Hubs. Event Hubs Capture vám umožňuje, abyste se místo na zachytávání dat soustředili na jejich zpracování.

Azure Event Hubs se také dá integrovat s [Azure Functions](/azure/azure-functions/) jako architektura bez serveru.

## <a name="scalable"></a>Škálovatelné 

Pomocí Event Hubs můžete začít se streamy v megabajtech a přejít na gigabajty nebo terabajty. Funkce [Automatické rozšiřování](event-hubs-auto-inflate.md) je jednou z mnoha dostupných možností škálování počtu jednotek propustnosti, aby to vyhovovalo vašim potřebám. 

## <a name="rich-ecosystem"></a>Ekosystém plný možností

[Služba Event Hubs pro ekosystémy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) umožňuje klientům a aplikacím [Apache Kafka (1.0 nebo novější)](https://kafka.apache.org/) komunikovat se službou Event Hubs a nemusí se přitom spravovat žádné clustery.
 
Díky širokému ekosystému dostupnému v různých [jazycích (.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs) můžete snadno začít zpracovávat streamy ze služby Event Hubs. Všechny podporované jazyky klientů poskytují integraci na nízké úrovni. Tento ekosystém poskytuje také bezproblémovou integraci se službami Azure, jako je Stream Analytics a Azure Functions, která umožňuje vytvářet bezserverové architektury.

## <a name="key-architecture-components"></a>Klíčové komponenty architektury

Služba Event Hubs poskytuje možnost zpracovávat datové proudy zpráv. Její charakteristiky ji ale odlišují od tradičních podnikových způsobů zasílání zpráv. Možnosti služby Event Hubs jsou vycházejí ze scénářů zpracování událostí a vysoké propustnosti. Event Hubs obsahuje následující [klíčové komponenty](event-hubs-features.md):

- **Poskytovatelé událostí**: Entita, která posílá data do centra událostí. Vydavatelé událostí můžou publikovat události pomocí protokolu HTTPS nebo AMQP 1.0 nebo Apache Kafka (1.0 nebo vyšší).
- **Oddíly**: Každý příjemce čte jenom konkrétní podmnožinu, neboli oddíl, streamu zprávy.
- **Skupiny příjemců**: Zobrazení (stav, pozice nebo posun) celého centra událostí. Skupiny příjemců poskytují různým přijímajícím aplikacím oddělená zobrazení datového proudu událostí a umožňují jim nezávisle číst datový proud vlastním tempem a s použitím vlastních posunů.
- **Jednotky propustnosti**: Předem koupené jednotky kapacity, které řídí kapacitu propustnosti služby Event Hubs.
- **Příjemci událostí**: Entita, která čte data událostí z centra událostí. Všichni příjemci se ve službě Event Hubs připojují pomocí relace protokolu AMQP 1.0 a události se doručují tak, jak jsou postupně dostupné. Všichni příjemci Kafka se připojují přes protokol Kafka 1.0 nebo novější.

Následující obrázek znázorňuje architekturu zpracování datového proudu Event Hubs:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Další postup

Pokud chcete začít používat službu Event Hubs, podívejte se na následující články:

1. **Vytvoření centra událostí:** [Azure Portal](event-hubs-create.md), [Azure CLI](event-hubs-quickstart-cli.md), [Azure PowerShell](event-hubs-quickstart-powershell.md), [Šablona Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub.md)
2. **Odesílání událostí do centra událostí**: [.NET Core](event-hubs-dotnet-standard-getstarted-send.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-send.md), [Java](event-hubs-java-get-started-send.md), [Python](event-hubs-python-get-started-send.md), [Node.js](event-hubs-node-get-started-send.md), [Přejít](event-hubs-go-get-started-send.md), [C](event-hubs-c-getstarted-send.md)
3. **Příjem událostí z centra událostí**: [.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md), [rozhraní .NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md), [Java](event-hubs-java-get-started-receive-eph.md), [Python](event-hubs-python-get-started-receive.md), [Node.js ](event-hubs-node-get-started-receive.md), [Přejít](event-hubs-go-get-started-receive-eph.md), [Apache Storm](event-hubs-storm-getstarted-receive.md)   

Další informace o službě Event Hubs najdete v následujících článcích:

- [Přehled funkcí Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy](event-hubs-faq.md)


