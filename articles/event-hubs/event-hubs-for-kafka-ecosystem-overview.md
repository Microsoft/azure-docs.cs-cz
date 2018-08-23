---
title: Azure Event Hubs pro Apache Kafka | Dokumentace Microsoftu
description: Přehled a úvod do Kafka povolena služba Azure Event Hubs
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 08/16/2018
ms.author: bahariri
ms.openlocfilehash: 16c101068be48ba1435ef230b29c679fcef17d08
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42060553"
---
# <a name="azure-event-hubs-for-apache-kafka-preview"></a>Azure Event Hubs pro Apache Kafka (preview)

Služba Event Hubs poskytuje Kafka koncového bodu, který lze použít ve stávajících Kafka aplikací založených na jako alternativu ke spuštění clusteru Kafka. Podporuje Služba Event Hubs [Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) a novější verze klienta a spolupracuje s vašimi stávajícími aplikacemi Kafka, včetně nástroje MirrorMaker. 

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co Event Hubs pro systém Kafka poskytuje?

Služba Event Hubs pro funkci Kafka poskytuje head protokolu nad Azure Event Hubs, která je binární kompatibilní s Kafka verze 1.0 a novějším pro čtení i zápis témat Kafka. Můžete začít pomocí koncového bodu Kafka z vašich aplikací se žádné změny kódu, ale minimální konfiguraci změnit. Můžete aktualizovat připojovací řetězec v konfiguracích tak, aby odkazoval na koncový bod Kafka vystavené vašeho centra událostí místo směřující do vašeho clusteru Kafka. Pak můžete začít, streamování událostí z aplikací, které používají protokol Kafka do služby Event Hubs. 

Koncepčně Kafka a Event Hubs jsou téměř identické: jsou obě dělené protokoly vytvořené pro streamovaná data. Následující tabulka mapuje koncepty mezi Kafka a Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka a Centrum událostí koncepční mapování

| Koncept Kafka | Event Hubs koncept|
| --- | --- |
| Cluster | Obor názvů |
| Téma | Event Hubs |
| Oddíl | Oddíl|
| Skupina uživatelů | Skupina uživatelů |
| Posun | Posun|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Hlavní rozdíly mezi Kafka a Event Hubs

Zatímco [Apache Kafka](https://kafka.apache.org/) je software, který můžete spustit bez ohledu na to zvolíte, Event Hubs je Cloudová služba, která je podobná do Azure Blob Storage. Neexistují žádné servery nebo sítě pro správu a bez zprostředkovatele konfigurace. Vytvoření oboru názvů, což je plně kvalifikovaný název domény, ve kterém témat pracovat, a pak vytvořte službu Event Hubs nebo témat v daném oboru názvů. Další informace o Event Hubs a obory názvů, naleznete v tématu [funkcích služby Event Hubs](event-hubs-features.md#namespace). Jako cloudová služba a Služba Event Hubs využívá jednu stabilní virtuální IP adresu jako koncový bod, tak klienty není potřeba vědět o zprostředkovatele nebo počítače v rámci clusteru. 

Škálování ve službě Event Hubs je řízen prostřednictvím kolik jednotek propustnosti nákupu, s každou jednotkou propustnosti opravňující je 1 MB za sekundu nebo 1000 událostí za sekundu z příchozího přenosu. Ve výchozím nastavení, služby Event Hubs škálování jednotek propustnosti při dosažení limitu se [automatické rozšiřování](event-hubs-auto-inflate.md) funkce; tato funkce také pomocí služby Event Hubs pro funkci Kafka. 

### <a name="security-and-authentication"></a>Zabezpečení a ověřování

Azure Event Hubs vyžaduje SSL nebo TLS pro veškerou komunikaci a používá sdílených přístupových podpisů (SAS) pro ověřování. Tento požadavek platí taky pro koncový bod Kafka v rámci služby Event Hubs. Z důvodu kompatibility s využitím Kafka Služba Event Hubs využívá SASL prostý pro ověřování a SASL protokolu SSL pro zabezpečení přenosu. Další informace o zabezpečení ve službě Event Hubs najdete v tématu [ověřování služby Event Hubs a zabezpečení](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Další funkce Event Hubs k dispozici pro systém Kafka

Event Hubs pro funkci Kafka umožňuje zápis s jeden protokol a čtení sebou, tak, aby vaše aktuální Kafka výrobci můžete pokračovat v publikování prostřednictvím Kafka, a přidáte čtenáře s využitím služby Event Hubs, jako je Azure Stream Analytics nebo Azure Functions. Kromě toho Služba Event Hubs funkce, jako [zachycení](event-hubs-capture-overview.md) a [geografického zotavení po havárii](event-hubs-geo-dr.md) spolupracovat také se službami Event Hubs pro funkci Kafka.

## <a name="features-that-are-not-supported-in-the-preview"></a>Funkce, které nejsou podporované ve verzi preview

Verze public Preview služby Event Hubs pro integraci Kafka nejsou podporovány následující funkce Kafka:

*   Výrobce Idempotentní
*   Transakce
*   Komprese
*   Uchovávání informací podle velikosti
*   Komprese protokolu
*   Přidání oddílů do stávajícího tématu
*   Podpora HTTP Kafka API
*   Kafka připojení
*   Rozhraní Kafka Streams

## <a name="next-steps"></a>Další postup

Tento článek poskytuje úvod do služby Event Hubs pro systém Kafka. Další informace najdete v následujících tématech:

* [Jak vytvořit Kafka povolena Služba Event Hubs](event-hubs-create-kafka-enabled.md)
* [Stream do služby Event Hubs ze svých aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
* Úvodní [Kurz služby Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

 
 

