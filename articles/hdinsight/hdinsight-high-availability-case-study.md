---
title: Případová studie architektury řešení Azure HDInsight s vysokou dostupností
description: Tento článek je fiktivní případovou studii možné architektury řešení Azure HDInsight s vysokou dostupností.
keywords: vysoká dostupnost Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6b995e2ab5ba663f6e33b009062859eb32928cc1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508587"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Případová studie architektury řešení Azure HDInsight s vysokou dostupností

Mechanismy replikace Azure HDInsight je možné integrovat do architektury vysoce dostupného řešení. V tomto článku se fiktivní Případová studie pro maloobchodní prodej používá k vysvětlení možného přístupu k zotavení po havárii s vysokou dostupností, důležitých nákladů a jejich odpovídajících návrhů.

Doporučení pro zotavení po havárii s vysokou dostupností můžou mít spoustu permutací a kombinací. Tato řešení se dodávají při úmyslném objednání specialistů a nevýhody jednotlivých možností. Tento článek popisuje jenom jedno možné řešení.

## <a name="customer-architecture"></a>Architektura zákazníka

Následující obrázek znázorňuje maloobchodní primární architekturu contoso. Tato architektura se skládá z úlohy streamování, úlohy Batch a obsluhy vrstvy, vrstvy spotřeby, vrstvy úložiště a správy verzí.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Maloobchodní architektura společnosti Contoso":::

### <a name="streaming-workload"></a>Zatížení streamování

Zařízení a senzory vytvářejí data do HDInsight Kafka, která tvoří rozhraní pro zasílání zpráv. An HDInsight čtení spotřebitelů Sparku z témat Kafka Spark transformuje příchozí zprávy a zapisuje je do clusteru HDInsight HBA v této vrstvě obsluhy.

### <a name="batch-workload"></a>Dávkové úlohy

An HDInsight podregistr clusteru Hadoop a MapReduce ingestuje data z místních transakčních systémů. Nezpracovaná data transformovaná pomocí podregistru a MapReduce se ukládají do tabulek podregistru v logickém oddílu Data Lake, který se zálohuje Azure Data Lake Storage Gen2. Data uložená v tabulkách podregistru jsou také k dispozici pro Spark SQL, který poskytuje Batch transformaci před uložením dat do adaptérů HBA pro obsluhu.

### <a name="serving-layer"></a>Obslužná vrstva

Cluster An HDInsight HBA s Apache Phoenix slouží k poskytování dat webovým aplikacím a řídicím panelům vizualizací. An HDInsight cluster LLAP se používá ke splnění požadavků na interní generování sestav.

### <a name="consumption-layer"></a>Vrstva spotřeby

Technologie Azure API Apps a API Management vrstev zpátky veřejnou webovou stránku. Požadavky na interní vytváření sestav jsou splněné Power BI.

### <a name="storage-layer"></a>Vrstva úložiště

Logicky dělené Azure Data Lake Storage Gen2 se používají jako Data Lake pro podniky. HDInsight metaúložiště se zálohuje službou Azure SQL DB.

### <a name="version-control-system"></a>Systém správy verzí

Systém správy verzí integrovaný do Azure Pipelines a hostovaný mimo Azure.

## <a name="customer-business-continuity-requirements"></a>Požadavky na provozní kontinuitu zákazníků

Je důležité určit minimální obchodní funkčnost, kterou budete potřebovat v případě havárie.

### <a name="contoso-retails-business-continuity-requirements"></a>Prodejní požadavky na provozní kontinuitu společnosti Contoso

* Musíme být chráněni před problémem regionálního selhání nebo regionálního stavu služeb.
* Zákazníkům se nikdy nezobrazí chyba 404. Veřejný obsah musí být vždy obsluhován. (RTO = 0)  
* Ve většině let můžeme zobrazit veřejný obsah, který je zastaralý po dobu 5 hodin. (RPO = 5 hodin)
* Během svátečních období musí být náš veřejně přístupný obsah vždycky aktuální. (RPO = 0)
* Požadavky na interní vytváření sestav nejsou považovány za kritické pro provozní kontinuitu.
* Optimalizujte náklady na provozní kontinuitu.

## <a name="proposed-solution"></a>Navrhované řešení

Následující obrázek ukazuje architekturu zotavení po havárii s vysokou dostupností v maloobchodní síti společnosti Contoso.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Řešení společnosti Contoso":::

**Kafka** používá [aktivní – pasivní](hdinsight-business-continuity-architecture.md#apache-kafka) replikaci k zrcadlení témat Kafka z primární oblasti do sekundární oblasti. Alternativou k Kafka replikace může být vytváření Kafka v obou oblastech.

**Podregistr a Spark** používají v normálním časech [aktivní primární modely replikace na vyžádání](hdinsight-business-continuity-architecture.md#apache-spark) . Proces replikace podregistru se pravidelně spouští a doprovází podregistry Azure SQL metastore a replikace účtu úložiště pro podregistr. Účet úložiště Spark se pravidelně replikuje pomocí DistCP ADF. Přechodný charakter těchto clusterů pomáhá optimalizovat náklady. Replikace se naplánuje každé 4 hodiny, aby se dostaly do bodu obnovení, který je dobře v rámci požadavku o 5 hodin.

Během normálního časového období replikace **HBA** používá model [následného zpracování](hdinsight-business-continuity-architecture.md#apache-hbase) dat, aby se zajistilo, že data budou vždy obsluhována bez ohledu na oblast a cíl bodu obnovení je velmi nízký.

Pokud v primární oblasti dojde k místnímu selhání, bude webová stránka a obsah back-endu obsluhovány ze sekundární oblasti po dobu 5 hodin s určitou mírou aktuálnosti. Pokud řídicí panel stavu služby Azure neindikuje, že se pro obnovení v rámci pětiletého okna přidá ETA, maloobchodní prodej vytvoří v sekundární oblasti oddíl a transformaci dat Sparku a pak všechny nadřazené zdroje dat nasměruje do sekundární oblasti. Aby se sekundární oblast mohla zapisovat, by došlo k selhání procesu navrácení služeb po obnovení, který zahrnuje replikaci zpátky na primární.

Během nákupní sezóny špičky je celý sekundární kanál vždy aktivní a spuštěný. Kafka výrobci vydávají do obou oblastí a replikace HBA by se změnily z Leader-Follower na Leader-Leader, aby se zajistilo, že veřejný obsah bude vždycky aktuální.

Řešení převzetí služeb při selhání není potřeba navrhovat pro interní vytváření sestav, protože není důležité pro provozní kontinuitu.

## <a name="next-steps"></a>Další kroky

Další informace o položkách, které jsou popsány v tomto článku, najdete v těchto tématech:

* [Provozní kontinuita Azure HDInsight](./hdinsight-business-continuity.md)
* [Architektury Azure HDInsight pro provozní kontinuitu](./hdinsight-business-continuity-architecture.md)
* [Co je Apache Hive a HiveQL ve službě Azure HDInsight?](./hadoop/hdinsight-use-hive.md)
