---
title: Optimalizace výkonu pro clustery Apache Kafka HDInsight
description: Obsahuje přehled technik pro optimalizaci úloh Apache Kafka v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494924"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimalizace výkonu pro clustery Apache Kafka HDInsight

Tento článek obsahuje několik návrhů pro optimalizaci výkonu úloh Apache Kafka v HDInsight. Důraz je kladen na úpravu konfigurace výrobce a makléře. Existují různé způsoby měření výkonu a optimalizace, které použijete, budou záviset na potřebách vašeho podniku.

## <a name="architecture-overview"></a>Přehled architektury

Témata Kafka slouží k uspořádání záznamů. Záznamy jsou vytvářeny producenty a spotřebovávány konzumenty. Výrobci posílají záznamy makléřům Kafka, kteří pak data ukládají. Každý pracovní uzel v clusteru HDInsight je zprostředkovatelem Kafka.

Témata rozdělují záznamy do oddílů napříč zprostředkovateli. Při využívání záznamů můžete dosáhnout paralelního zpracování dat díky použití až jednoho konzumenta pro každý oddíl.

Replikace se používá k duplikaci oddílů mezi uzly. To chrání před výpadky uzlu (zprostředkovatele). Jeden oddíl mezi skupinou replik je označen jako odkazová čára oddílu. Provoz producenta se směruje do vedoucích jednotlivých uzlů pomocí stavu, který spravuje ZooKeeper.

## <a name="identify-your-scenario"></a>Určení scénáře

Výkon Apache Kafka má dva hlavní aspekty – propustnost a latenci. Propustnost je maximální rychlost, jakou mohou být data zpracována. Vyšší propustnost je obvykle lepší. Latence je čas potřebný k uložení nebo načtení dat. Nižší latence je obvykle lepší. Nalezení správné rovnováhy mezi propustností, latencí a náklady na infrastrukturu aplikace může být náročné. Vaše požadavky na výkon se pravděpodobně budou shodovat s jednou z následujících tří běžných situací na základě toho, zda požadujete vysokou propustnost, nízkou latenci nebo obojí:

* Vysoká propustnost, nízká latence. Tento scénář vyžaduje vysokou propustnost a nízkou latenci (~100 milisekund). Příkladem tohoto typu aplikace je monitorování dostupnosti služby.
* Vysoká propustnost, vysoká latence. Tento scénář vyžaduje vysokou propustnost (~1,5 BPS), ale může tolerovat vyšší latenci (< 250 ms). Příkladem tohoto typu aplikace je požití telemetrických dat pro téměř v reálném čase procesy, jako jsou aplikace zabezpečení a detekce vniknutí.
* Nízká propustnost, nízká latence. Tento scénář vyžaduje nízkou latenci (< 10 ms) pro zpracování v reálném čase, ale může tolerovat nižší propustnost. Příkladem tohoto typu aplikace jsou kontroly pravopisu a gramatiky online.

## <a name="producer-configurations"></a>Konfigurace výrobců

