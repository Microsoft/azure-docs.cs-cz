---
title: Architektury Azure HDInsight pro provozní kontinuitu
description: Tento článek popisuje různé možné architektury podnikového kontinuity pro HDInsight.
keywords: vysoká dostupnost Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f74309370f1489714013344116e7feb9551fbfd5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933445"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Architektury Azure HDInsight pro provozní kontinuitu

Tento článek obsahuje několik příkladů architektur provozní kontinuity, které můžete zvážit pro Azure HDInsight. Tolerance pro omezenou funkčnost během havárie je obchodní rozhodnutí, které se liší od jedné aplikace po další. Může být přijatelné, aby některé aplikace byly nedostupné nebo byly částečně dostupné s omezenou funkčností nebo opožděným zpracováním za určitou dobu. U ostatních aplikací může být možné nepřijmout jakékoli omezené funkce.

> [!NOTE]
> Architektury prezentované v tomto článku nejsou nijak vyčerpávající. Když jste provedli objektivní určení s očekávanou provozní kontinuitou, složitostí provozu a náklady na vlastnictví, měli byste navrhovat vlastní jedinečné architektury.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive a interaktivní dotaz

[Replikace podregistru v2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) se doporučuje pro zajištění kontinuity podnikových prostředí v podregistru HDInsight a v clusterech interaktivních dotazů. Trvalé oddíly samostatného clusteru podregistru, které je potřeba replikovat, jsou vrstvou úložiště a metastore Hive. Clustery podregistru ve scénáři s více uživateli, které Balíček zabezpečení podniku potřebují Azure Active Directory Domain Services a Ranger metastore.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Architektura podregistru a interaktivních dotazů":::

Replikace na základě událostí podregistru je nakonfigurovaná mezi primárním a sekundárním clusterem. Skládá se ze dvou různých fází, spouštěcích a přírůstkových spuštění:

* Zavedení replikuje celý sklad podregistru, včetně metastore Hivech informací z primárního do sekundárního.

* Přírůstkové běhy jsou v primárním clusteru automatizované a události vygenerované během přírůstkových běhů se přehrávají v sekundárním clusteru. Sekundární cluster se zachytí s událostmi generovanými z primárního clusteru a zajišťuje, aby byl sekundární cluster konzistentní s událostmi primárního clusteru po spuštění replikace.

Sekundární cluster se potřebuje jenom v době, kdy replikace spouští distribuovanou kopii, `DistCp` ale úložiště a metaúložiště musí být trvalé. Můžete si vybrat, že se má na vyžádání před replikací aktivovat skriptový sekundární cluster, spustit replikační skript a po úspěšné replikaci ho vyvěsit.

Sekundární cluster je obvykle jen pro čtení. Sekundární cluster můžete nastavit pro čtení i zápis, ale tím se přidá další složitost, která zahrnuje replikaci změn ze sekundárního clusteru do primárního clusteru.

### <a name="hive-event-based-replication-rpo--rto"></a>Obnovení replikace na základě událostí z podregistru RPO & RTO

* RPO: ztráta dat je omezená na poslední úspěšnou událost přírůstkové replikace z primární na sekundární.

* RTO: čas mezi selháním a obnovením transakcí nadřazených a podřízených transakcí se sekundárním serverem.

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive a interaktivní architektury dotazů

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Aktivní primární podregistr s sekundární na vyžádání

V *aktivní primární službě s sekundární architekturou na vyžádání* aplikace zapisují do aktivní primární oblasti, zatímco v sekundární oblasti nejsou během normálního provozu zřízeny žádné clustery. SQL metastore a Storage v sekundární oblasti jsou trvalé, zatímco cluster HDInsight je spouštěný a nasazený na vyžádání pouze před spuštěním naplánované replikace podregistru.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="aktivní primární s sekundárním na vyžádání":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Aktivní primární podregistr s sekundárním pohotovostním režimem

V *aktivní primární primární službě s pohotovostním sekundárním* prostředím aplikace zapisují do aktivní primární oblasti a v režimu jen pro čtení se spustí v režimu jen pro čtení během normálního provozu. Během normálního provozu můžete zvolit přesměrování operací čtení specifických pro oblast do sekundárního.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="aktivní primární s sekundárním pohotovostním režimem":::

Další informace o replikaci podregistru a ukázkách kódu najdete [v tématu Apache Hive replikace v clusterech Azure HDInsight](./interactive-query/apache-hive-replication.md) .

## <a name="apache-spark"></a>Apache Spark

