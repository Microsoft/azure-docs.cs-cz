---
title: Migrace místních Apache Hadoop clusterů Azure HDInsight – výhody a motivace
description: Informace o motivace a výhody pro migrace místních Hadoop clusterů pro Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 94dec611a04819580696133c48db66da1ea9c463
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000438"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrace místních Apache Hadoop clusterů Azure HDInsight – výhody a motivace

Tento článek je první z řady na osvědčené postupy pro migraci v místním nasazení ekosystému Apache Hadoop do Azure HDInsight. Tato série článků je pro uživatele, kteří odpovídají za návrh, nasazení a migrace řešení Apache Hadoop v Azure HDInsight. Role, které můžou mít užitek z těchto článků zahrnují cloudové architekty, správci systému Hadoop a technici DevOps. Vývojáři softwaru, datovými architekty a datovými vědci by měl také využívat vysvětlení jak různých typů činnosti clusterů v cloudu.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Důvod, proč k migraci do Azure HDInsight

Azure HDInsight je Cloudová distribuce komponent Hadoop z [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). Azure HDInsight umožňuje snadné, rychlé a nákladově efektivní zpracování obrovského množství dat. HDInsight obsahuje nejoblíbenější opensourcové architektury, jako například:

- Apache Hadoop
- Apache Spark
- Apache Hive s funkcí LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="azure-hdinsight-advantages-over-on-premises-hadoop"></a>Azure HDInsight výhod oproti místního clusteru Hadoop

- **Nízkonákladové** -lze snížit náklady na [vytváření clusterů na vyžádání](../hdinsight-hadoop-create-linux-clusters-adf.md) a platit jenom za využité. Oddělený výpočetní výkon a úložiště nabízí flexibilitu udržováním objem dat nezávislé na velikosti clusteru.

- **Automatizované vytváření clusterů** – automatizované vytváření clusteru vyžaduje minimální instalaci a konfiguraci. Automation je možné pro clustery na vyžádání.

- **Spravovat hardware a konfiguraci** – není nutné se starat o fyzický hardware nebo infrastrukturu s clusterem HDInsight. Stačí zadat konfiguraci clusteru, a Azure nastaví ho.

- **Snadná škálovatelnost** – HDInsight umožňuje [škálování](../hdinsight-administer-use-portal-linux.md) úlohy směrem nahoru nebo dolů. Úloha opětovné vyvážení bez přerušení úloh zpracování dat a distribuci dat postará Azure.

