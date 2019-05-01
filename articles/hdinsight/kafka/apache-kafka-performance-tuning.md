---
title: Optimalizace výkonu pro clustery Apache Kafka HDInsight
description: Obsahuje přehled postupů pro optimalizaci úlohy Apache Kafka v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 8226d1f49b8ba73870dba009e97ff2718a0eee27
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689359"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Optimalizace výkonu pro clustery Apache Kafka HDInsight

Tento článek obsahuje některé návrhy týkající se optimalizace výkonu vašich úloh Apache Kafka v HDInsight. Zaměřuje se na nastavení výrobce a zprostředkovatele konfigurace. Existují různé způsoby měření výkonu a optimalizace, které použijete, bude záviset na vašich obchodních potřeb.

## <a name="architecture-overview"></a>Přehled architektury

Témat Kafka se používají k uspořádání záznamy. Záznamy jsou vytvářené producenti a využívat spotřebiteli. Producenti Odeslat záznamy zprostředkovatelům systému Kafka, které potom ukládají data. Každý pracovní uzel v clusteru HDInsight je zprostředkovatelem Kafka.

Témata rozdělují záznamy do oddílů napříč zprostředkovateli. Při využívání záznamů můžete dosáhnout paralelního zpracování dat díky použití až jednoho konzumenta pro každý oddíl.

Replikace se používá k duplicitní oddílů mezi uzly. To chrání před výpadky uzlů (zprostředkovatelé). Jeden oddíl mezi skupiny repliky je určený jako lídr oddílu. Provoz producenta se směruje do vedoucích jednotlivých uzlů pomocí stavu, který spravuje ZooKeeper.

## <a name="identify-your-scenario"></a>Určení scénáře

Apache Kafka výkonu má dva hlavní aspekty – latence a propustnosti. Propustnost je maximální rychlost přenosu, na které lze zpracovat data. Vyšší propustnost je obvykle vhodnější. Latence je doba, která je potřebná pro data uložená nebo se musí načíst. Nižší latence je obvykle vhodnější. Jak najít správnou rovnováhu mezi propustnosti, latence a nákladů na infrastrukturu aplikace může být náročné. Vašim požadavkům na výkon pravděpodobně odpovídat jedné z následujících tři běžné situace, na základě na, zda potřebujete vysokou propustnost a nízká latence:

* Vysoká propustnost, nízkou latenci. Tento scénář vyžaduje vysokou propustností a nízkou latencí (~ 100 milisekund). Příkladem takové aplikace je služby monitorování dostupnosti.
* Vysoká propustnost, vysokou latenci. Tento scénář vyžaduje vysokou propustnost (~1.5 GB/s), ale může tolerovat vyšší latence (< 250 ms). Příkladem tento typ aplikace je příjem dat telemetrie pro téměř v reálném čase procesy, jako je aplikace zjišťování neoprávněných vniknutí a zabezpečení.
* Nízká propustnost, nízká latence. Tento scénář vyžaduje pro zpracování v reálném čase s nízkou latencí (< 10 ms), ale dokáží odolat nižší propustnost. Příkladem takové aplikace je online kontroly pravopisu a gramatiky.

## <a name="producer-configurations"></a>Konfigurace výrobce

