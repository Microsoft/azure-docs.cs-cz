---
title: 'Výhody: Migrace místního Apache Hadoop do Azure HDInsight'
description: Seznamte se s motivací a výhodami pro migraci místních clusterů Hadoop do Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: 2440b93629416ea73fcf211cbe7bf5a3b72ab2e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74267323"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrace místních clusterů Apache Hadoop do Azure HDInsight – motivace a výhody

Tento článek je první z řady osvědčených postupů pro migraci místních nasazení ekosystému Apache Hadoop do Azure HDInsight. Tato série článků je pro lidi, kteří jsou zodpovědní za návrh, nasazení a migraci řešení Apache Hadoop v Azure HDInsight. Role, které mohou mít prospěch z těchto článků patří cloud architekti, Hadoop správci a DevOps inženýři. Vývojáři softwaru, datoví inženýři a datoví vědci by také měli těžit z vysvětlení, jak fungují různé typy clusterů v cloudu.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Proč migrovat do Azure HDInsightu

Azure HDInsight je cloudová distribuce komponent Hadoop. Azure HDInsight umožňuje snadné, rychlé a nákladově efektivní zpracování obrovského množství dat. HDInsight obsahuje nejoblíbenější open-source architektury, jako jsou:

- Apache Hadoop
- Apache Spark
- Apache Hive s LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Výhody Azure HDInsight oproti místnímu Hadoopu

- **Nízké náklady** – náklady lze snížit [vytvořením clusterů na vyžádání](../hdinsight-hadoop-create-linux-clusters-adf.md) a platbou pouze za to, co používáte. Oddělené výpočetní prostředky a úložiště poskytují flexibilitu tím, že udržují svazek dat nezávislý na velikosti clusteru.

- **Automatické vytváření clusteru** – Automatické vytváření clusteru vyžaduje minimální nastavení a konfiguraci. Automatizace lze použít pro clustery na vyžádání.

- **Spravovaný hardware a konfigurace** – není třeba se starat o fyzický hardware nebo infrastrukturu s clusterem HDInsight. Stačí zadat konfiguraci clusteru a Azure ji nastaví.

- **Snadno škálovatelné** – HDInsight umožňuje [škálovat](../hdinsight-administer-use-portal-linux.md) úlohy nahoru nebo dolů. Azure se postará o redistribuci dat a vyvažování úloh bez přerušení úloh zpracování dat.

