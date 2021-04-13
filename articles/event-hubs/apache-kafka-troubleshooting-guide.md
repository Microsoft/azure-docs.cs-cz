---
title: Řešení potíží se službou Azure Event Hubs pro Apache Kafka
description: Tento článek popisuje, jak řešit problémy s Azure Event Hubs pro Apache Kafka
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: a9d4a93f0074f206cd4627913505c66eb6480cbd
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314078"
---
# <a name="apache-kafka-troubleshooting-guide-for-event-hubs"></a>Průvodce odstraňováním potíží s Apache Kafka pro Event Hubs
Tento článek popisuje tipy k odstraňování problémů, ke kterým může dojít při použití Event Hubs pro Apache Kafka. 

## <a name="server-busy-exception"></a>Zaneprázdněná výjimka serveru
Můžete obdržet výjimku zaneprázdnění serveru z důvodu omezení Kafka. U klientů AMQP Event Hubs hned po omezení služby okamžitě vrací výjimku **zaneprázdnění serveru** . Je ekvivalentní se zprávou "zkusit znovu později". V Kafka jsou zprávy před dokončením zpožděny. Délka zpoždění se vrátí v milisekundách jako `throttle_time_ms` v odpovědi na zpracování nebo načtení. Ve většině případů tyto opožděné požadavky nejsou zaprotokolovány jako výjimky zaneprázdněnosti serveru v Event Hubs řídicích panelech. Místo toho `throttle_time_ms` by měla být použita hodnota odpovědi jako ukazatel, který propustnost překročila zřízenou kvótu.

Pokud je přenos nadměrný, služba má následující chování:

- Pokud dojde k překročení časového limitu požadavku, Event Hubs vrátí kód chyby **porušení zásad** , pokud vyprodukuje zpoždění požadavku.
- Pokud zpoždění žádosti o načtení překročí časový limit žádosti, Event Hubs protokolovat požadavek jako omezený a odpoví s prázdnou sadou záznamů a bez kódu chyby.

[Vyhrazené clustery](event-hubs-dedicated-overview.md) nemají mechanismy omezování. Nebudete využívat všechny prostředky clusteru.

## <a name="no-records-received"></a>Nepřijaly se žádné záznamy.
Můžete se setkat s tím, že spotřebitelé nezískávají žádné záznamy a neustále se vyrovnávají. V tomto scénáři spotřebitelé nezískají žádné záznamy a neustále se přerovnávají. V případě, že k tomu nedochází, není k dispozici žádná výjimka nebo chyba, ale v protokolech Kafka se dozvíte, že se uživatelé zablokují a pokoušejí se znovu připojit ke skupině a přiřazovat Existuje několik možných příčin:

- Ujistěte se, že `request.timeout.ms` je minimální hodnota 60000 a že `session.timeout.ms` je alespoň doporučená hodnota 30000. Je-li toto nastavení příliš nízké, může dojít k vypršení časových limitů příjemců, což způsobí, že dojde k rebilanci (což pak způsobí více časových limitů, což způsobí větší vyvážení atd.) 
- Pokud vaše konfigurace odpovídá těmto doporučeným hodnotám a stále se zobrazuje stálé nové vyrovnání, můžete otevřít problém (Nezapomeňte, že byste měli mít k dispozici celou konfiguraci, abyste mohli přispět k ladění)!

## <a name="compressionmessage-format-version-issue"></a>Problém verze formátu komprese/zprávy
Kafka podporuje kompresi a Event Hubs pro Kafka aktuálně ne. Chyby, které zmiňují verzi formátu zprávy (například), `The message format version on the broker does not support the request.` jsou způsobeny tím, že se klient pokusí do našich zprostředkovatelů odeslat komprimované zprávy Kafka.

Pokud jsou komprimovaná data nezbytná, komprimace dat před odesláním do zprostředkovatelů a dekomprimace po přijetí představuje platné alternativní řešení. Tělo zprávy je pouze pole bajtů pro službu, takže komprese nebo dekomprese na straně klienta nezpůsobí žádné problémy.

## <a name="unknownserverexception"></a>UnknownServerException
Můžete obdržet UnknownServerException z klientských knihoven Kafka podobně jako v následujícím příkladu: 

```
org.apache.kafka.common.errors.UnknownServerException: The server experienced an unexpected error when processing the request
```

Otevřete lístek s podporou Microsoftu.  Protokolování na úrovni ladění a časová razítka výjimky ve standardu UTC jsou užitečné při ladění problému. 

## <a name="other-issues"></a>Další problémy
Zkontrolujte následující položky, pokud se při použití Kafka na Event Hubs zobrazí problémy.

- **Brána firewall blokující provoz** – zajistěte, aby brána firewall neblokovala port **9093** .
- **TopicAuthorizationException** – nejběžnější příčiny této výjimky jsou:
    - Překlep v připojovacím řetězci v konfiguračním souboru nebo
    - Probíhá pokus o použití Event Hubs pro Kafka v oboru názvů úrovně Basic. Funkce Event Hubs for Kafka je [podporována pouze pro obory názvů Standard a vyhrazené úrovně](https://azure.microsoft.com/pricing/details/event-hubs/).
- **Neshoda verzí Kafka** – Event Hubs pro ekosystémy Kafka podporuje Kafka verze 1,0 a novější. Některé aplikace využívající Kafka verze 0,10 a novější můžou občas fungovat kvůli zpětné kompatibilitě protokolu Kafka, ale důrazně doporučujeme používat starší verze rozhraní API. Kafka verze 0,9 a starší nepodporují požadované protokoly SASL a nelze se připojit k Event Hubs.
- **Podivné kódování v hlavičkách AMQP při použití s Kafka** – při posílání událostí do centra událostí přes AMQP jsou jakákoli AMQP hlavičky datové části serializovány v kódování AMQP. Kafka uživatelé nepoužívají k deserializaci hlaviček z AMQP. Chcete-li načíst hodnoty hlaviček, ručně dekóduje hlavičky AMQP. Případně se můžete vyhnout používání hlaviček AMQP, pokud víte, že budete spotřebovávat prostřednictvím protokolu Kafka. Další informace najdete v [tomto problému GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/issues/56).
- **SASL ověřování** – připravujeme vaše rozhraní, aby spolupracovalo s ověřovacím protokolem SASL, který vyžadovaná Event Hubs může být obtížnější, než aby splňovalo oči. Podívejte se, jestli můžete řešit problémy s konfigurací pomocí prostředků vašeho rozhraní na SASL ověřování. 

## <a name="limits"></a>Omezení
Apache Kafka vs. Event Hubs Kafka. Ve většině případů má rozhraní Kafka Azure Event Hubs stejné výchozí hodnoty, vlastnosti, kódy chyb a obecné chování, které Apache Kafka dělá. Instance, které tyto dva explicitně liší (nebo kde Event Hubs ukládá omezení Kafka), jsou uvedeny níže:

- Maximální délka `group.id` vlastnosti je 256 znaků.
- Maximální velikost `offset.metadata.max.bytes` je 1024 bajtů.
- Posunutí potvrzení jsou omezená na 4 volání/sekundu na oddíl s maximální velikostí interního protokolu 1 MB.


## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs a Event Hubs pro Kafka najdete v následujících článcích:  

- [Apache Kafka příručka pro vývojáře pro Event Hubs](apache-kafka-developer-guide.md)
- [Průvodce migrací Apache Kafka pro Event Hubs](apache-kafka-migration-guide.md)
- [Nejčastější dotazy – Event Hubs pro Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [Doporučené konfigurace](apache-kafka-configurations.md)