Úlohy Sparku mohou nebo nemusí zahrnovat komponentu podregistru. Pokud chcete, aby úlohy Spark SQL mohly číst a zapisovat data z podregistru, clustery HDInsight Spark sdílejí vlastní metaúložiště podregistru z clusterů a interaktivních dotazových dotazů ve stejné oblasti. V takových scénářích musí být replikace mezi jednotlivými úlohami Spark také doprovázet replikaci metaúložiště a úložiště podoblasti. Scénáře převzetí služeb při selhání v této části platí pro:

* [Spark SQL na tabulkách s kyselým použitím konektoru pro umožní (HDInsight Warehouse Connector)](./interactive-query/apache-hive-warehouse-connector.md) pomocí clusteru HDInsight Interactive Query.
* Úlohy Spark SQL v tabulkách bez KYSELosti pomocí clusteru HDInsight Hadoop.

V případě scénářů, kde Spark funguje v samostatném režimu, je potřeba replikovat data a uložená Spark jar (pro úlohy Livy) z primární oblasti do sekundární oblasti, a to v pravidelných intervalech, a to pomocí Azure Data Factory `DistCP` .

Doporučujeme používat systémy správy verzí k ukládání notebooků a knihoven Spark, kde je lze snadno nasadit v primárních nebo sekundárních clusterech. Zajistěte, aby v primárním nebo sekundárním pracovním prostoru byly připraveny řešení založená na poznámkovém bloku a jiná než poznámková centra pro načtení správných připojení dat.

Pokud existují konkrétní zákaznické knihovny, které jsou nad rámec toho, co HDInsight poskytuje nativně, musí být sledovány a pravidelně načteny do záložního sekundárního clusteru.  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark RPO & RTO replikace

* RPO: ztráta dat je omezená na poslední úspěšnou přírůstkovou replikaci (Spark a podregistr) z primárního na sekundární.

* RTO: čas mezi selháním a obnovením transakcí nadřazených a podřízených transakcí se sekundárním serverem.

### <a name="apache-spark-architectures"></a>Apache Spark architektury

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Spark – aktivní primární s využitím sekundárního na vyžádání

Aplikace čtou a zapisují do clusterů Spark a podregistr v primární oblasti, zatímco během normálního provozu nejsou v sekundární oblasti zřízeny žádné clustery. SQL metastore, úložiště podregistru a Sparkové úložiště jsou v sekundární oblasti trvalé. Clustery Spark a podregistr se skriptují a nasazují na vyžádání. Replikace podregistru se používá k replikaci úložiště podregistru a metaúložištěí podregistru, zatímco Azure Data Factory je `DistCP` možné použít ke kopírování samostatného úložiště Spark. Clustery podregistrů je potřeba nasadit před každým spuštěním replikace každého podregistru kvůli `DistCp` výpočetnímu prostředí závislosti.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="aktivní primární s architekturou sekundární Apache Spark na vyžádání":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Spark – aktivní primární s pohotovostním sekundárním

Aplikace čtou a zapisují do clusterů Spark a podregistr v primární oblasti a v pohotovostním režimu se v režimu jen pro čtení spouští v sekundární oblasti během normálního provozu. Během normálního provozu se můžete rozhodnout, že chcete přesměrovat podregistry specifické pro oblast a operace čtení Sparku na sekundární.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="aktivní primární pohotovostní sekundární Apache Spark ":::

## <a name="apache-hbase"></a>Apache HBase

Replikace při exportu a HBA jsou běžné způsoby povolování kontinuity podnikových prostředí mezi clustery HDInsight.