- **Globální dostupnost** – HDInsight je dostupná ve více [oblastech](https://azure.microsoft.com/regions/services/) než všechny ostatní nabídka analýz velkých objemů dat. Služba Azure HDInsight je dostupná také pro Azure Government, Čínu a Německo a umožňuje tak splnit požadavky vašeho podniku v klíčových suverénních oblastech.

- **Zabezpečení a dodržování** – HDInsight umožňuje chránit datové prostředky vašeho podniku pomocí [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [šifrování](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)a integraci s [Azure Active Adresář](../domain-joined/apache-domain-joined-introduction.md). HDInsight také splňuje nejoblíbenější oborové a vládní [standardy pro dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud).

- **Zjednodušená správa verzí** – Azure HDInsight verze součástí ekosystému Hadoop spravuje a udržuje je aktuální. Aktualizace softwaru jsou obvykle složitý proces pro místní nasazení.

- **Menší clustery optimalizované pro určité úlohy s méně závislosti mezi komponentami** – instalační program systému Hadoop typický místní používal jeden cluster, který slouží různým účelům. S Azure HDInsight je možné vytvářet clustery určeného pro konkrétní úlohy. Vytváření clusterů pro konkrétní úlohy odebere vyžadovalo udržování jeden cluster s rostoucí složitostí.

- **Zvýšení produktivity** – můžete použít různé nástroje pro Hadoop a Spark v upřednostňovaných vývojových prostředích.

- **Rozšíření s vlastní nástroje nebo aplikace jiných výrobců** – clustery HDInsight je možné rozšířit pomocí nainstalovaných komponent a je také možné integrovat s jinými řešeními pro velké objemy dat s využitím [jedním kliknutím](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  nasazení z Azure Market place.

- **Snadná správa, Správa a monitorování** – Azure HDInsight se integruje s [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) a poskytuje jednotné rozhraní, pomocí kterého můžete monitorování všech vašich clusterů.

- **Integrace s dalšími službami Azure** – HDInsight je možné snadno integrovat s dalšími oblíbenými službami Azure jako je následující:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Opravy procesů a komponent** – HDInsight neustále kontroluje infrastruktury a open source komponenty pomocí vlastní monitorování infrastruktury. Kritické chyby, jako je například nedostupnost opensourcové komponenty a uzly také automaticky obnoví. V Ambari se aktivují upozornění, pokud libovolné součásti OSS, se nezdařilo.

Další informace najdete v článku [co je Azure HDInsight a technologie Apache hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces plánování migrace

Následující kroky se doporučují pro plánování migrace místních Hadoop clusterů pro Azure HDInsight:

1. Zjistěte aktuální místní nasazení a topologie.
2. Zjistěte aktuální rozsahu projektu, časové osy a odborných znalostí týmu.
3. Zjistěte požadavky pro Azure.
4. Vytvářejte si podrobný plán na základě osvědčených postupů.

## <a name="gathering-details-to-prepare-for-a-migration"></a>Shromažďování podrobností přípravy na migraci

Tato část obsahuje šablony dotazníky umožňující získat důležité informace o:

- Místní nasazení
- Podrobnosti o projektu
- Požadavky na Azure

### <a name="on-premises-deployment-questionnaire"></a>Dotazník pro místní nasazení

| **Dotaz** | **Příklad** | **Odpověď** |
|---|---|---|
|**Téma**: **prostředí**|||
|Typ clusteru distribuce|Hortonworks, Cloudera, MapR| |
|Verze clusteru distribuce|HDP 2.6.5 CDH – 5.7|
|Komponenty ekosystému velkých objemů dat|HDFS, Yarn, Hive, LLAP, Impala, Kudu, HBase, Spark, MapReduce, Kafka, Zookeeper, Solr, Sqoop, Oozie, Ranger, střední, Falcon, Zeppelin, R|
|Typy clusterů|Hadoop, Spark, nesrostlé Kafka, Storm, Solr|
|Počet clusterů|4|
|Číslo hlavní uzly|2|
|Počet uzlů pracovního procesu|100|
|Počet hraničních uzlů| 5|
|Celkové místo na disku|100 TB|
|Konfigurace hlavního uzlu|min nebo y, procesoru, disku atd.|
|Konfigurace datové uzly|min nebo y, procesoru, disku atd.|
|Hraniční uzly konfigurace|min nebo y, procesoru, disku atd.|
|HDFS šifrování?|Ano|
|Vysoká dostupnost|HA HDFS, Metastore HA|
|Zotavení po havárii / zálohování|Zálohování clusteru?|  
|Systémy, které jsou závislé na clusteru|SQL Server, Teradata, Power BI, MongoDB|
|Integrace třetích stran|Tableau GridGain Qubole, Informatica, Splunk|
|**Téma**: **zabezpečení**|||
|Zabezpečení perimetru|Brány firewall|
|Cluster ověřování a autorizace|Active Directory, Ambari, Cloudera správce, bez ověřování|
|Řízení přístupu HDFS|  Ruční, ssh uživatelů|
|Hive ověřování a autorizace|SENTRY, LDAP, Kerberos, Ranger AD|
|Auditování|Ambari, Cloudera Navigátor Ranger|
|Monitorování|Grafitová, shromážděná, statsd, Telegraf, InfluxDB|
|Zobrazení výstrah|Kapacitor Prometheus, služby Datadog|
|Doba uchování dat| 3 roky, 5 let.|
|Správce clusteru|Jeden správce, více správců|

### <a name="project-details-questionnaire"></a>Dotazník podrobností projektu

|**Dotaz**|**Příklad**|**Odpověď**|
|---|---|---|
|**Téma**: **úloh a četnost**|||
|Úlohy MapReduce|10 úlohy--dvakrát denně||
|Úlohy Hive|100 úloh – každou hodinu||
|Sparkových úloh služby batch|50 úlohy--každých 15 minut||
|Úlohy Spark Streaming|5 úloh – každé 3 minuty||
|Strukturované streamování úlohy|5 úlohy--každou minutu||
|Úlohy trénování modelů ML|2 úlohy--jednou za týden||
|Programovací jazyky|Python, Scala, Java||
|Skriptování|Prostředí Pythonu||
|**Téma**: **dat**|||
|Zdroje dat|Ploché soubory Json, Kafka, relační databázový systém||
|Orchestrace dat|Pracovní postupy Oozie, vzduchu||
|V paměti pro vyhledávání|Apache ke konferenci Ignite, redis Cache||
|Cíle dat|HDFS, relační databázový systém, Kafka, MPP ||
|**Téma**: **metadat**|||
|Typ databáze Hive|MySQL, Postgres||
|Ne. z metaúložiště Hive|2||
|Ne. tabulek Hive|100||
|Ne. zásad Ranger|20||
|Ne. Oozie pracovních postupů|100||
|**Téma**: **škálování**|||
|Objem dat, včetně replikace|100 TB||
|Denní objem příjmu|50 GB||
|Míry růstu dat|10 % za rok||
|Míra růstu uzlů clusteru|% 5, ročně
|**Téma**: **clusteru využití**|||
|Průměrné využití procesoru % využití|60%||
|Průměrná paměť % využití|75 %||
|Využité místo na disku|75 %||
|Průměrná sítě % využití|25 %
|**Téma**: **zaměstnanců**|||
|Ne. správců|2||
|Ne. vývojářů|10||
|Ne. koncových uživatelů|100||
|Dovednosti|Hadoop, Spark||
|Ne. z dostupných prostředků pro účely migrace|2||
|**Téma**: **omezení**|||
|Aktuální omezení|Latence je vysoká.||
|Aktuální problémy|Problém souběžnosti||

### <a name="azure-requirements-questionnaire"></a>Dotazník požadavky pro Azure

|**Téma**: **infrastruktury** |||
|---|---|---|
|**Dotaz**|**Příklad**|**Odpověď**|
| Preferované oblasti|USA – východ||
|Upřednostňované sítě VNet?|Ano||
|HA / DR potřeby?|Ano||
|Integrace s jinými cloudovými službami?|ADF, služby cosmos DB||
|**Téma**: **přesun dat**  |||
|Předvolby počátečním načtení|DistCp, Data box, ADF, WANDisco||
|Rozdílová data přenosu|DistCp, AzCopy||
|Průběžné přírůstkové datové přenosy|DistCp, Sqoop||
|**Téma**: **Monitoring a Alerting** |||
|Použití Azure Monitoring a Alerting integrace Vs monitorování třetích stran|Použití Azure Monitoring a Alerting||
|**Téma**: **předvolby pro zabezpečení** |||
|Soukromým a chráněným datovým kanálem?|Ano||
|Cluster připojeno k doméně (ESP)?|     Ano||
|On-Premises synchronizace AD do cloudu?|     Ano||
|Ne. Synchronizace uživatelů AD?|          100||
|OK pro synchronizaci hesel do cloudu?|    Ano||
|Jenom cloudoví uživatelé?|                 Ano||
|Potřeba vícefaktorové ověřování?|                       Ne|| 
|Požadavky na ověření dat?|  Ano||
|Řízení přístupu na základě rolí?|        Ano||
|Auditování potřeba?|                  Ano||
|Šifrování dat v klidovém stavu?|          Ano||
|Šifrování dat při přenosu?|       Ano||
|**Téma**: **předvolby Re – architektura** |||
|Jeden cluster vs specifické typy clusterů|Specifické typy clusterů||
|Společně umísťovat úložiště Vs vzdálené úložiště?|Vzdálené úložiště||
|Protože data uložená vzdáleně menší velikost clusteru?|Menší velikost clusteru||
|Použití více clusterů menší než jednoho velkého clusteru?|Použití několika menších clusterů||
|Používat vzdálený metastore?|Ano||
|Sdílet metaúložiště mezi různými clustery?|Ano||
|Dekonstruovat úlohy?|Nahraďte úloh Hive Sparkových úloh||
|Pro Orchestrace dat pomocí ADF?|Ne||
|HDInsight vs datovou platformou Hortonworks na IaaS?|HDInsight||

## <a name="next-steps"></a>Další postup

Přečtěte si další článek v této sérii:

- [Osvědčené postupy architektury pro místní migrace Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
