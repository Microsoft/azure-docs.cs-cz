---
title: Doporučené konfigurace pro klienty Apache Kafka – Azure Event Hubs
description: Tento článek poskytuje doporučené konfigurace Apache Kafka pro klienty, kteří pracují s Azure Event Hubs pro Apache Kafka.
ms.topic: reference
ms.date: 03/03/2021
ms.openlocfilehash: be009aae41b2cb26ab02fdbe14bc4e18311ad235
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102042347"
---
# <a name="recommended-configurations-for-apache-kafka-clients"></a>Doporučené konfigurace pro klienty Apache Kafka
Tady jsou doporučené konfigurace pro používání Azure Event Hubs z Apache Kafka klientských aplikací. 

## <a name="java-client-configuration-properties"></a>Vlastnosti konfigurace klienta Java

### <a name="producer-and-consumer-configurations"></a>Konfigurace výrobců a příjemců

Vlastnost | Doporučené hodnoty | Povolený rozsah | Poznámky
---|---:|-----:|---
`metadata.max.age.ms` | 180000 (přibližná) | < 240000 | Dá se snížit, aby se změny metadat dřív vybraly.
`connections.max.idle.ms`   | 180000 | < 240000 | Azure uzavře příchozí TCP nečinný > 240 000 MS, což může vést k odeslání nedoručených připojení (zobrazují se jako vyprší dávka z důvodu časového limitu odeslání).

### <a name="producer-configurations-only"></a>Pouze konfigurace výrobců
Konfigurace výrobců najdete [tady](https://kafka.apache.org/documentation/#producerconfigs).

Vlastnost | Doporučené hodnoty | Povolený rozsah | Poznámky
---|---:|---:|---
`max.request.size` | 1000000 | < 1046528 | Služba ukončí připojení, pokud jsou odesílány požadavky větší než 1 046 528 bajtů.  *Tato hodnota **musí** být změněna a bude způsobovat problémy ve scénářích s vysokou propustností.*
`retries` | > 0 | | Může vyžadovat zvýšení hodnoty delivery.timeout.ms, viz dokumentace.
`request.timeout.ms` | 30000... 60000 | > 20000| EH interně nastaví jako výchozí minimální hodnotu 20 000 ms.  *I když jsou požadavky s nižšími hodnotami časového limitu přijaty, chování klienta není zaručeno.*
`metadata.max.idle.ms` | 180000 | > 5000 | Určuje, jak dlouho bude producent uchovávat metadata v mezipaměti pro téma, které je nečinné. Pokud uplynulý čas od posledního vytvořeného tématu přesáhne dobu nečinnosti, bude metadata tohoto tématu zapomenuto a další přístup k němu vynutí požadavek na načtení metadat.
`linger.ms` | > 0 | | V případě scénářů s vysokou propustností by měla být hodnota možnosti permanentní rovna nejvyšší přípustné hodnotě, aby bylo možné využít dávkování.
`delivery.timeout.ms` | | | Nastavte podle vzorce ( `request.timeout.ms`  +  `linger.ms` ) * `retries` .
`compression.type` | `none` | | Komprese aktuálně není podporovaná...

### <a name="consumer-configurations-only"></a>Jenom konfigurace příjemců
Konfigurace příjemce najdete [tady](https://kafka.apache.org/documentation/#consumerconfigs).

Vlastnost | Doporučené hodnoty | Povolený rozsah | Poznámky
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 | | 3000 je výchozí hodnota a neměla by se změnit.
`session.timeout.ms` | 30000 |6000... 300000| Začněte s 30000. v důsledku zmeškaných prezenčních signálů zvyšte zobrazení častého opětovného vyrovnávání.


## <a name="librdkafka-configuration-properties"></a>Vlastnosti konfigurace librdkafka
Hlavní `librdkafka` konfigurační soubor ([odkaz](https://github.com/edenhill/librdkafka/blob/master/CONFIGURATION.md)) obsahuje rozšířené popisy pro níže uvedené vlastnosti.

### <a name="producer-and-consumer-configurations"></a>Konfigurace výrobců a příjemců

Vlastnost | Doporučené hodnoty | Povolený rozsah | Poznámky
---|---:|-----:|---
`socket.keepalive.enable` | true | | Nezbytné, pokud se očekává, že je připojení nečinné.  Azure ukončí příchozí TCP nečinný > 240 000 ms.
`metadata.max.age.ms` | ~ 180000| < 240000 | Dá se snížit, aby se změny metadat dřív vybraly.

### <a name="producer-configurations-only"></a>Pouze konfigurace výrobců

Vlastnost | Doporučené hodnoty | Povolený rozsah | Poznámky
---|---:|-----:|---
`retries` | > 0 | | Výchozí hodnota je 2. Tuto hodnotu Doporučujeme zachovat. 
`request.timeout.ms` | 30000... 60000 | > 20000| EH interně nastaví jako výchozí minimální hodnotu 20 000 ms.  `librdkafka` Výchozí hodnota je 5000, což může být problematické. *I když jsou požadavky s nižšími hodnotami časového limitu přijaty, chování klienta není zaručeno.*
`partitioner` | `consistent_random` | Viz dokumentace k librdkafka | `consistent_random` je výchozí a nejlepší.  Prázdné a null klíče jsou ve většině případů zpracovávány v ideálním případě.
`compression.codec` | `none` || Komprese aktuálně není podporována.

### <a name="consumer-configurations-only"></a>Jenom konfigurace příjemců

Vlastnost | Doporučené hodnoty | Povolený rozsah | Poznámky
---|---:|-----:|---
`heartbeat.interval.ms` | 3000 || 3000 je výchozí hodnota a neměla by se změnit.
`session.timeout.ms` | 30000 |6000... 300000| Začněte s 30000. v důsledku zmeškaných prezenčních signálů zvyšte zobrazení častého opětovného vyrovnávání.


## <a name="further-notes"></a>Další poznámky

Podívejte se na následující tabulku běžných scénářů chyb souvisejících s konfigurací.

Příznaky | Problém | Řešení
----|---|-----
Posun neúspěšných potvrzení kvůli novému vyrovnávání | Váš příjemce čeká mezi voláními na dotazování () příliš dlouho a služba vychází ze skupiny příjemců. | Máte několik možností: <ul><li>Zvýšit časový limit zpracování dotazů ( `max.poll.interval.ms` )</li><li>Snížit velikost dávky zprávy a zrychlit zpracování</li><li>Vylepšete paralelní zpracování, aby nedošlo k blokování příjemce. dotazování ()</li></ul> Použití některé kombinace tří je nejspíš wisest.
Výjimky sítě při vysoké propustnosti | Používáte pro klienta Java + výchozí hodnotu max. Request. Size?  Vaše požadavky můžou být moc velké. | Viz Konfigurace jazyka Java výše.

## <a name="next-steps"></a>Další kroky
V tématu [limity, kvóty a omezení předplatného a služeb Azure](..//azure-resource-manager/management/azure-subscription-service-limits.md) pro kvóty a omezení pro všechny služby Azure. 