Export HBA je proces dávkové replikace, který používá nástroj pro export HBA k exportu tabulek z primárních clusterů HBA do svého základního Azure Data Lake Storage úložiště 2. generace. Exportovaná data jsou následně dostupná z clusteru sekundárních HBA a naimportovaná do tabulek, které musí existovat v sekundárním rozhraní. I když adaptéry pro export nabízí členitost na úrovni tabulky, v situacích přírůstkové aktualizace řídí modul automatizace exportu rozsah přírůstkových řádků, které se mají zahrnout do každého spuštění. Další informace najdete v tématu [zálohování a replikace HDInsight HBA](./hbase/apache-hbase-backup-replication.md#export-then-import).

HBA replikace v rámci plně automatizovaného způsobu používá replikaci téměř v reálném čase mezi clustery HBA. Replikace se provádí na úrovni tabulky. Všechny tabulky nebo konkrétní tabulky můžou být cílené na replikaci. Replikace HBA je nakonec konzistentní, což znamená, že nedávné úpravy tabulky v primární oblasti nemusí být k dispozici všem sekundárním objektům okamžitě. Sekundárním způsobem je zaručeno, že primárním způsobem bude konzistentní. Replikace HBA je možné nastavit mezi dvěma nebo více clustery HDInsight HBA, pokud:

* Primární a sekundární je ve stejné virtuální síti.
* Primární a sekundární jsou v různých virtuální sítě partnerských uzlů ve stejné oblasti.
* Primární a sekundární jsou v různých virtuální sítě partnerských uzlů v různých oblastech.

Další informace najdete v tématu [nastavení replikace clusteru Apache HBA v Azure Virtual Networks](./hbase/apache-hbase-replication.md).

Existuje několik dalších způsobů, jak provádět zálohování clusterů HBA, jako je [kopírování složky HBA](./hbase/apache-hbase-backup-replication.md#copy-the-hbase-folder), [kopírování tabulek](./hbase/apache-hbase-backup-replication.md#copy-tables) a [snímků](./hbase/apache-hbase-backup-replication.md#snapshots).

### <a name="hbase-rpo--rto"></a>HBA – RPO & RTO

#### <a name="hbase-export"></a>Export HBA

* RPO: ztráta dat je omezená na poslední úspěšný dávkový přírůstkový import ze sekundární databáze z primární.
* RTO: čas mezi selháním primárního a obnovením vstupně-výstupních operací na sekundárním počítači.

#### <a name="hbase-replication"></a>Replikace HBA

* RPO: ztráta dat je omezená na poslední expedici WalEdit přijatou na sekundárním místě.
* RTO: čas mezi selháním primárního a obnovením vstupně-výstupních operací na sekundárním počítači.

### <a name="hbase-architectures"></a>Architektury HBA

Replikace HBA je možné nastavit ve třech režimech: vedoucí a následný Leader-Leader a cyklicky.

#### <a name="hbase-replication--leader--follower-model"></a>HBA – replikace: model následného typu

V této nastavení mezi různými oblastmi je replikace jednosměrná od primární oblasti do sekundární oblasti. Pro jednosměrnou replikaci je možné identifikovat buď všechny tabulky nebo konkrétní tabulky na primárním počítači. Během normálního provozu se sekundární cluster dá použít k obsluze požadavků na čtení ve své vlastní oblasti.

Sekundární cluster funguje jako normální cluster HBA, který může hostovat vlastní tabulky a může obsluhovat čtení a zápisy z místních aplikací. Zápisy do replikovaných tabulek nebo tabulek, které jsou nativní pro sekundární, však nebudou replikovány zpět na primární.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="Model následného vedoucího procesu HBA":::

#### <a name="hbase-replication--leader--leader-model"></a>Replikace HBA: vedoucí – model vedoucího procesu

Tato nastavení pro různé oblasti jsou velmi podobná jednosměrovém nastavení s tím rozdílem, že replikace probíhá obousměrně mezi primární oblastí a sekundární oblastí. Aplikace můžou používat oba clustery ve čtení – režimy zápisu a aktualizace jsou asynchronně vyměňovanými mezi nimi.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="Model vedoucího procesu HBA":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>Replikace HBA: více oblastí nebo cyklických

Model více oblastí/cyklických replikací je rozšířením replikace HBA a dá se použít k vytvoření globálně redundantní architektury HBA s více aplikacemi, které čtou a zapisují do clusterů specifických pro jednotlivé oblasti. Clustery je možné nastavit v různých kombinacích vedoucí/vedoucího nebo vedoucího/následného procesu v závislosti na obchodních požadavcích.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="Cyklický model HBA":::

## <a name="apache-kafka"></a>Apache Kafka

Pokud chcete povolit dostupnost mezi oblastmi, HDInsight 4,0 podporuje Kafka nástroje MirrorMaker, které se dají použít k údržbě sekundární repliky primárního clusteru Kafka v jiné oblasti. Nástroje MirrorMaker funguje jako dvojice spotřebitelů v nejvyšší úrovni, spotřebovává konkrétní téma v primárním clusteru a vytvoří téma se stejným názvem v sekundárním poli. Replikace mezi clustery pro zotavení po havárii s vysokou dostupností pomocí nástroje MirrorMaker je dodávána s předpokladem, že producenti a spotřebitelé musí převzít služby při selhání do clusteru repliky. Další informace najdete v tématu [použití nástroje MirrorMaker k replikaci Apache Kafkach témat s Kafka v HDInsight](./kafka/apache-kafka-mirroring.md) .

V závislosti na době, kdy byla replikace zahájena, může replikace tématu nástroje MirrorMaker vést k různým posunům mezi zdroji a tématy repliky. Clustery HDInsight Kafka také podporují replikaci oddílu tématu, což je funkce vysoké dostupnosti na úrovni jednotlivých clusterů.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Replikace Apache Kafka":::

### <a name="apache-kafka-architectures"></a>Apache Kafka architektury

#### <a name="kafka-replication-active--passive"></a>Kafka replikace: aktivní – pasivní

Instalace Active-Passive umožňuje asynchronní jednosměrné zrcadlení z aktivní na pasivní. Producenti a spotřebitelé si musí být vědomi existence aktivního a pasivního clusteru a musí být připraveni k převzetí služeb při selhání pasivní pro případ, že aktivní selže. Níže jsou některé výhody a nevýhody instalace Active-Passive.

Výhody:

* Latence sítě mezi clustery nemá vliv na výkon aktivního clusteru.
* Jednoduchost jednosměrné replikace.

Nevýhody:

* Pasivní cluster může zůstat nevyužitý.
* Složitosti při obpři převzetí služeb při selhání v producentech a spotřebiteli aplikací
* Může dojít ke ztrátě dat během selhání aktivního clusteru.
* Konečná konzistence mezi tématy mezi aktivními a pasivními clustery.
* Failbacks na primární může vést k nekonzistenci zpráv v tématech.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Apache Kafka aktivní pasivní model":::

#### <a name="kafka-replication-active--active"></a>Replikace Kafka: aktivní – aktivní

Nastavení Active-Active zahrnuje dva rozpracované clustery HDInsight Kafka s více uzly s obousměrnou asynchronní replikací s nástroje MirrorMaker. V tomto návrhu jsou k dispozici také zprávy spotřebované spotřebiteli v primárním i opačném případě. Níže jsou některé výhody a nevýhody instalace Active-Active.

Výhody:

* Kvůli jejich duplicitnímu stavu se převzetí služeb při selhání a failbacks snadněji spustí.

Nevýhody:

* Nastavení, Správa a monitorování jsou složitější než aktivní – pasivní.
* Problém cyklické replikace je potřeba vyřešit.  
* Obousměrná replikace vede k vyšším nákladům na výstup pro místní datové přenosy.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Apache Kafka aktivní aktivní model":::

## <a name="hdinsight-enterprise-security-package"></a>Balíček zabezpečení podniku HDInsight

Toto nastavení se používá k povolení funkcí více uživatelů v primární i sekundární službě a také k [sadám služba AD DS služby repliky Azure](../active-directory-domain-services/tutorial-create-replica-set.md) , aby bylo zajištěno, že se uživatelé budou moct ověřovat v obou clusterech. Během normálního provozu je potřeba nastavit zásady Ranger v sekundárním, aby bylo zajištěno, že uživatelé budou omezeni na operace čtení. Níže uvedená architektura vysvětluje, jak může vypadat aktivní primární podregistr s povoleným protokolem ESP – sekundární nastavení.

Replikace Ranger metastore:

Ranger metastore se používá k trvalému ukládání a obsluze zásad Ranger pro řízení autorizací dat. Doporučujeme, abyste zachovali nezávislé zásady Ranger v primárních a sekundárních a zachovali sekundární jako repliku pro čtení.
  
Pokud vyžadujete, aby se zásady Ranger synchronizovaly mezi primárními a sekundárními verzemi, použijte [Ranger import/export](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export) k pravidelnému zálohování a importu zásad Ranger z primární na sekundární.

Replikace zásad Ranger mezi primárním a sekundárním objektem může způsobit, že sekundárnímu modulu bude povolen zápis, což může vést k neúmyslným zápisům na sekundární navýšení nekonzistence dat.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="Architektura Balíček zabezpečení podniku HDInsight":::

## <a name="next-steps"></a>Další kroky

Další informace o položkách, které jsou popsány v tomto článku, najdete v těchto tématech:

* [Provozní kontinuita Azure HDInsight](./hdinsight-business-continuity.md)
* [Případová studie architektury řešení Azure HDInsight s vysokou dostupností](./hdinsight-high-availability-case-study.md)
* [Co je Apache Hive a HiveQL ve službě Azure HDInsight?](./hadoop/hdinsight-use-hive.md)