V dalších částech se zaměřuje na některé z vašich nejdůležitějších vlastnosti konfigurace za účelem optimalizace výkonu vaší výrobců Kafka. Podrobné vysvětlení všech vlastností konfigurace najdete v tématu [dokumentace Apache Kafka producer konfigurace](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Velikost dávky

Apache Kafka producenti sestavit skupiny zprávy (označované jako listy), které se odesílají jako jednotku k uložení do jednoho úložiště oddílu. Velikost dávky znamená, že počet bajtů, které musí být přítomné přenášená této skupiny. Zvyšuje `batch.size` parametr může zvýšit propustnost, protože snižuje režii ze sítě a požadavky na vstupně-výstupní operace zpracování. V rámci mírném zatížení velikost dávky zvýšenou zvýšit latence odesílání přes Kafka podle výrobce čeká batch bude připravené. V případě velkého zatížení se doporučuje zvýšit velikost dávky pro zlepšení latence a propustnosti.

### <a name="producer-required-acknowledgements"></a>Výrobce vyžaduje potvrzení

Výrobce vyžaduje `acks` konfigurace určuje počet potvrzení před musí být vedoucí instancí oddílu žádost o zápis se považuje za dokončení. Toto nastavení ovlivní spolehlivost dat a trvá hodnoty `0`, `1`, nebo `-1`. Hodnota `-1` znamená, že potvrzení musí být přijata ze všech replik, předtím, než se dokončí zápis. Nastavení `acks = -1` poskytují větší záruku proti ztrátě dat, ale také výsledky v vyšší latence a propustnosti nižší. Pokud vaše požadavky na aplikace vyžadují vyšší propustnost, zkuste `acks = 0` nebo `acks = 1`. Mějte na paměti, který není potvrdil všechny repliky spolehlivost dat může snížit.

### <a name="compression"></a>Komprese

Kafka producer je možné nakonfigurovat pro kompresi zprávy před odesláním do zprostředkovatele. `compression.type` Nastavení určuje kompresní kodek, který se má použít. Komprese podporované kodeky jsou "gzip," "snappy," a "lz4." Komprese je výhodné a má brát, pokud existuje omezení u kapacity disku.

Mezi dvěma kodeky běžně používaných komprese `gzip` a `snappy`, `gzip` má vyšší poměr kompresi, což vede k nižší využití disku za cenu vyšší zatížení procesoru. `snappy` Kodek poskytuje méně komprese s menšími nároky na procesor. Můžete se rozhodnout, které kodek určený podle omezení zprostředkovatele disku nebo výrobce procesoru. `gzip` dokáže komprimovat data s rychlostí pětkrát větší než `snappy`.

Pomocí komprese dat se zvýší počet záznamů, které můžou být uložená na disku. To může také zvýšit využití procesoru režie v případech, kdy došlo k neshodě mezi formáty komprese výrobce a zprostředkovatele. jako data musí být komprimované před odesláním a potom dekomprimovat před zpracováním.

## <a name="broker-settings"></a>Nastavení služby zprostředkovatele

V následujících částech se zaměřuje na některé z vašich nejdůležitějších nastavení za účelem optimalizace výkonu vaší zprostředkovatelům systému Kafka. Podrobné vysvětlení všech zprostředkovatele nastavení najdete v tématu [dokumentace Apache Kafka producer konfigurace](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Počet disků

Disky úložiště mají omezenou IOPS (vstupně-výstupní operace za sekundu) a pro čtení a zápis bajtů za sekundu. Při vytváření nových oddílů Kafka ukládá každý nový oddíl na disk s co nejmenším existující oddíly k jejich vyrovnání mezi dostupné disky. Bez ohledu na strategii, při zpracování stovky replik oddílů na každém disku Kafka snadno saturate propustnost disku dostupná. Tady kompromis je mezi propustnost a náklady. Pokud vaše aplikace vyžaduje vyšší propustnost, vytvořit cluster s více spravovaných disků na zprostředkovatele. HDInsight aktuálně nepodporuje přidávání spravovaných disků do spuštěného clusteru. Další informace o tom, jak nakonfigurovat počet spravovaných disků najdete v tématu [konfigurace úložiště a škálovatelnosti pro Apache Kafka v HDInsight](apache-kafka-scalability.md). Při nepochopení plného náklady na rostoucí úložného prostoru pro uzly v clusteru.

### <a name="number-of-topics-and-partitions"></a>Počet témat a oddílů

Producenti Kafka zapisovat do témat. Konzumenti Kafka číst z témat. Téma je přidružen k protokolu, který je datová struktura na disku. Kafka přidá záznamy z producentů na konci tématu protokolu. Téma protokol se skládá z mnoha oddílů, které jsou rozdělené do několika souborů. Tyto soubory se zase rozděleny mezi více uzlů v clusteru Kafka. Spotřebitelé číst z témat Kafka v jejich tempo a můžete si vybrat jejich pozice (posun) v protokolu tématu.

Každý oddíl Kafka je soubor protokolu systému a výrobce vláken může zapisovat do protokolů více současně. Podobně vlákno každý příjemce čte zprávy typu z jednoho oddílu, využívání z několika oddílů je zpracovat, protože i paralelně.

Zvýšení hustoty oddílu (počet oddílů jednoho zprostředkovatele) přidává režijní náklady související s operací s metadaty a každý oddíl požadavků/odpovědí mezi vedoucí oddílu a jeho sledujícími. I v případě neexistence dat předávaných replik oddílů přesto načíst data od vedoucích, což má za následek další zpracování pro odesílání a přijímání požadavků v síti.

Pro Apache Kafka clusterů 1.1 a výše v HDInsight, doporučujeme mít maximálně 1000 oddíly na zprostředkovatele, včetně repliky. Zvýšení počtu oddílů jednoho zprostředkovatele snižuje výkon a může také způsobit nedostupnost tématu. Další informace o podpoře oddílů Kafka najdete v tématu [oficiální Apache Kafka blogový příspěvek o zvýšení počtu oddílů podporované ve verzi 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). Podrobnosti o úpravách témata najdete v tématu [Apache Kafka: Úprava témata](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Počet replik

Vyšší faktor replikace za následek další požadavky mezi vedoucí instance oddílu a sledujícími. V důsledku toho vyšší faktor replikace využívá další disk a procesoru zpracovávat další požadavky, zvýšení zápisu latenci a snížení propustnosti.

Doporučujeme použít aspoň 3 x replikace pro systém Kafka v Azure HDInsight. Většině oblastí Azure mají tři domény selhání, ale v oblastech s jenom dvě domény selhání, uživatelé by měli použít 4 x replikace.

Další informace o replikaci najdete v tématu [Apache Kafka: replikace](https://kafka.apache.org/documentation/#replication) a [Apache Kafka: zvýšení faktor replikace](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>Další postup

* [Zpracování bilionů událostí za den pomocí platformy Apache Kafka v Azure](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Co je Apache Kafka v HDInsight?](apache-kafka-introduction.md)
