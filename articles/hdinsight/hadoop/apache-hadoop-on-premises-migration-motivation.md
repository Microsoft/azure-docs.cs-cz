---
title: 'Výhody: migrace místních Apache Hadoop do Azure HDInsight'
description: Seznamte se s motivací a výhodami migrace místních clusterů Hadoop do Azure HDInsight.
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 975d72df32027888e217d5da9171dba0ba61f257
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943259"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrace místních Apache Hadoop clusterů do Azure HDInsight – motivace a výhody

Tento článek je první v řadě na základě osvědčených postupů pro migraci místních Apache Hadoop nasazení pro ekosystém do Azure HDInsight. Tato série článků je určená lidem, kteří zodpovídají za návrh, nasazení a migraci Apache Hadoop řešení ve službě Azure HDInsight. Role, které můžou tyto články využívat, zahrnují cloudové architekty, správce Hadoop a DevOps technici. Vývojáři softwaru, technici dat a odborníci na data by měli mít také na vysvětlení, jak různé typy clusterů fungují v cloudu.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Proč migrovat do Azure HDInsight

Azure HDInsight je cloudová distribuce komponent systému Hadoop. Azure HDInsight umožňuje snadné, rychlé a nákladově efektivní zpracování obrovského množství dat. HDInsight obsahuje nejoblíbenější Open Source architektury, jako jsou:

- Apache Hadoop
- Apache Spark
- Apache Hive s LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Výhody Azure HDInsight v místní službě Hadoop

- **Nízké náklady** – náklady se můžou snížit [vytvořením clusterů na vyžádání](../hdinsight-hadoop-create-linux-clusters-adf.md) a platíte jenom za to, co využijete. Oddělené výpočetní prostředky a úložiště poskytují flexibilitu tím, že se objem dat nezávisle na velikosti clusteru udržuje.

- **Automatizované vytváření clusterů** – automatizované vytváření clusterů vyžaduje minimální nastavení a konfiguraci. Automatizaci je možné použít pro clustery na vyžádání.

- **Spravovaný hardware a konfigurace** – nemusíte si dělat starosti s fyzickým hardwarem nebo infrastrukturou s clusterem HDInsight. Stačí zadat konfiguraci clusteru a Azure ho nastaví.

- **Snadno škálovatelná** – HDInsight umožňuje [škálovat](../hdinsight-administer-use-portal-linux.md) úlohy nahoru nebo dolů. Azure se stará o redistribuci dat a opětovné vyrovnávání zatížení, aniž by došlo k přerušení úloh zpracování dat.

