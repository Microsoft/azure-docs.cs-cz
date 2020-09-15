---
title: Použití centra událostí z aplikace Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o podpoře Apache Kafka službou Azure Event Hubs.
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 29850e89d1cccf7708e5cca8eaf58afee8157890
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061403"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Použití Azure Event Hubs z Apache Kafkach aplikací
Event Hubs poskytuje koncový bod kompatibilní s rozhraními API Apache Kafka® výrobce a spotřebitele, která můžou používat většina stávajících klientských aplikací Apache Kafka jako alternativu ke spuštění vlastního Apache Kafka clusteru. Event Hubs podporuje klienty rozhraní API Apache Kafka výrobce a příjemce ve verzi 1,0 a vyšší.

> [!VIDEO https://www.youtube.com/embed/UE1WgB96_fc]

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co Event Hubs for Kafka poskytnout?

Funkce Event Hubs for Apache Kafka poskytuje hlavní hlavičku protokolu pro Azure Event Hubs, která je kompatibilní s protokolem Apache Kafka klienty vytvořenými pro Apache Kafka Server verze 1,0 a novější a podporuje jak čtení z, tak i zápis na Event Hubs, což je ekvivalentem Apache Kafka témat. 

Ve svých aplikacích můžete často použít Event Hubs koncový bod Kafka, aniž byste museli měnit kód v porovnání s existujícím nastavením Kafka a upravovat jenom konfiguraci: aktualizujte připojovací řetězec v konfiguracích tak, aby odkazoval na koncový bod Kafka vystavený centrem událostí, a nemusíte ukazovat na svůj cluster Kafka. Pak můžete spustit streamování událostí z aplikací, které používají protokol Kafka, do Event Hubs. 

Koncepční, Kafka a Event Hubs jsou velmi podobné: Jedná se o segmentované protokoly sestavené pro streamovaná data, kde klient řídí, která součást uchovávaného protokolu si chce přečíst. Následující tabulka mapuje koncepty mezi Kafka a Event Hubs.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Kafka a konceptuální mapování centra událostí

| Koncept Kafka | Event Hubs koncept|
| --- | --- |
| Cluster | Obor názvů |
| Téma | Centrum událostí |
| Oddíl | Oddíl|
| Skupina uživatelů | Skupina uživatelů |
| Posun | Posun|

### <a name="key-differences-between-apache-kafka-and-event-hubs"></a>Klíčové rozdíly mezi Apache Kafka a Event Hubs

I když [Apache Kafka](https://kafka.apache.org/) je software, který obvykle potřebujete k instalaci a provozování, Event Hubs je plně spravovaná cloudová služba, která je nativní. Neexistují žádné servery, disky ani sítě, které by bylo možné spravovat a monitorovat, a žádné služby, které by bylo možné zvážit ani konfigurovat, kdykoli. Vytvoříte obor názvů, což je koncový bod s plně kvalifikovaným názvem domény, a potom v rámci tohoto oboru názvů vytvoříte Event Hubs (témata). 

Další informace o Event Hubs a oborech názvů najdete v tématu [Event Hubs funkce](event-hubs-features.md#namespace). Jako cloudová služba Event Hubs používá jedinou stabilní virtuální IP adresu jako koncový bod, takže klienti nepotřebují znát o vzdálení nebo počítačích v clusteru. I když Event Hubs implementuje stejný protokol, tento rozdíl znamená, že všechny přenosy Kafka pro všechny oddíly jsou prediktivním způsobem směrovány prostřednictvím tohoto jednoho koncového bodu, ale nevyžadují přístup k bráně firewall pro všechny zprostředkovatele clusteru.   

Škála v Event Hubs je řízena počtem jednotek propustnosti, které zakoupíte, s každou jednotkou propustnosti entitling 1 megabajt za sekundu nebo 1000 událostí za sekundu příchozího přenosu dat a dvakrát tento svazek. Event Hubs může automaticky škálovat jednotky propustnosti při dosažení limitu propustnosti, pokud používáte funkci [automatického rozplochení](event-hubs-auto-inflate.md) ; Tato funkce funguje taky s podporou protokolu Apache Kafka.  

### <a name="is-apache-kafka-the-right-solution-for-your-workload"></a>Je Apache Kafka správné řešení pro vaše zatížení?

Připravujeme z vytváření aplikací pomocí Apache Kafka, bude také užitečné pochopit, že Azure Event Hubs je součástí loďstva služeb, která zahrnuje i [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)a [Azure Event Grid](../event-grid/overview.md). 

I když někteří poskytovatelé komerčních distribucí Apache Kafka můžou navrhnout, že Apache Kafka je jednou zarážkami pro potřeby vaší platformy pro zasílání zpráv, je tato skutečnost taková, že Apache Kafka neimplementuje, například vzor fronty [konkurenčního příjemce](/azure/architecture/patterns/competing-consumers) nemá podporu pro  [publikování a odběr](/azure/architecture/patterns/publisher-subscriber) na úrovni, která umožňuje předplatitelům přístup k příchozím zprávám na základě jiných pravidel vyhodnocených serverem než jednoduchých posunů a nemá žádná zařízení ke sledování životního cyklu úlohy iniciované zprávou nebo sidelining vadných zpráv do fronty nedoručených zpráv, což je základem pro mnoho podnikových scénářů zasílání zpráv.

Pro pochopení rozdílů mezi vzorci a podle toho, která služba nejlépe pokrývá tuto službu, přečtěte si téma [Možnosti asynchronního zasílání zpráv v dokumentaci Azure](/azure/architecture/guide/technology-choices/messaging) . Jako uživatel Apache Kafka se můžete setkat s tím, že komunikační cesty, které jste doposud provedli s Kafka, můžou být realizované s mnohem méně základní složitou složitostí a ještě výkonnějšími funkcemi pomocí Event Grid nebo Service Bus. 

Pokud potřebujete konkrétní funkce Apache Kafka, které nejsou k dispozici prostřednictvím Event Hubs pro Apache Kafka rozhraní, nebo pokud váš vzor implementace překročí [Event Hubs kvóty](event-hubs-quotas.md), můžete také spustit [nativní Apache Kafka cluster ve službě Azure HDInsight](../hdinsight/kafka/apache-kafka-introduction.md).  

## <a name="security-and-authentication"></a>Zabezpečení a ověřování
Při každém publikování nebo zpracování událostí z Event Hubs pro Kafka se klient pokouší získat přístup k prostředkům Event Hubs. Chcete zajistit, aby k prostředkům byl přistup pomocí autorizované entity. Při použití Apache Kafka protokolu u klientů můžete nastavit konfiguraci pro ověřování a šifrování pomocí mechanismů SASL. Při použití Event Hubs pro Kafka se vyžaduje šifrování TLS (protože všechna data v přenosu s Event Hubs jsou šifrovaná TLS). Můžete to udělat zadáním možnosti SASL_SSL v konfiguračním souboru. 

Azure Event Hubs poskytuje několik možností, jak autorizovat přístup k vašim zabezpečeným prostředkům. 

- OAuth 2.0
- Sdílený přístupový podpis (SAS)

#### <a name="oauth-20"></a>OAuth 2.0
Event Hubs se integruje s Azure Active Directory (Azure AD), která poskytuje centralizovaný autorizační server kompatibilní s **OAuth 2,0** . Pomocí Azure AD můžete použít řízení přístupu na základě role (RBAC) k udělení jemně odstupňovaných oprávnění pro identity klientů. Tuto funkci můžete použít u klientů Kafka zadáním **SASL_SSL** pro protokol a  **OAUTHBEARER** pro mechanismus. Podrobnosti o rolích a úrovních Azure pro přístup k oboru najdete v tématu [autorizace přístupu pomocí Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Sdílený přístupový podpis (SAS)
Event Hubs taky poskytuje **sdílené přístupové podpisy (SAS)** pro delegovaný přístup k Event Hubs prostředkům Kafka. Autorizaci přístupu pomocí mechanismu OAuth 2,0 s tokenem založeným na tokenech zabezpečení poskytuje prvotřídní zabezpečení a snadné použití prostřednictvím SAS. Předdefinované role mohou také eliminovat nutnost ověřování na základě seznamu ACL, které musí uživatel spravovat a spravovat. Tuto funkci můžete použít spolu s klienty Kafka zadáním **SASL_SSL** pro protokol a pro mechanismus **jako prostý** . 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!NOTE]
> Při použití ověřování pomocí SAS u klientů Kafka nejsou vytvořená připojení po opětovném vygenerování klíče SAS odpojena. 

#### <a name="samples"></a>ukázky 
**Kurz** s podrobnými pokyny k vytvoření centra událostí a přístup k němu pomocí SAS nebo OAuth najdete v tématu [rychlý Start: streamování dat pomocí Event Hubs pomocí protokolu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Další **ukázky** , které ukazují, jak používat OAuth s Event Hubs pro Kafka, najdete v tématu [ukázky na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features"></a>Další funkce Event Hubs 

Funkce Event Hubs for Apache Kafka je jedním ze tří protokolů, které jsou souběžně dostupné v Azure Event Hubs, doplňují požadavky HTTP a AMQP. Můžete psát pomocí kteréhokoli z těchto protokolů a číst je s jiným způsobem, aby vaše aktuální Apache Kafka výrobci mohli pokračovat v publikování prostřednictvím Apache Kafka, ale čtenář může těžit z nativní integrace s rozhraním Event Hubs "AMQP", například Azure Stream Analytics nebo Azure Functions. V opačném případě můžete Azure Event Hubs snadno integrovat do sítí směrování AMQP jako cílový koncový bod a teprve potom číst data prostřednictvím Apache Kafka integrací.  

Kromě toho Event Hubs funkce, jako je například [Capture](event-hubs-capture-overview.md), což umožňuje extrémně dlouhodobě dlouhodobou archivaci pomocí Azure Blob Storage a Azure Data Lake Storage a [geografická havárie – obnovení](event-hubs-geo-dr.md) funguje i s Event Hubs pro funkci Kafka.

## <a name="apache-kafka-feature-differences"></a>Rozdíly v Apache Kafka funkcí 

Cílem Event Hubs pro Apache Kafka je poskytnout přístup k funkcím centra událostí Azure aplikacím, které jsou uzamčené do rozhraní API služby Apache Kafka a jinak by se muselo zálohovat pomocí clusteru Apache Kafka. 

Jak je vysvětleno [výše](#is-apache-kafka-the-right-solution-for-your-workload), poskytuje Azure zasílání zpráv Azure bohatě a robustní pokrytí mnoha scénářů zasílání zpráv a i když Event Hubs podporuje Apache Kafka rozhraní API následující funkce, ukazujíme, kde a jak je požadovaná funkce dostupná.

### <a name="transactions"></a>Transakce

[Azure Service Bus](../service-bus-messaging/service-bus-transactions.md) má robustní podporu transakcí, která umožňuje příjem a vyrovnávání zpráv a relací při posílání odchozích zpráv, které vyplývají ze zpracování zpráv do více cílových entit v rámci ochrany konzistence transakce. Sada funkcí umožňuje nejen přesně jednou zpracovat každou zprávu v sekvenci, ale zároveň se jim vyhne riziko, že by nechtěně znovu zpracovala stejné zprávy, jako by to byl případ s Apache Kafka. Service Bus je doporučená služba pro úlohy transakčních zpráv.

### <a name="compression"></a>Komprese

Funkce [Komprese](https://cwiki.apache.org/confluence/display/KAFKA/Compression) na straně klienta Apache Kafka komprimuje dávku více zpráv do jedné zprávy na straně producenta a dekomprimuje dávku na straně spotřebitele. Zprostředkovatel Apache Kafka považuje dávku za speciální zprávu.

Tato funkce je v podstatě na lichá s modelem víceprotokolového protokolu Azure Event Hubs, který umožňuje, aby se zprávy, i ty, které se odesílají v dávkách, jednotlivě nacházely z zprostředkovatele a prostřednictvím libovolného protokolu. 

Datová část události centra událostí je datový proud bajtů a obsah lze zkomprimovat pomocí algoritmu, který zvolíte. Formát kódování Apache Avro podporuje nativní komprimaci.

### <a name="log-compaction"></a>Komprimace protokolů

Apache Kafka komprimace protokolu je funkce, která umožňuje vyřadit všechny kromě posledního záznamu každého klíče z oddílu, což efektivně přepíná Apache Kafka téma do úložiště hodnot klíčů, kde Poslední přidaná hodnota Přepisuje předchozí. Databázové služby, jako je například [Azure Cosmos DB](../cosmos-db/introduction.md), jsou mnohem lepší, i když jsou vzor úložiště klíč-hodnota i s častými aktualizacemi.

Funkce komprimace protokolů se používá v klientech rozhraní Kafka Connect a Kafka Streams.

### <a name="kafka-streams"></a>Datové proudy Kafka

Kafka Streams je Klientská knihovna pro Stream Analytics, která je součástí Apache Kafka open source projektu, ale je oddělená od Apache Kafka zprostředkovatele datových proudů událostí. 

Nejčastějším důvodem, proč zákazníci s Azure Event Hubs žádají o podporu datových proudů Kafka, je, že mají zájem o ksqlDB produkt. "ksqlDB" je proprietární sdílený zdrojový projekt, který má [licenci](https://github.com/confluentinc/ksql/blob/master/LICENSE) k tomu, že žádný dodavatel, který nabízí software jako služba, infrastruktura jako služba, infrastruktura jako služba nebo jiná podobná online služby, která soutěží s konkurenčními produkty nebo službami, může používat nebo nabízet podporu "ksqlDB". Prakticky, pokud používáte ksqlDB, musíte buď Kafka sami, nebo musíte použít cloudové nabídky. Licenční podmínky můžou mít vliv i na zákazníky Azure, kteří nabízejí služby pro účely vyloučené licencí.

Kafka datové proudy a bez ksqlDB mají méně možností než mnoho alternativních rozhraní a služeb. většina z nich má Vestavěná rozhraní pro streamování SQL a všechny, které se integrují s Azure Event Hubs ještě dnes:

- [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md)
- [Azure synapse Analytics (prostřednictvím zachycení Event Hubs)](../event-grid/event-grid-event-hubs-integration.md)
- [Azure Databricks](/azure/databricks/scenarios/databricks-stream-from-eventhubs.md)
- [Apache Samza](https://samza.apache.org/learn/documentation/latest/connectors/eventhubs)
- [Apache Storm](event-hubs-storm-getstarted-receive.md)
- [Apache Spark](event-hubs-kafka-spark-tutorial.md)
- [Apache Flink](event-hubs-kafka-flink-tutorial.md)
- [Datové proudy Akka](event-hubs-kafka-akka-streams-tutorial.md)

Uvedené služby a architektury můžou obecně získávat datové proudy událostí a referenční data přímo z různých sad zdrojů prostřednictvím adaptérů. Kafka streamy můžou získat data jenom z Apache Kafka a vaše analytické projekty jsou proto uzamčené do Apache Kafka. Chcete-li použít data z jiných zdrojů, je nutné nejprve importovat data do Apache Kafka pomocí architektury Kafka Connect.

Pokud musíte použít rozhraní Kafka Streams v Azure, [Apache Kafka v HDInsight](../hdinsight/kafka/apache-kafka-introduction.md) vám nabídne tuto možnost. Apache Kafka v HDInsight poskytuje plnou kontrolu nad všemi aspekty konfigurace Apache Kafka a přitom se plně integruje s různými aspekty platformy Azure, od umístění v doméně selhání/aktualizace k izolaci sítě až po monitorování integrace. 

## <a name="next-steps"></a>Další kroky
Tento článek poskytuje Úvod do Event Hubs pro Kafka. Další informace najdete v tématu [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md).
