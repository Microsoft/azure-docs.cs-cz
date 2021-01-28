---
title: Optimalizace výkonu pro clustery Apache Kafka HDInsight
description: Poskytuje přehled postupů pro optimalizaci Apache Kafka úloh v Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: d24527efe6adce6f16b7c890f23c755545f5d5a0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935893"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimalizace výkonu pro clustery Apache Kafka HDInsight

Tento článek obsahuje některé návrhy na optimalizaci výkonu Apache Kafka úloh v HDInsight. Zaměřuje se na úpravu konfigurace výrobců a zprostředkovatelů. Existují různé způsoby měření výkonu a optimalizace, které použijete, budou záviset na potřebách vaší firmy.

## <a name="architecture-overview"></a>Přehled architektury

Témata Kafka slouží k uspořádání záznamů. Záznamy jsou vytvářeny producenty a spotřebovávány konzumenty. Výrobci odesílají záznamy do zprostředkovatelů Kafka, které pak data ukládají. Každý pracovní uzel v clusteru HDInsight je zprostředkovatelem Kafka.

Témata rozdělují záznamy do oddílů napříč zprostředkovateli. Při využívání záznamů můžete dosáhnout paralelního zpracování dat díky použití až jednoho konzumenta pro každý oddíl.

Replikace se používá pro duplikaci oddílů mezi uzly. To chrání proti výpadkům uzlů (Broker). Jeden oddíl mezi skupinou replik je označený jako vedoucí oddílu. Provoz producenta se směruje do vedoucích jednotlivých uzlů pomocí stavu, který spravuje ZooKeeper.

## <a name="identify-your-scenario"></a>Určení scénáře

Apache Kafka výkon má dvě hlavní aspekty – propustnost a latence. Propustnost je maximální rychlost, s jakou se dají data zpracovat. Vyšší propustnost je obvykle lepší. Latence je čas potřebný k uložení nebo načtení dat. Nižší latence je obvykle lepší. Zjištění správného zůstatku mezi propustností, latencí a náklady na infrastrukturu aplikace může být náročné. Vaše požadavky na výkon budou pravděpodobně odpovídat jedné z následujících tří běžných situací, na základě toho, zda požadujete vysokou propustnost, nízkou latenci nebo obojí:

* Vysoká propustnost, nízká latence. Tento scénář vyžaduje vysokou propustnost a nízkou latenci (~ 100 milisekund). Příkladem tohoto typu aplikace je monitorování dostupnosti služby.
* Vysoká propustnost a vysoká latence. Tento scénář vyžaduje vysokou propustnost (~ 1,5 GB/s), ale může tolerovat vyšší latenci (< 250 ms). Příkladem tohoto typu aplikace je přijímání dat telemetrie pro procesy téměř v reálném čase, jako jsou aplikace pro zjišťování zabezpečení a vniknutí.
* Nízká propustnost, nízká latence. Tento scénář vyžaduje nízkou latenci (< 10 ms) pro zpracování v reálném čase, ale může tolerovat nižší propustnost. Příkladem tohoto typu aplikace je kontrola pravopisu a gramatiky v režimu online.

## <a name="producer-configurations"></a>Konfigurace výrobců