V následujících částech se zvýrazní některé z nejdůležitějších vlastností konfigurace pro optimalizaci výkonu výrobců Kafka. Podrobné vysvětlení všech vlastností konfigurace naleznete v [dokumentaci Apache Kafka o konfiguracích výrobců](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Velikost dávky

Apache Kafka výrobci sestavit skupiny zpráv (tzv listy), které jsou odesílány jako jednotka, které mají být uloženy v jednom oddílu úložiště. Velikost dávky znamená počet bajtů, které musí být přítomny před přenosem této skupiny. Zvýšení `batch.size` parametru může zvýšit propustnost, protože snižuje nároky na zpracování ze síťových a vi požadavků. Při nízkém zatížení může zvýšená velikost dávky zvýšit latenci odesílání Kafka, protože výrobce čeká na připravenost dávky. Při velkém zatížení se doporučuje zvětšit velikost dávky, aby se zlepšila propustnost a latence.

### <a name="producer-required-acknowledgments"></a>Výrobce požadoval potvrzení

Konfigurace požadovaná `acks` výrobcem určuje počet potvrzení požadovaných vedoucím oddílu před tím, než je požadavek na zápis považován za dokončený. Toto nastavení má vliv na `0`spolehlivost `1`dat `-1`a přebírá hodnoty , , nebo . Hodnota `-1` znamená, že potvrzení musí být přijata ze všech replik před dokončením zápisu. Nastavení `acks = -1` poskytuje silnější záruky proti ztrátě dat, ale také vede k vyšší latenci a nižší propustnosti. Pokud požadavky aplikace vyžadují vyšší propustnost, zkuste nastavení `acks = 0` nebo `acks = 1`. Mějte na paměti, že nepotvrzení všech replik může snížit spolehlivost dat.

### <a name="compression"></a>Komprese

Výrobce Kafka lze nakonfigurovat tak, aby komprimoval zprávy před jejich odesláním zprostředkovatelům. Toto `compression.type` nastavení určuje kompresní kodek, který má být použit. Podporované kompresní kodeky jsou "gzip", "snappy" a "lz4". Komprese je výhodná a měla by být zvážena, pokud existuje omezení kapacity disku.

Mezi dvěma běžně používanými `gzip` kompresními kodeky a `snappy`, `gzip` má vyšší kompresní poměr, což má za následek nižší využití disku za cenu vyššího zatížení procesoru. Kodek `snappy` poskytuje menší kompresi s menší režií procesoru. Můžete se rozhodnout, který kodek použít na základě omezení procesoru zprostředkovatele nebo výrobce. `gzip`můžete komprimovat data rychlostí `snappy`pětkrát vyšší než .

Použití komprese dat zvýší počet záznamů, které mohou být uloženy na disku. Může také zvýšit režii procesoru v případech, kdy je neshoda mezi formáty komprese používané výrobcem a zprostředkovatelem. protože data musí být před odesláním komprimována a před zpracováním dekomprimována.

## <a name="broker-settings"></a>Nastavení brokera

V následujících částech se zvýrazní některá z nejdůležitějších nastavení pro optimalizaci výkonu vašich makléřů Kafka. Podrobné vysvětlení všech nastavení brokerů najdete v [dokumentaci Apache Kafka o konfiguracích výrobců](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="number-of-disks"></a>Počet disků

Disky úložiště mají omezené vstupně-výstupní operace (vstupní a výstupní operace za sekundu) a bajty pro čtení a zápis za sekundu. Při vytváření nových oddílů kafka ukládá každý nový oddíl na disk s nejmenším počtem existujících oddílů, aby je vyvážil na dostupných discích. Navzdory strategii úložiště může kafka při zpracování stovek replik oddílů na každém disku snadno nasytit dostupnou propustnost disku. Kompromis zde je mezi propustností a náklady. Pokud vaše aplikace vyžaduje větší propustnost, vytvořte cluster s více spravovanými disky na zprostředkovatele. HDInsight v současné době nepodporuje přidávání spravovaných disků do spuštěného clusteru. Další informace o konfiguraci počtu spravovaných disků najdete [v tématu Konfigurace úložiště a škálovatelnosti pro Apache Kafka na HDInsight](apache-kafka-scalability.md). Seznamte se s náklady na zvýšení úložného prostoru pro uzly v clusteru.

### <a name="number-of-topics-and-partitions"></a>Počet témat a oddílů

Kafka producenti psát na témata. Kafka spotřebitelé číst z témat. Téma je přidruženo k protokolu, což je datová struktura na disku. Kafka připojí záznamy od výrobce (výrobců) na konec protokolu tématu. Protokol tématu se skládá z mnoha oddílů, které jsou rozloženy do více souborů. Tyto soubory jsou zase rozloženy do více uzlů clusteru Kafka. Spotřebitelé číst z kafka témata na jejich kadence a můžete vybrat svou pozici (offset) v protokolu tématu.

Každý oddíl Kafka je soubor protokolu v systému a vlákna výrobce mohou zapisovat do více protokolů současně. Podobně vzhledem k tomu, že každý podproces příjemce čte zprávy z jednoho oddílu, spotřebovávající z více oddílů je zpracována paralelně také.

Zvýšení hustoty oddílu (počet oddílů na zprostředkovatele) přidá režii související s operacemi metadat a požadavek/odpověď na oddíl mezi vedoucím oddílu a jeho následovníky. I v případě, že data protékají, repliky oddílů stále načítat data z odkazových informací, což má za následek další zpracování pro odesílání a přijímání požadavků v síti.

Pro clustery Apache Kafka 1.1 a vyšší v HDInsight, doporučujeme mít maximálně 1000 oddílů na brokera, včetně replik. Zvýšení počtu oddílů na zprostředkovatele snižuje propustnost a může také způsobit nedostupnost tématu. Další informace o podpoře oddílů Kafka najdete [v oficiálním příspěvku na blogu Apache Kafka o zvýšení počtu podporovaných oddílů ve verzi 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Podrobnosti o úpravách témat naleznete v [tématu Apache Kafka: úpravy témat](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Počet replik

Vyšší faktor replikace má za následek další požadavky mezi odkazovou čárou oddílu a následovníky. V důsledku toho vyšší faktor replikace spotřebovává více disku a procesoru pro zpracování dalších požadavků, zvýšení latence zápisu a snížení propustnosti.

Doporučujeme použít alespoň 3x replikace pro Kafka v Azure HDInsight. Většina oblastí Azure má tři domény selhání, ale v oblastech s jenom dvě domény selhání, uživatelé by měli používat 4x replikace.

Další informace o replikaci najdete v [tématu Apache Kafka: replikace](https://kafka.apache.org/documentation/#replication) a [Apache Kafka: zvýšení replikačního faktoru](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Další kroky

* [Zpracování bilionů událostí za den pomocí platformy Apache Kafka v Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Co je Apache Kafka ve službě HDInsight?](apache-kafka-introduction.md)
