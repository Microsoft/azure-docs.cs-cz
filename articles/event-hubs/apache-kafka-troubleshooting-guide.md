---
title: Řešení problémů s Azure Event Hubs pro Apache Kafka
description: Tento článek ukazuje, jak řešit problémy s Azure Event Hubs pro Apache Kafka
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/01/2020
ms.author: shvija
ms.openlocfilehash: 12ddc5fa74b7a1b42bbd64fde9ec3410b1c1e425
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606727"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Průvodce odstraňováním potíží apache Kafka pro centra událostí
Tento článek obsahuje tipy pro řešení potíží, které můžete narazit při použití event hubů pro Apache Kafka. 

## <a name="server-busy-exception"></a>Výjimka Zaneprázdněn serveru
Může se zobrazit výjimka Zaneprázdněn serveru z důvodu omezení Kafka. U klientů AMQP centra událostí okamžitě vrátí výjimku **Zaneprázdněn serveru** při omezení služby. Je to ekvivalentní "zkuste to znovu později" zprávy. V Kafce jsou zprávy zpožděny před dokončením. Délka zpoždění je vrácena v `throttle_time_ms` milisekundách jako v odpovědi na načtení. Ve většině případů tyto zpožděné požadavky nejsou zaznamenány jako výjimky ServerBusy na řídicích panelech centra událostí. Místo toho by `throttle_time_ms` měla být použita hodnota odpovědi jako indikátor, že propustnost překročila zřízenou kvótu.

Pokud je přenos nadměrný, služba má následující chování:

- Pokud zpoždění požadavku překročí časový limit požadavku, vrátí centra událostí kód chyby **porušení zásad.**
- Pokud zpoždění požadavku na načtení překročí časový limit požadavku, centra událostí zaznamená požadavek jako omezené a odpoví prázdnou sadou záznamů a bez kódu chyby.

[Vyhrazené clustery](event-hubs-dedicated-overview.md) nemají mechanismy omezení. Můžete spotřebovat všechny prostředky clusteru.

## <a name="no-records-received"></a>Nebyly přijaty žádné záznamy.
Můžete vidět, že spotřebitelé nedostávají žádné záznamy a neustále vyvažují. V tomto scénáři spotřebitelé nedostanou žádné záznamy a neustále znovu vyvážit. Neexistuje žádná výjimka nebo chyba, když se to stane, ale protokoly Kafka se zobrazí, že spotřebitelé jsou přilepená snaží znovu připojit ke skupině a přiřadit oddíly. Existuje několik možných příčin:

- Ujistěte se, že vaše `request.timeout.ms` je alespoň doporučená hodnota `session.timeout.ms` 60000 a vaše je alespoň doporučená hodnota 30000. S tato nastavení příliš nízká může způsobit časových časových opovenek spotřebitele, které pak způsobit rebalances (které pak způsobí více časové toto časové období, které způsobují větší vyvážení, a tak dále) 
- Pokud vaše konfigurace odpovídá těmto doporučeným hodnotám a stále vidíte neustálé vyvažování, neváhejte a otevřete problém (nezapomeňte do problému zahrnout celou konfiguraci, abychom mohli pomoci s laděním)!

## <a name="compressionmessage-format-version-issue"></a>Problém s verzí komprese/formátu zprávy
Kafka podporuje kompresi a Event Hubs pro Kafka v současné době není. Chyby, které zmiňují `The message format version on the broker does not support the request.`verzi ve formátu zprávy (například) jsou způsobeny, když se klient pokusí odeslat komprimované zprávy Kafka našim zprostředkovatelům.

Pokud je nutno komprimovaná data, komprese dat před jejich odesláním zprostředkovatelům a dekomprese po přijetí je platné řešení. Tělo zprávy je pouze bajtové pole ke službě, takže komprese/dekomprese na straně klienta nezpůsobí žádné problémy.

## <a name="unknownserverexception"></a>Výjimka neznámého serveru
Může se zobrazit výjimka UnknownServerException z klientských knihoven Kafka podobná následujícímu příkladu: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Otevřete lístek s podporou Microsoftu.  Protokolování na úrovni ladění a časová razítka výjimek v utc jsou užitečné při ladění problému. 

## <a name="other-issues"></a>Další problémy
Pokud se při používání Kafky v centru událostí zobrazují následující položky, zkontrolujte následující položky.

- **Brána firewall blokuje provoz** – Ujistěte se, že port **9093** není blokován bránou firewall.
- **TopicAuthorizationException** - Nejběžnější příčiny této výjimky jsou:
    - Překlep v připojovacím řetězci v konfiguračním souboru nebo
    - Pokouším se použít centra událostí pro Kafka v oboru názvů úrovně Basic. Centra událostí pro Kafka jsou [podporována pouze pro obory názvů standardní a vyhrazené úrovně](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Neshoda verzí Kafka** - Event Hubs for Kafka Ecosystems podporuje Kafka verze 1.0 a novější. Některé aplikace používající Kafka verze 0.10 a novější mohou občas fungovat z důvodu zpětné kompatibility protokolu Kafka, ale důrazně doporučujeme nepoužívat staré verze rozhraní API. Kafka verze 0.9 a starší nepodporují požadované protokoly SASL a nelze se připojit k Event Hubs.
- **Podivné kódování na hlavičkách AMQP při spotřebování s Kafka** - při odesílání událostí do centra událostí přes AMQP jsou všechny hlavičky datové části AMQP serializovány v kódování AMQP. Kafka spotřebitelé nemají rekonstruovat záhlaví z AMQP. Chcete-li číst hodnoty záhlaví, ručně dekódujte záhlaví AMQP. Případně se můžete vyhnout použití záhlaví AMQP, pokud víte, že budete spotřebovávat prostřednictvím protokolu Kafka. Další informace naleznete v [tomto problému GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **Ověřování SASL** – získání architektury pro spolupráci s ověřovacím protokolem SASL vyžadovým centrem událostí může být obtížnější, než se na první pohled zdá. Podívejte se, jestli můžete řešit potíže s konfigurací pomocí prostředků rozhraní framework u ověřování SASL. 

## <a name="limits"></a>Omezení
Apache Kafka vs. Event Hubs Kafka. Centra událostí pro ekosystémy Kafka má z větší části stejné výchozí hodnoty, vlastnosti, kódy chyb a obecné chování, které dělá Apache Kafka. Případy, kdy se tyto dvě explicitně liší (nebo kde Centra událostí ukládají omezení, které Kafka ne) jsou uvedeny níže:

- Maximální délka vlastnosti `group.id` je 256 znaků
- Maximální velikost `offset.metadata.max.bytes` je 1024 bajtů
- Posunení potvrzení jsou omezeny na 4 volání za sekundu na oddíl s maximální vnitřní velikost protokolu 1 MB


## <a name="next-steps"></a>Další kroky
Další informace o centru událostí a centru událostí pro Kafku najdete v následujících článcích:  

- [Průvodce vývojáři Apache Kafka pro Centra událostí](apache-kafka-developer-guide.md)
- [Průvodce migrací Apache Kafka pro centra událostí](apache-kafka-migration-guide.md)
- [Nejčastější dotazy - Event Hubs for Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Doporučené konfigurace](https://github.com/Azure/azure-event-hubs-for-kafka/blob/master/CONFIGURATION.md)