V následujících částech se zvýrazní některé z nejdůležitějších vlastností konfigurace za účelem optimalizace výkonu vašich výrobců Kafka. Podrobné vysvětlení všech vlastností konfigurace najdete v tématu [Apache Kafka dokumentaci týkající se konfigurací výrobců](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Velikost dávky

Apache Kafka výrobci sestavují skupiny zpráv (nazývaných dávky), které se odesílají jako jednotka, která se uloží do jediného oddílu úložiště. Velikost dávky znamená počet bajtů, které musí být před přenosem této skupiny přítomny. Zvýšením `batch.size` parametru se může zvýšit propustnost, protože se sníží režie zpracování ze sítě a vstupně-výstupních požadavků. Při lehkém zatížení může zvýšená velikost dávky zvýšit latenci při odesílání Kafka, protože producent čeká na přípravu dávky. V případě vysoké zátěže se doporučuje zvýšit velikost dávky, aby se zvýšila propustnost a latence.

### <a name="producer-required-acknowledgments"></a>Potvrzování požadovaných výrobců

Požadovaná konfigurace producent `acks` určí počet potvrzování vyžadovaných vedoucím oddílu před tím, než se žádost o zápis považuje za dokončenou. Toto nastavení ovlivňuje spolehlivost dat a přebírá hodnoty `0` , `1` nebo `-1` . Hodnota `-1` znamená, že před dokončením zápisu musí být přijato potvrzení ze všech replik. Nastavení `acks = -1` poskytuje silnější záruky před ztrátou dat, ale také má za následek vyšší latenci a nižší propustnost. Pokud požadavky vaší aplikace vyžadují vyšší propustnost, zkuste nastavit `acks = 0` nebo `acks = 1` . Pamatujte na to, že nepotvrzující všechny repliky můžou snížit spolehlivost dat.

### <a name="compression"></a>Komprese

Kafka výrobce je možné nakonfigurovat tak, aby před odesláním do zprostředkovatelů mohli Komprimovat zprávy. `compression.type`Nastavení určuje Kompresní kodek, který se má použít. Podporované kompresní kodeky jsou "gzip", "přichyceno" a "LZ4". Komprese je prospěšná a měla by se brát v úvahu, pokud dojde k omezení kapacity disku.

Mezi dvěma běžně používanými kompresními kodeky `gzip` a `snappy` `gzip` má vyšší kompresní poměr, což vede ke snížení využití disku za cenu vyššího zatížení procesoru. `snappy`Kodek poskytuje méně komprese s nižšími nároky na výkon procesoru. Podle toho, který kodek se má použít, můžete rozhodnout na základě omezení na disku nebo procesoru zprostředkovatele. `gzip` může komprimovat data v poměru pěti krát větší než `snappy` .

Při použití komprese dat se zvýší počet záznamů, které lze uložit na disk. Může taky zvýšit nároky na procesor v případech, kdy dojde ke neshodě mezi formáty komprese používanými producentem a zprostředkovatelem. protože před zpracováním je nutné data zkomprimovat a pak je dekomprimovat.

## <a name="broker-settings"></a>Nastavení zprostředkovatele

V následujících částech se zvýrazní některá z nejdůležitějších nastavení, která optimalizují výkon vašich zprostředkovatelů Kafka. Podrobné vysvětlení všech nastavení služby Broker najdete v tématu [Apache Kafka dokumentaci týkající se konfigurací výrobců](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Počet disků

Disky úložiště mají omezený počet IOPS (vstupně-výstupní operace za sekundu) a bajty čtení a zápisu za sekundu. Při vytváření nových oddílů Kafka ukládá každý nový oddíl na disk s nejmenším počtem existujících oddílů, aby je vyrovnaly napříč dostupnými disky. Navzdory strategii úložiště může Kafka při zpracování stovky replik oddílů na jednotlivých discích snadno zvýšit kapacitu dostupného disku. Kompromisy jsou mezi propustností a náklady. Pokud vaše aplikace vyžaduje větší propustnost, vytvořte cluster s větším množstvím spravovaných disků na jednoho zprostředkovatele. HDInsight v současné době nepodporuje přidávání spravovaných disků do spuštěného clusteru. Další informace o tom, jak nakonfigurovat počet spravovaných disků, najdete v tématu [Konfigurace úložiště a škálovatelnosti pro Apache Kafka v HDInsight](apache-kafka-scalability.md). Seznamte se s vlivem na náklady na zvýšení úložného prostoru pro uzly ve vašem clusteru.

### <a name="number-of-topics-and-partitions"></a>Počet témat a oddílů

Kafka výrobci zapisují do témat. Kafka spotřebitelé čtou z témat. Téma je přidruženo k protokolu, který je datovou strukturou na disku. Kafka připojí záznamy od výrobců ke konci protokolu tématu. Protokol tématu se skládá z mnoha oddílů, které jsou rozloženy do více souborů. Tyto soubory jsou zase rozloženy mezi několik uzlů clusteru Kafka. Spotřebitelé si načtou z Kafka témat na jejich tempo a můžou v protokolu tématu vybrat jejich pozici (posun).

Každý oddíl Kafka je soubor protokolu v systému a vlákna výrobců můžou zapisovat do několika protokolů současně. Podobně vzhledem k tomu, že každé uživatelské vlákno čte zprávy z jednoho oddílu, je zpracování z více oddílů paralelní i.

Zvýšení hustoty oddílů (počet oddílů na zprostředkovatele) přidává režijní náklady související s operacemi s metadaty a požadavky na oddíl/odpověď mezi vedoucím oddílu a jeho sledujícím. I v případě neexistence toku dat prostřednictvím repliky oddílů stále načítají data od vedoucích, což má za následek dodatečné zpracování pro odesílání a příjem požadavků přes síť.

Pro Apache Kafka clustery 1,1 a vyšší ve službě HDInsight doporučujeme, abyste měli maximálně 1000 oddílů na zprostředkovatele, včetně replik. Zvýšení počtu oddílů na zprostředkovatele sníží propustnost a může také způsobit nedostupnost tématu. Další informace o podpoře oddílů Kafka najdete v [příspěvku na blogu oficiálního Apache Kafka na zvýšení počtu podporovaných oddílů ve verzi 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Podrobnosti o úpravách témat najdete v tématu [Apache Kafka: úpravy témat](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Počet replik

Vyšší faktor replikace má za následek další požadavky mezi vedoucím oddílu a jeho sledujícím. V důsledku toho vyšší faktor replikace spotřebovává více disků a CPU, aby bylo možné zpracovávat další požadavky, zvýšení latence zápisu a snížení propustnosti.

Doporučujeme, abyste pro Kafka ve službě Azure HDInsight používali aspoň 3x replikaci. Většina oblastí Azure má tři domény selhání, ale v oblastech, které mají jenom dvě domény selhání, by uživatelé měli používat 4x pro replikaci.

Další informace o replikaci najdete v tématu [Apache Kafka: replikace](https://kafka.apache.org/documentation/#replication) a [Apache Kafka: zvýšení faktoru replikace](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Další kroky

* [Zpracování bilionů událostí za den pomocí platformy Apache Kafka v Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Co je Apache Kafka ve službě HDInsight?](apache-kafka-introduction.md)
