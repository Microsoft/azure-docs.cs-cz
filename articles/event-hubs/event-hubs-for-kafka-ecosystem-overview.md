---
title: Použití centra událostí z aplikace Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o podpoře Apache Kafka službou Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d3271d6e8cb7d423e1dccd235b244688e7ab5683
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555792"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Použití Azure Event Hubs z Apache Kafkach aplikací
Event Hubs poskytuje koncový bod Kafka, který můžou stávající aplikace založené na Kafka použít jako alternativu ke spuštění vlastního clusteru Kafka. Event Hubs podporuje [Apache Kafka protokol 1,0 a novější](https://kafka.apache.org/documentation/)a funguje s vašimi stávajícími aplikacemi Kafka, včetně nástroje MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co Event Hubs for Kafka poskytnout?

Funkce Event Hubs for Kafka poskytuje hlavní hlavičku protokolu pro Azure Event Hubs, která je binární kompatibilní s Kafka verzemi 1,0 a novějšími pro čtení i zápis do Kafka témat. Můžete začít používat Kafka koncový bod z vašich aplikací bez změny kódu, ale minimální změna konfigurace. Připojovací řetězec v konfiguracích aktualizujete tak, aby odkazoval na koncový bod Kafka vystavený centrem událostí, a nemusíte ukazovat na svůj cluster Kafka. Pak můžete spustit streamování událostí z aplikací, které používají protokol Kafka, do Event Hubs. Tato integrace také podporuje architektury, jako je [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), která je aktuálně ve verzi Preview. 

Koncepční Kafka a Event Hubs jsou téměř identické: Jedná se o segmentované protokoly sestavené pro streamovaná data. Následující tabulka mapuje koncepty mezi Kafka a Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka a konceptuální mapování centra událostí

| Koncept Kafka | Event Hubs koncept|
| --- | --- |
| Cluster | hosting |
| Téma | Centrum událostí |
| Oddíl | Oddíl|
| Skupina uživatelů | Skupina uživatelů |
| Posun | Posun|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Klíčové rozdíly mezi Kafka a Event Hubs

I když [Apache Kafka](https://kafka.apache.org/) je software, který můžete spustit všude, kde si zvolíte, Event Hubs je cloudová služba, která se podobá službě Azure Blob Storage. Neexistují žádné servery nebo sítě, které by bylo možné spravovat, a žádné zprostředkovatele pro konfiguraci. Vytvoříte obor názvů, který je plně kvalifikovaný název domény, ve kterém jsou témata živá, a pak vytvoříte Event Hubs nebo témata v rámci tohoto oboru názvů. Další informace o Event Hubs a oborech názvů najdete v tématu [Event Hubs funkce](event-hubs-features.md#namespace). Jako cloudová služba Event Hubs používá jedinou stabilní virtuální IP adresu jako koncový bod, takže klienti nepotřebují informace o vzdálení nebo počítačích v clusteru. 

Škála v Event Hubs se řídí počtem jednotek propustnosti, které zakoupíte, s každou jednotkou propustnosti entitling na 1 MB za sekundu nebo za 1000 událostí za sekundu příchozího přenosu dat. Ve výchozím nastavení Event Hubs škáluje jednotky propustnosti, když dosáhnete svého limitu pomocí funkce [automatického rozplochení](event-hubs-auto-inflate.md) ; Tato funkce funguje taky s funkcí Event Hubs pro Kafka. 

### <a name="security-and-authentication"></a>Zabezpečení a ověřování

Azure Event Hubs vyžaduje pro účely ověřování protokol SSL nebo TLS a pro ověřování používá sdílené přístupové podpisy (SAS). Tento požadavek platí také pro koncový bod Kafka v rámci Event Hubs. Pro zajištění kompatibility s Kafka používá Event Hubs k ověřování a SASL SSL pro zabezpečení přenosu SASL PLAIN. Další informace o zabezpečení v Event Hubs najdete v tématu [ověřování a zabezpečení Event Hubs](event-hubs-authentication-and-security-model-overview.md).

## <a name="other-event-hubs-features-available-for-kafka"></a>Další funkce Event Hubs dostupné pro Kafka

Funkce Event Hubs for Kafka umožňuje psát pomocí jednoho protokolu a číst s jiným, takže stávající výrobci Kafka mohou pokračovat v publikování prostřednictvím Kafka a můžete přidat čtenáře s Event Hubs, jako je například Azure Stream Analytics nebo Azure Functions. Kromě toho Event Hubs funkce, jako je například [zachycení](event-hubs-capture-overview.md) a [geografická havárie – obnovení](event-hubs-geo-dr.md) , fungují také s funkcí Event Hubs pro Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkce, které ještě nejsou podporované 

Tady je seznam funkcí Kafka, které ještě nejsou podporované:

*   Idempotentní producent
*   transakce
*   Komprese
*   Uchovávání na základě velikosti
*   Komprimace protokolů
*   Přidávání oddílů do existujícího tématu
*   Podpora rozhraní HTTP Kafka API
*   Datové proudy Kafka

## <a name="next-steps"></a>Další kroky

Tento článek poskytuje Úvod do Event Hubs pro Kafka. Další informace najdete na následujících odkazech:

- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create-kafka-enabled.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí s podporou Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí s podporou Kafka](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí s podporou Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrace připojení Kafka s centrem událostí s podporou Kafka](event-hubs-kafka-connect-tutorial.md)
- [Připojení Akka Streams k centru událostí s podporou Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)