- **Globální dostupnost** – HDInsight je k dispozici ve více [oblastech](https://azure.microsoft.com/regions/services/) než jakákoli jiná nabídka analýzy velkých objemů dat. Služba Azure HDInsight je dostupná také pro Azure Government, Čínu a Německo a umožňuje tak splnit požadavky vašeho podniku v klíčových suverénních oblastech.

- **Zabezpečené a kompatibilní** – HDInsight umožňuje chránit vaše podnikové datové prostředky pomocí Virtual Network, [šifrování](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)a integrace s [Azure](../hdinsight-plan-virtual-network-deployment.md)pomocí [Azure Active Directory](../domain-joined/hdinsight-security-overview.md). HDInsight také splňuje nejoblíbenější oborové a vládní [standardy dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud).

- **Zjednodušená správa verzí** – Azure HDInsight spravuje verzi součástí pro ekosystém systému Hadoop a udržuje je aktuální. Aktualizace softwaru jsou obvykle složitým procesem pro místní nasazení.

- **Menší clustery optimalizované pro konkrétní úlohy s menšími závislostmi mezi komponentami** – typický místní instalační program Hadoop používá jeden cluster, který slouží k mnoha účelům. S Azure HDInsight je možné vytvářet clustery specifické pro úlohy. Vytváření clusterů pro konkrétní úlohy odstraňuje složitost údržby jednoho clusteru a rostoucí složitost.

- **Produktivita** – v upřednostňovaném vývojovém prostředí můžete použít různé nástroje pro Hadoop a Spark.

- **Rozšiřitelnost pomocí vlastních nástrojů nebo aplikací třetích stran** – clustery HDInsight se dají rozšířit o nainstalované komponenty a dají se také integrovat s dalšími řešeními pro velké objemy dat pomocí nasazení [jedním kliknutím](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) z místa na trhu Azure.

- **Snadná správa, Správa a monitorování** – Azure HDInsight se integruje s [protokoly Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) a poskytuje tak jediné rozhraní, se kterým můžete monitorovat všechny clustery.

- **Integrace s dalšími službami Azure** – HDInsight je možné snadno integrovat s dalšími oblíbenými službami Azure, jako jsou tyto:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Procesy a součásti pro samočinné retušování** – HDInsight nepřetržitě kontroluje infrastruktury a open source komponenty pomocí vlastní monitorovací infrastruktury. Také automaticky obnoví kritické chyby, jako je například nedostupnost open source komponent a uzlů. Výstrahy se aktivují v Ambari, pokud se nějaká součást OSS nezdařila.

Další informace najdete v článku [co je Azure HDInsight a Apache Hadoop Technology Stack](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces plánování migrace

Následující postup se doporučuje pro plánování migrace místních clusterů Hadoop do Azure HDInsight:

1. Pochopte aktuální místní nasazení a topologie.
2. Pochopení aktuálního rozsahu projektu, časových os a odbornosti týmu.
3. Pochopení požadavků Azure.
4. Sestavte podrobný plán na základě osvědčených postupů.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Shromažďování podrobností pro přípravu na migraci

V této části jsou uvedeny dotazníky šablon, které vám pomůžou shromáždit důležité informace o:

- Místní nasazení
- Podrobnosti o projektu
- Požadavky na Azure

### <a name="on-premises-deployment-questionnaire"></a>Dotazník místního nasazení

| **Otázka** | **Příklad** | **Odpověď** |
|---|---|---|
|**Téma**: **prostředí**|||
|Verze distribuce clusteru|HDP 2.6.5, CDH – 5,7|
|Součásti pro ekosystém velkých objemů dat|HDFS, příze, podregistr, LLAP, Impala, Kudu, HBA, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, Atlas, Falcon, Zeppelin, R|
|Typy clusterů|Hadoop, Spark, Kafka, zaplavení, Solr|
|Počet clusterů|4|
|Počet hlavních uzlů|2|
|Počet uzlů pracovního procesu|100|
|Počet hraničních uzlů| 5|
|Celkové místo na disku|100 TB|
|Konfigurace hlavního uzlu|m/y, CPU, disk atd.|
|Konfigurace datových uzlů|m/y, CPU, disk atd.|
|Konfigurace hraničních uzlů|m/y, CPU, disk atd.|
|Šifrování HDFS?|Yes|
|Vysoká dostupnost|HDFS HA, metastore HA|
|Zotavení po havárii/zálohování|Zálohovat cluster?|  
|Systémy, které jsou závislé na clusteru|SQL Server, Teradata, Power BI, MongoDB|
|Integrace třetích stran|Tableau, GridGain, Qubole, Informatica, Splunk|
|**Téma**: **zabezpečení**|||
|Zabezpečení hraničních|brány firewall,|
|Ověřování clusteru & autorizaci|Active Directory, Ambari, Cloudera Manager, bez ověřování|
|HDFS – Access Control|  Ruční, uživatelé SSH|
|Ověřování podregistru & autorizaci|Sentry, LDAP, AD s protokolem Kerberos, Ranger|
|Auditování|Ambari, Cloudera Navigator, Ranger|
|Monitorování|Grafit, Collect, statd, telegraf, InfluxDB|
|Zobrazení výstrah|Kapacitor, Prometheus, služby Datadog|
|Doba uchovávání dat| 3 roky, 5 let|
|Správci clusteru|Jeden správce, více správců|

### <a name="project-details-questionnaire"></a>Dotazník podrobností projektu

|**Otázka**|**Příklad**|**Odpověď**|
|---|---|---|
|**Téma**: **úlohy a frekvence**|||
|MapReduce úlohy|10 úloh – dvakrát denně||
|Úlohy podregistru|úlohy 100 – každou hodinu||
|Dávkové úlohy Spark|úlohy 50 – každých 15 minut||
|Úlohy streamování Sparku|5 úloh – každé 3 minuty||
|Strukturované úlohy streamování|5 úloh – každou minutu||
|Školicí úlohy modelů ML|2 úlohy--jednou za týden||
|Programovací jazyky|Python, Scala, Java||
|Skriptování|Prostředí, Python||
|**Téma**: **data**|||
|Zdroje dat|Ploché soubory, JSON, Kafka, RDBMS||
|Orchestrace dat|Oozie pracovní postupy, tok||
|V hledání paměti|Apache Ignite, Redis||
|Cíle dat|HDFS, RDBMS, Kafka, MPP ||
|**Téma**: **meta data**|||
|Typ databáze typu podregistr|MySQL, Postgres||
|Počet metaúložiště podregistru|2||
|Počet tabulek podregistru|100||
|Počet zásad Ranger|20||
|Počet pracovních postupů Oozie|100||
|**Téma**: **škálování**|||
|Objem dat včetně replikace|100 TB||
|Objem denního příjmu|50 GB||
|Míra nárůstu dat|10% za rok||
|Míra růstu uzlů clusteru|5% za rok
|**Téma**: **využití clusteru**|||
|Průměrné využití procesoru v%|60 %||
|Průměrná doba využití paměti%|75%||
|Využité místo na disku|75%||
|Průměrná doba využití sítě%|25%
|**Téma**: **zaměstnanci**|||
|Počet správců|2||
|Počet vývojářů|10||
|Počet koncových uživatelů|100||
|Dovednosti|Hadoop, Spark||
|Počet dostupných prostředků pro účely migrace|2||
|**Téma**: **omezení**|||
|Aktuální omezení|Latence je vysoká.||
|Aktuální výzvy|Problém souběžnosti||

### <a name="azure-requirements-questionnaire"></a>Dotazník požadavků Azure

|**Otázka**|**Příklad**|**Odpověď**|
|---|---|---|
|**Téma**: **infrastruktura** |||
| Upřednostňovaná oblast|USA – východ||
|Upřednostňovaná virtuální síť?|Yes||
|Je potřeba HA/DR?|Yes||
|Integraci s dalšími Cloud Services?|ADF, CosmosDB||
|**Téma**:   **přesun dat**  |||
|Předvolba počátečního zatížení|DistCp, data box, ADF, WANDisco||
|Rozdíl přenosu dat|DistCp, AzCopy||
|Pokračující přírůstkový přenos dat|DistCp, Sqoop||
|**Téma**:   **monitorování výstrah &** |||
|Použití monitorování Azure & upozorňování a integrace monitorování třetích stran|Použití upozorňování & monitorování Azure||
|**Téma**:   **Předvolby zabezpečení** |||
|Privátní a chráněný datový kanál?|Yes||
|Cluster připojený k doméně (ESP)?|     Yes||
|Místní AD Sync ke cloudu?|     Yes||
|Počet uživatelů služby AD, které se mají synchronizovat?|          100||
|Chcete synchronizovat hesla do cloudu?|    Yes||
|Jenom uživatelé cloudu?|                 Yes||
|Je potřeba MFA?|                       No|| 
|Požadavky na autorizaci dat?|  Yes||
|Řízení přístupu na základě role?|        Yes||
|Je vyžadováno auditování?|                  Yes||
|Šifrování dat v klidovém umístění?|          Yes||
|Šifrování dat při přenosu?|       Yes||
|**Téma**:   **Předvolby opětovné architektury** |||
|Jeden cluster vs konkrétní typy clusterů|Konkrétní typy clusterů||
|Společně umístěné úložiště vs – vzdálené úložiště?|Vzdálené úložiště||
|Menší velikost clusteru jako data se ukládají vzdáleně?|Menší velikost clusteru||
|Použít více menších clusterů místo jednoho velkého clusteru?|Použití více menších clusterů||
|Používáte vzdálenou metastore?|Yes||
|Sdílet metaúložiště mezi různými clustery?|Yes||
|Dekonstruovat úlohy?|Výměna úloh podregistru pomocí úloh Spark||
|Chcete použít ADF pro orchestraci dat?|No||

## <a name="next-steps"></a>Další kroky

Přečtěte si další článek v této sérii:

- [Osvědčené postupy architektury pro migraci z místního prostředí do Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