- **Globální dostupnost** – HDInsight je k dispozici ve více [oblastech](https://azure.microsoft.com/regions/services/) než jakákoli jiná nabídka analýzy velkých objemů dat. Služba Azure HDInsight je dostupná také pro Azure Government, Čínu a Německo a umožňuje tak splnit požadavky vašeho podniku v klíčových suverénních oblastech.

- **Zabezpečení a kompatibilní** – HDInsight umožňuje chránit prostředky podnikových dat pomocí [virtuální sítě Azure](../hdinsight-plan-virtual-network-deployment.md), šifrování [mašit](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)a integrace se [službou Azure Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight také splňuje nejoblíbenější oborové a vládní [standardy dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud).

- **Zjednodušená správa verzí** – Azure HDInsight spravuje verzi komponent ekosystému Hadoop a udržuje je aktuální. Aktualizace softwaru jsou obvykle složitý proces pro místní nasazení.

- **Menší clustery optimalizované pro konkrétní úlohy s menším počtem závislostí mezi součástmi** – typické místní nastavení Hadoopu používá jeden cluster, který slouží mnoha účelům. S Azure HDInsight lze vytvářet clustery specifické pro úlohy. Vytváření clusterů pro konkrétní úlohy odstraňuje složitost udržování jednoho clusteru s rostoucí složitostí.

- **Produktivita** – Ve vašem preferovaném vývojovém prostředí můžete použít různé nástroje pro Hadoop a Spark.

- **Rozšiřitelnost pomocí vlastních nástrojů nebo aplikací třetích stran** – clustery HDInsight lze rozšířit o nainstalované součásti a lze je také integrovat s ostatními řešeními pro velké objemy dat pomocí nasazení jedním [kliknutím](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) z místa Azure Marketu.

- **Snadná správa, správa a monitorování** – Azure HDInsight se integruje s [protokoly Azure Monitoru](../hdinsight-hadoop-oms-log-analytics-tutorial.md) a poskytuje jediné rozhraní, pomocí kterého můžete monitorovat všechny clustery.

- **Integrace s dalšími službami Azure** – HDInsight lze snadno integrovat s dalšími oblíbenými službami Azure, jako jsou následující:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Samoobslužné procesy a komponenty** - HDInsight neustále kontroluje infrastrukturu a open-source komponenty pomocí vlastní monitorovací infrastruktury. Také automaticky obnoví kritické chyby, jako je například nedostupnost komponent a uzlů s otevřeným zdrojovým kódem. Výstrahy jsou spuštěny v Ambari, pokud se nezdaří některé součásti OSS.

Další informace najdete v článku [Co je Azure HDInsight a apache hadoop technologie zásobníku](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces plánování migrace

Následující kroky se doporučují pro plánování migrace místních clusterů Hadoop do Azure HDInsight:

1. Seznamte se s aktuálním místním nasazením a topologiemi.
2. Seznamte se s aktuálním rozsahem projektu, časovými osami a týmovými odbornými znalostmi.
3. Seznamte se s požadavky Azure.
4. Vytvořte podrobný plán založený na osvědčených postupech.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Shromažďování podrobností pro přípravu na migraci

Tato část obsahuje dotazníky šablon, které vám pomohou shromáždit důležité informace o:

- Místní nasazení
- Podrobnosti o projektu
- Požadavky na Azure

### <a name="on-premises-deployment-questionnaire"></a>Místní dotazník pro nasazení

| **Otázka** | **Příklad** | **Odpověď** |
|---|---|---|
|**Téma**: **Životní prostředí**|||
|Verze distribuce clusteru|HDP 2,6,5, CDH 5,7|
|Komponenty ekologického systému big data|HDFS, Příze, Úl, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Typy clusterů|Hadoop, Jiskra, Confluent Kafka, Bouře, Solr|
|Počet clusterů|4|
|Počet hlavních uzlů|2|
|Počet pracovních uzlů|100|
|Počet hraničních uzlů| 5|
|Celkový počet místa na disku|100 TB|
|Konfigurace hlavního uzlu|m/y, cpu, disk atd.|
|Konfigurace datových uzlů|m/y, cpu, disk atd.|
|Konfigurace hraničních uzlů|m/y, cpu, disk atd.|
|HDFS šifrování?|Ano|
|Vysoká dostupnost|HDFS HA, Metastore HA|
|Zotavení po havárii / zálohování|Záložní cluster?|  
|Systémy, které jsou závislé na clusteru|SQL Server, Teradata, Power BI, MongoDB|
|Integrace třetích stran|Tableau, GridGain, Qubole, Informatika, Splunk|
|**Téma**: **Bezpečnost**|||
|Zabezpečení obvodu|Brány firewall|
|Autorizace & ověřování clusteru|Služba Active Directory, Ambari, Správce cloudery, Bez ověřování|
|Řízení přístupu hdfs|  Manuál, ssh uživatelé|
|Autorizace & ověřování podregistrem|Sentry, LDAP, AD s Kerberos, Ranger|
|Auditování|Ambari, Cloudera Navigátor, Ranger|
|Monitorování|Grafit, sbírat, statsd, Telegraf, PřílivDB|
|Zobrazení výstrah|Kapacitor, Prometheus, Datadog|
|Doba uchovávání dat| 3 roky, 5 let|
|Správci clusteru|Jeden správce, více správců|

### <a name="project-details-questionnaire"></a>Dotazník podrobností projektu

|**Otázka**|**Příklad**|**Odpověď**|
|---|---|---|
|**Téma**: **Pracovní zátěž a četnost**|||
|Úlohy MapReduce|10 pracovních míst - dvakrát denně||
|Pracovní příležitosti pro Hive|100 pracovních míst -- každou hodinu||
|Dávkové úlohy spark|50 úloh -- každých 15 minut||
|Úlohy streamování Spark|5 pracovních míst - každé 3 minuty||
|Úlohy strukturovaného streamování|5 pracovních míst - každou minutu||
|ML Model školení pracovních míst|2 práce - jednou týdně||
|Programovací jazyky|Python, Scala, Java||
|Skriptování|Shell, Python||
|**Téma**: **Data**|||
|Zdroje dat|Ploché soubory, Json, Kafka, RDBMS||
|Orchestrace dat|Pracovní postupy Oozie, Proudění vzduchu||
|Vyhledávání v paměti|Apache Zapalte, Redis||
|Cíle dat|HDFS, RDBMS, Kafka, MPP ||
|**Téma**: **Meta data**|||
|Typ Db úlu|Mysql, Postgres||
|Počet metaobchodů Hive|2||
|Počet tabulek úlu|100||
|Počet zásad rangerů|20||
|Počet pracovních postupů Oozie|100||
|**Téma**: **Měřítko**|||
|Svazek dat včetně replikace|100 TB||
|Denní objem požití|50 GB||
|Rychlost růstu dat|10% ročně||
|Rychlost růstu uzlů clusteru|5% ročně
|**Téma**: **Využití clusteru**|||
|Průměrný použitý počet procesorů|60 %||
|Průměrná využitá paměť %|75 %||
|Využité místo na disku|75 %||
|Průměrná použitá síť %|25 %
|**Téma**: **Zaměstnanci**|||
|Počet správců|2||
|Počet vývojářů|10||
|Počet koncových uživatelů|100||
|Dovednosti|Hadoop, Jiskra||
|Počet dostupných zdrojů pro úsilí o migraci|2||
|**Téma**: **Omezení**|||
|Aktuální omezení|Latence je vysoká||
|Současné výzvy|Problém souběžnosti||

### <a name="azure-requirements-questionnaire"></a>Dotazník požadavků Azure

|**Téma**: **Infrastruktura** |||
|---|---|---|
|**Otázka**|**Příklad**|**Odpověď**|
| Upřednostňovaná oblast|USA – východ||
|Upřednostňována virtuální síť?|Ano||
|HA / DR potřebné?|Ano||
|Integrace s dalšími cloudovými službami?|ADF, CosmosDB||
|**Téma**: **Přesun dat**  |||
|Počáteční předvolba zatížení|DistCp, Datová schránka, ADF, WANDisco||
|Delta přenosu dat|DistCp, AzCopy||
|Probíhající přírůstkový přenos dat|DistCp, Sqoop||
|**Téma**: **Monitorování & upozorňování** |||
|Použití Azure Monitoring & Upozornění Vs Integrace monitorování třetích stran|Použití výstrah y Azure Monitoring &||
|**Téma**: **Bezpečnostní předvolby** |||
|Soukromý a chráněný datový kanál?|Ano||
|Cluster spojený s doménou (ESP)?|     Ano||
|Místní synchronizace služby AD do cloudu?|     Ano||
|Počet uživatelů služby AD k synchronizaci?|          100||
|Ok synchronizovat hesla do cloudu?|    Ano||
|Cloud pouze uživatelé?|                 Ano||
|Potřebujete Vícefaktorové finanční pomoci?|                       Ne|| 
|Požadavky na autorizaci dat?|  Ano||
|Řízení přístupu na základě rolí?|        Ano||
|Je třeba auditovat?|                  Ano||
|Šifrování dat v klidu?|          Ano||
|Šifrování dat při přenosu?|       Ano||
|**Téma**: **Předvolby rearchitektury** |||
|Jeden cluster vs Specifické typy clusterů|Konkrétní typy clusterů||
|Colocated Storage Vs vzdálené úložiště?|Vzdálené úložiště||
|Menší velikost clusteru, protože data jsou uložena vzdáleně?|Menší velikost clusteru||
|Chcete použít více menších clusterů místo jednoho velkého clusteru?|Použití více menších clusterů||
|Použít vzdálený metastore?|Ano||
|Sdílet metaobchody mezi různými clustery?|Ano||
|Dekonstruovat úlohy?|Nahradit úlohy Hive úlohami Spark||
|Použít adf pro orchestraci dat?|Ne||

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii:

- [Doporučené postupy architektury pro místní migraci Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
