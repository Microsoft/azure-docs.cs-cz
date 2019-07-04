---
title: Apache Hadoop komponenty a verze – Azure HDInsight
description: Informace o Apache Hadoop komponenty a verze v HDInsight a úrovně služeb, která je k dispozici v této cloudové distribuce datovou platformou Hortonworks.
keywords: verzích systému hadoop, ekosystém součásti platformy hadoop, součásti platformy hadoop, jak zkontrolovat verze systému hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/07/2019
ms.openlocfilehash: 72be96e89cd46267d0c0795bd5121de76e48cf6f
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484101"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Co je Apache Hadoop komponenty a verze, které jsou k dispozici s HDInsight?

Další informace o [Apache Hadoop](https://hadoop.apache.org/) ekosystému komponenty a verze v Microsoft Azure HDInsight, jakož i Enterprise Security Package. Zjistíte také, jak chcete zkontrolovat verze komponenty systému Hadoop v HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop součásti, které jsou k dispozici v různých verzích HDInsight

Azure HDInsight podporuje více verzích clusterů systému Hadoop, které mohou být nasazeny v každém okamžiku. Každou volbu verze vytvoří konkrétní verze dané distribuce HDP a sada komponent, které jsou obsaženy v rámci příslušné distribuci. Od 4. dubna 2017 výchozí verze clusteru používat Azure HDInsight 3.6 je a je založen na HDP 2.6.

Verze součástí spojené s verzemi clusterů HDInsight jsou uvedeny v následující tabulce: 

> [!NOTE]  
> Výchozí verze pro službu HDInsight mohou změnit bez předchozího upozornění. Pokud máte verzi závislosti, zadejte verzi HDInsight při vytváření clusterů s využitím .NET SDK pomocí Azure Powershellu a rozhraní příkazového řádku Azure Classic.

| Komponenta | HDInsight 4.0 | HDInsight 3.6 (výchozí) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 |
|---------------------------|---------------|-----------------------------|---------------|---------------|---------------|----------------------|
| Datová platforma Hortonworks | 3.0 | 2.6 | 2.5 | 2.4 | 2.3 | 2.2 |
| Apache Hadoop a YARN | 3.1.1 | 2.7.3 | 2.7.3 | 2.7.1 | 2.7.1 | 2.6.0 |
| Apache Tez | 0.9.1 | 0.7.0 | 0.7.0 | 0.7.0 | 0.7.0 | 0.5.2 |
| Apache Pig | 0.16.0 | 0.16.0 | 0.16.0 | 0.15.0 | 0.15.0 | 0.14.0 |
| Apache Hive a HCatalog | - | 1.2.1 | 1.2.1 | 1.2.1 | 1.2.1 | 0.14.0 |
| Apache Hive | 3.1.0 | 2.1.0 | - | - | - | - |
| Apache Tez Hive2 | - | 0.8.4 | - | - | - | - |
| Apache Ranger | 1.1.0 | 0.7.0 | 0.6.0 | - | - | - |
| Apache HBase | 2.0.1 | 1.1.2 | 1.1.2 | 1.1.2 | 1.1.1 | 0.98.4 |
| Apache Sqoop | 1.4.7 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.6 | 1.4.5 |
| Apache Oozie | 4.3.1 | 4.2.0 | 4.2.0 | 4.2.0 | 4.2.0 | 4.1.0 |
| Apache Zookeeper | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 | 3.4.6 |
| Apache Storm | - | 1.1.0 | 1.0.1 | 0.10.0 | 0.10.0 | 0.9.3 |
| Apache Mahout | - | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0+ | 0.9.0 |
| Apache Phoenix | 5 | 4.7.0 | 4.7.0 | 4.4.0 | 4.4.0 | 4.2.0 |
| Apache Spark | 2.3.1, 2.4 | 2.3.0, 2.2.0, 2.1.0 | 1.6.2, 2.0 | 1.6.0 | 1.5.2 | 1.3.1 (jenom Windows) |
| Apache Livy | 0,5 | 0.4, 0.4, 0.3 | 0.3 | 0.3 | 0.2 | - |
| Apache Kafka | 1.1.1, 2.1 | 1.1, 1.0 * (viz poznámka níže) | 0.10.0 | 0.9.0 | - | - |
| Apache Ambari | 2.7.0 | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 | - |
| Apache Zeppelin | 0.8.0 | 0.7.0 | - | - | - | - |
| Mono | 4.2.1 | 4.2.1 | 4.2.1 | 3.2.8 | - | - |

> [!NOTE]
> Z důvodu důležité informace o výkonu systému podpora pro systém Kafka verze 0.10 vypršelo v březnu 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Zkontrolujte aktuální informace o verzi komponenty systému Hadoop

Verze součástí ekosystému Hadoop spojené s verzemi clusterů HDInsight můžete změnit pomocí aktualizací na HDInsight. Chcete-li ověřit, jaké verze jsou používány pro cluster a zkontrolujte součásti platformy Hadoop, pomocí rozhraní Ambari REST API. **GetComponentInformation** příkaz načte informace o součásti služby. Podrobnosti najdete v tématu [dokumentaci Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Poznámky k verzi

Zobrazit [zpráva k vydání verze HDInsight](hdinsight-release-notes.md) další poznámky k verzi v nejnovějších verzích HDInsight.

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight

V následujících tabulkách jsou uvedeny verzích HDInsight. Verze HDP, které odpovídají jednotlivým verzím HDInsight jsou uvedeny společně s data vydání produktu. Datum vypršení platnosti a vyřazení podpory jsou také k dispozici, pokud už známý.

### <a name="available-versions"></a>Dostupné verze

Následující tabulka uvádí verze HDInsight, které jsou k dispozici na webu Azure portal, jakož i jiné metody nasazení, například prostředí PowerShell a sady .NET SDK.

| Verze HDInsight | Verze HDP | VM OS | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení z provozu | Vysoká dostupnost |  Dostupnost na webu Azure portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |HDP 3.0 |Ubuntu 16.0.4 LTS |24. září 2018 | | |Ano |Ano |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4\. dubna 2017 | 30. června 2020 |31. prosince 2020 |Ano |Ano |


> [!NOTE]  
> Za podporu pro verzi vypršela platnost, nemusí být k dispozici prostřednictvím portálu Microsoft Azure. Ale i nadále být k dispozici pomocí verze clusteru `Version` parametr v prostředí Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) příkazu a sady SDK .NET až do verze vyřazením.
>

### <a name="retired-versions"></a>Vyřazené verze

Následující tabulka uvádí verze HDInsight, které jsou **není** k dispozici na webu Azure Portal.

| Verze HDInsight | Verze HDP | VM OS | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení z provozu | Vysoká dostupnost |  Dostupnost na webu Azure portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (Bez Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |30. září 2016 |5\. září 2017 |28. června 2018 |Ano |Ne |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29. března 2016 |29. prosince 2016 |9\. ledna 2018 |Ano |Ne |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2\. prosince 2015 |27. června 2016 |31. července 2018 |Ano |Ne |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2\. prosince 2015 |27. června 2016 |31. července 2017 |Ano |Ne |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS nebo Windows Server 2012 R2 |18. února 2015 |1\. března 2016 |1\. dubna 2017 |Ano |Ne |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24. června 2014 |18. května 2015 |30. června 2016 |Ano |Ne |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11. února 2014 |17. září 2014 |30. června 2015 |Ano |Ne |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28. října 2013 |12. května 2014 |31. května 2015 |Ano |Ne |
| HDInsight 1.6 |HDP 1.1 | |28. října 2013 |26. dubna 2014 |31. května 2015 |Ne |Ne |

> [!NOTE]  
> Clustery s vysokou dostupností pomocí dvou hlavních uzlů se nasadí ve výchozím nastavení pro HDInsight verze 2.1 nebo novější. Nejsou k dispozici u clusterů HDInsight verze 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Enterprise Security Package pro HDInsight

Podnikové zabezpečení je volitelný balíček, který můžete přidat jako součást pracovního postupu vytvoření clusteru v clusteru HDInsight. Enterprise Security Package podporuje:

- Integrace se službou Active Directory pro ověřování.

    V minulosti můžete vytvořit pouze clustery HDInsight s místní správce a místní uživatele SSH. Má uživatel místní správce přístup všech souborů, složek, tabulek a sloupců.  S balíčkem Enterprise Security Package, můžete povolit řízení přístupu na základě rolí integrací clusterů HDInsight s vlastní služby Active Directory, mezi které patří místní služby Active Directory, Azure Active Directory Domain Services nebo služby Active Directory na IaaS virtuální počítač. Správce domény v clusteru můžete udělit uživatelům používat jejich vlastní podnikové (doména) uživatelské jméno a heslo pro přístup ke clusteru. 

    Další informace naleznete v tématu:

    - [Úvod do zabezpečení Apache Hadoop s clustery HDInsight připojené k doméně](./domain-joined/apache-domain-joined-introduction.md)
    - [Plán Azure clustery systému Apache Hadoop připojených k doméně v HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurace prostředí sandboxu připojeného k doméně](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurace clusterů HDInsight připojených k doméně pomocí Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorizace pro data

  - Integrace s Apache Rangerem pro autorizaci pro fronty Yarn, Hive a Spark SQL.
  - Můžete nastavit řízení přístupu k souborům a složkám.

    Další informace naleznete v tématu:

  - [Nakonfigurovat zásady Apache Hive v HDInsight připojených k doméně](./domain-joined/apache-domain-joined-run-hive.md)

- Zobrazení protokolů auditu na nakonfigurovaných zásad a monitorování přístupy. 

### <a name="supported-cluster-types"></a>Typy podporované clusterů

V současné době pouze následující typy clusteru podporují Enterprise Security Package:

- Hadoop (pouze HDInsight 3.6)
- Spark
- Interaktivní dotaz

### <a name="support-for-azure-data-lake-storage"></a>Podpora pro Azure Data Lake Storage

Enterprise Security Package podporuje, pomocí Azure Data Lake Storage jako primární úložiště a doplňkové úložiště.

### <a name="pricing-and-service-level-agreement"></a>Smlouva o úrovni cen a služby

Informace o cenách a smlouva SLA pro Enterprise Security Package najdete v tématu [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Smlouva o úrovni služeb pro verze clusteru HDInsight

Smlouva o úrovni služeb (SLA) je definován z hlediska _podporu okno_. Okno podpory je časový úsek, který je ve verzi clusteru HDInsight nepodporuje Microsoft zákaznický servis a podporu. Pokud je verze _podporují datum vypršení platnosti_ , který je předán, HDInsight cluster je mimo časový interval pro podporu. Datum vypršení platnosti podpora pro zadanou HDInsight verze X (po je dostupná novější verze X + 1) se počítá jako později z:  

* Vzorec 1: Přidáte k datu, kdy byla vydána verze clusteru HDInsight X 180 dnů.
* Vzorec 2: Přidáte 90 dní k datu, kdy verze clusteru HDInsight X + 1 je k dispozici na webu Azure portal.

_Vyřazením_ je datum, po jejímž uplynutí verze clusteru nelze pro HDInsight. Od 31. července 2017, nelze změnit velikost clusteru služby HDInsight po datu vyřazení z provozu. 

> [!NOTE]  
> Clustery HDInsight Windows (včetně verze, 2.1, 3.0, 3.1, 3.2 nebo 3.3) spustit v Azure hostovaný operační systém řady verze 4, který používá 64bitovou verzi systému Windows Server 2012 R2. Azure hostovaný operační systém řady verze 4 podporuje rozhraní .NET Framework verze 4.0, 4.5, 4.5.1 a 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Poznámky k spojené s HDInsight verze verzi Hortonworks

V části obsahuje odkazy na poznámky k verzi pro datovou platformou Hortonworks distribucích a Apache součásti, které se používají s HDInsight.
* Verze clusteru HDInsight 4.0 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* Verze clusteru HDInsight 3.6 využívá distribuci systému Hadoop, který je založen na [datová platforma Hortonworks 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Verze clusteru HDInsight 3.5 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Verze clusteru HDInsight 3.5 není _výchozí_ cluster Hadoop, který se vytvoří na webu Azure Portal.
* Verze clusteru HDInsight 3.4 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Verze clusteru HDInsight 3.3 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Zpráva k vydání verze Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) jsou k dispozici na webu Apache.
  * [Apache Hive zpráva k vydání verze](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) jsou k dispozici na webu Apache.
* Verze clusteru HDInsight 3.2 využívá distribuci systému Hadoop, který je založen na [datovou platformu Hortonworks 2.2][hdp-2-2].

  * Zpráva k vydání verze pro konkrétní komponenty Apache jsou dostupné takto: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [běžné](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), a [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Verze clusteru HDInsight 3.1 využívá distribuci systému Hadoop, který je založen na [datovou platformou Hortonworks 2.1.7][hdp-2-1-7]. HDInsight 3.1 clusters created before November, 7, 2014, are based on [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Verze clusteru HDInsight 3.0 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Verze clusteru HDInsight 2.1 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Verze clusteru HDInsight 1.6 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 1.1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí konfigurace a virtuální počítač velikosti uzlů pro clustery

Následující tabulky uvádějí výchozí velikostí virtuálních počítačů (VM) pro clustery HDInsight.  V této tabulce jsou nezbytné pro zjištění velikosti virtuálních počítačů pro použití při vytváření skriptů prostředí PowerShell nebo rozhraní příkazového řádku Azure k nasazení clusterů HDInsight.

> [!IMPORTANT]  
> Pokud potřebujete více než 32 uzlů pracovního procesu v clusteru, musíte vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.

* Všechny podporované oblastech kromě Brazílie – jih a Japonsko – západ:

|Typ clusteru|Hadoop|HBase|Interaktivní dotaz|Storm|Spark|ML Server|Kafka|
|---|---|---|---|---|---|---|---|
|Hlavní: výchozí velikosti virtuálního počítače|D12 v2|D12 v2|D13 v2|A3|D12 v2|D12 v2|D3v2|
|Hlavní: doporučené velikosti virtuálních počítačů|D3 v2|D3 v2|D13|A4 v2|D12 v2|D12 v2|A2M v2|
||D4 v2|D4 v2|D14|A8 v2|D13 v2|D13 v2|D3 v2|
||D12 v2|D12 v2|E16 v3|A2m v2|D14 v2|D14 v2|D4 v2|
||E4 v3|E4 v3|E32 v3|E4 v3|E4 v3|E4 v3|D12 v2|
|Pracovních procesů: výchozí velikosti virtuálního počítače|D4 v2|D4 v2|D14 v2|D3 v2|D13 v2|D4 v2|D12v2 4 se 2 disky S30 jednoho zprostředkovatele|
|Pracovního procesu: doporučené velikosti virtuálních počítačů|D3 v2|D3 v2|D13|D3 v2|D4 v2|D4 v2|D13 v2|
||D4 v2|D4 v2|D14|D4 v2|D12 v2|D12 v2|DS12 v2|
||D12 v2|D12 v2|E16 v3|D12 v2|D13 v2|D13 v2|DS13 v2|
||E4 v3|E4 v3|E20 v3|E4 v3|D14 v2|D14 v2|E4 v3|
||||E32 v3||E16 v3|E16 v3|ES4 v3|
||||E64 v3||E20 v3|E20 v3|E8 v3|
||||||E32 v3|E32 v3|ES8 v3|
||||||E64 v3|E64 v3||
|ZooKeeper: výchozí velikosti virtuálního počítače||A4 v2|A4 v2|A4 v2||A2 v2|D3v2|
|ZooKeeper: doporučené velikosti virtuálních počítačů||A4 v2||A2 v2|||A2M v2|
|||A8 v2||A4 v2|||D3 v2|
|||A2m v2||A8 v2|||E8 v3|
|Služby ML: výchozí velikost virtuálního počítače||||||D4 v2||
|Služby ML: Doporučená velikost virtuálního počítače||||||D4 v2||
|||||||D12 v2||
|||||||D13 v2||
|||||||D14 v2||
|||||||E16 v3||
|||||||E20 v3||
|||||||E32 v3||
|||||||E64 v3||

* Brazílie – jih a Japonsko – západ pouze (žádné velikosti v2):

  | Typ clusteru | Hadoop | HBase | Interaktivní dotaz |Storm | Spark | Služby ML |
  | --- | --- | --- | --- | --- | --- | --- |
  | Hlavní: výchozí velikosti virtuálního počítače |D12 |D12  | D13 |A3 |D12 |D12 |
  | Hlavní: doporučené velikosti virtuálních počítačů |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |A3,<br/> A4,<br/> A5 |D12,<br/> D13,<br/> D14 |D12,<br/> D13,<br/> D14 |
  | Pracovních procesů: výchozí velikosti virtuálního počítače |D4 |D4  |  D14 |D3 |D13 |D4 |
  | Pracovního procesu: doporučené velikosti virtuálních počítačů |D3,<br/> D4,<br/> D12 |D3,<br/> D4,<br/> D12  | D13,<br/> D14 |D3,<br/> D4,<br/> D12 |D4,<br/> D12,<br/> D13,<br/> D14 | D4,<br/> D12,<br/> D13,<br/> D14 |
  | ZooKeeper: výchozí velikosti virtuálního počítače | |A4 v2 | A4 v2| A4 v2 | | A2 v2|
  | ZooKeeper: doporučené velikosti virtuálních počítačů | |A2,<br/> A3,<br/> A4 | |A2,<br/> A3,<br/> A4 | | |
  | Služby ML: výchozí velikosti virtuálních počítačů | | | | | |D4 |
  | Služby ML: doporučené velikosti virtuálních počítačů | | | | | |D4,<br/> D12,<br/> D13,<br/> D14 |

> [!NOTE]
> - Hlavní se označuje jako *Nimbus* typ clusteru Storm.
> - Pracovního procesu se označuje jako *Supervisor* typ clusteru Storm.
> - Pracovního procesu se označuje jako *oblasti* HBase typ clusteru.

## <a name="next-steps"></a>Další postup
- [Nastavení clusteru pro Apache Hadoop, Spark a další informace o HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Práce v Apache Hadoop v HDInsight z Windows PC](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
