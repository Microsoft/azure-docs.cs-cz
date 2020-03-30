---
title: Součásti a verze Apache Hadoop - Azure HDInsight
description: Seznamte se s komponentami a verzemi Apache Hadoop v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2321918e9eae63a71d136753657bd7259862c2d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272653"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Jaké jsou komponenty a verze Apache Hadoop dostupné v HDInsightu?

Seznamte se s komponentami a verzemi ekosystému [Apache Hadoop](https://hadoop.apache.org/) v Microsoft Azure HDInsight a také o balíčku enterprise security. Přečtěte si také, jak zkontrolovat verze komponent Hadoop v HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Komponenty Apache Hadoop dostupné v různých verzích HDInsight

Azure HDInsight podporuje několik verzí clusteru Hadoop, které lze kdykoli nasadit. dubna 2017 je výchozí verze clusteru používaná službou Azure HDInsight 3.6.

Verze komponent přidružených k verzím clusteru HDInsight jsou uvedeny v následující tabulce:

> [!NOTE]  
> Výchozí verze služby HDInsight se může změnit bez předchozího upozornění. Pokud máte závislost na verzi, zadejte verzi HDInsight při vytváření clusterů pomocí sady .NET SDK s Azure PowerShell a Azure Classic CLI.

| Komponenta              | HDInsight 4.0 | HDInsight 3.6 (výchozí)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop a YARN | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache prase             | 0.16.0        | 0.16.0                      |
| Apache Úl            | 3.1.0         | 2.1.0 (klastry, které nejsou esp), 1.2.1 (clustery ESP)                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apačský ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apačský sqoop           | 1.4.7         | 1.4.6                       |
| Apač Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apač Mahout          | -             | 0.9.0+                      |
| Apač fénix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apačská Livy            | 0,5           | 0.4, 0.4, 0.3               |
| Apache Kafka           | 1.1.1, 2.1    | 1.1, 1.0 * (viz poznámka níže) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Kvůli aspekty výkonu systému vypršela podpora pro Verzi Kafka 0.10 v březnu 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Kontrola aktuálních informací o verzi součásti Hadoop

Verze komponent ekosystému Hadoop přidružené k verzím clusteru HDInsight se mohou měnit pomocí aktualizací hdinsightu. Chcete-li zkontrolovat součásti Hadoop a ověřit, které verze jsou používány pro cluster, použijte rozhraní API Ambari REST. Příkaz **GetComponentInformation** načítá informace o součástech služby. Podrobnosti naleznete v [dokumentaci Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Poznámky k verzi

Další poznámky k verzi v nejnovějších verzích HDInsightu najdete v [poznámkách](hdinsight-release-notes.md) k verzi HDInsight.

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Podpora vypršení platnosti a vyřazení pro verze HDInsight

**Vypršení platnosti podpory** znamená, že Microsoft už nebude poskytovat podporu pro zadanou verzi HDInsight a už nebude možné prostřednictvím portálu Azure pro vytvoření clusteru. Tyto verze však stále lze vytvořit pomocí azure CLI nebo různé sady SDK. 

**Vyřazení** verze HDInsight znamená, že existující clustery budou nadále fungovat tak, jak jsou. Nové clustery této verze však nelze vytvořit žádným způsobem (včetně CLI a Sad SDK). Ostatní funkce roviny ovládacího prvku (například ruční škálování a automatické škálování) také nemusí fungovat po vyřazení verze. Podpora není k dispozici pro vyřazené verze.

V následujících tabulkách jsou uvedeny verze rozhraní HDInsight. Datum vypršení platnosti podpory a odchodu do důchodu jsou také k dispozici, když jsou známy.

### <a name="available-versions"></a>Dostupné verze

V následující tabulce jsou uvedeny verze HDInsight, které jsou dostupné na webu Azure Portal, a další metody nasazení, jako je PowerShell a .NET SDK.

| VERZE HDInsight | Operační systém virtuálního počítače | Datum vydání | Datum vypršení platnosti podpory | Datum odchodu do důchodu | Vysoká dostupnost |  Dostupnost na webu Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |Září 24, 2018 | | |Ano |Ano |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4. dubna 2017April 4, 2017 | 31. prosince 2020 |31. prosince 2020 |Ano |Ano |

Spark 2.1, 2.2 & Podpora Kafka 1.0 vyprší 30.

> [!NOTE]  
> Po vypršení podpory pro verzi nemusí být dostupná prostřednictvím portálu Microsoft Azure. Verze clusteru jsou však nadále k dispozici pomocí parametru `Version` v příkazu Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) a v sdk .NET SDK až do data vyřazení verze.

### <a name="retired-versions"></a>Vyřazené verze

V následující tabulce jsou uvedeny verze HDInsight, které **nejsou** k dispozici na webu Azure Portal.

| VERZE HDInsight | VERZE HDP | Operační systém virtuálního počítače | Datum vydání | Datum vypršení platnosti podpory | Datum odchodu do důchodu | Vysoká dostupnost |  Dostupnost na webu Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |30. září 2016September 30, 2016 |5. září 2017 |28. června 2018 |Ano |Ne |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |29. března 2016March 29, 2016 |29. prosince 2016December 29, 2016 |9. ledna 2018 |Ano |Ne |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2. prosince 2015 |Červen 27, 2016 |31. července 2018 |Ano |Ne |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2. prosince 2015 |Červen 27, 2016 |31. července 2017July 31, 2017 |Ano |Ne |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12.04 LTS nebo Windows Server 2012 R2 |18. února 2015 February 18, 2015 |1. března 2016 |1. dubna 2017 |Ano |Ne |
| HDInsight 3.1 |HDP 2,1 |Windows Server 2012 R2 |24. června 2014 |18. května 2015May 18, 2015 |30. června 2016 |Ano |Ne |
| HDInsight 3.0 |HDP 2,0 |Windows Server 2012 R2 |11. února 2014 February 11, 2014 |17. září 2014 September 17, 2014 |30. června 2015 |Ano |Ne |
| HDInsight 2.1 |HDP 1,3 |Windows Server 2012 R2 |28. října 2013October 28, 2013 |12. května 2014 May 12, 2014 |31. května 2015May 31, 2015 |Ano |Ne |
| HDInsight 1,6 |HDP 1,1 | |28. října 2013October 28, 2013 |26. dubna 2014April 26, 2014 |31. května 2015May 31, 2015 |Ne |Ne |

> [!NOTE]  
> Vysoce dostupné clustery se dvěma hlavními uzly jsou ve výchozím nastavení nasazeny pro HDInsight verze 2.1 a novější. Nejsou k dispozici pro clustery HDInsight verze 1.6.

## <a name="enterprise-security-package-for-hdinsight"></a>Balíček podnikového zabezpečení pro HDInsight

Podnikové zabezpečení je volitelný balíček, který můžete přidat do clusteru HDInsight jako součást pracovního postupu vytváření clusteru. Balíček Enterprise Security Package podporuje:

- Integrace se službou Active Directory pro ověřování.

    V minulosti můžete vytvářet clustery HDInsight pouze s místním uživatelem správce a místním uživatelem SSH. Místní správce může přistupovat ke všem souborům, složkám, tabulkám a sloupcům.  Pomocí balíčku Enterprise Security Package můžete povolit řízení přístupu na základě rolí integrací clusterů HDInsight s vlastní službou Active Directory, která zahrnuje místní službu Active Directory, službu Azure Active Directory Domain Services nebo službu Active Directory v systému IaaS virtuálního počítače. Správce domény v clusteru může uživatelům udělit, aby pro přístup ke clusteru používali vlastní firemní (doménové) uživatelské jméno a heslo.

    Další informace naleznete v tématu:

    - [Úvod do zabezpečení Apache Hadoop s clustery HDInsight přilehlými k doméně](./domain-joined/hdinsight-security-overview.md)
    - [Plánování clusterů Apache Hadoop přilehlých k doméně Azure ve hdinsightu](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurace prostředí sandboxu připojeného k doméně](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurace clusterů HDInsight přilehlých k doméně pomocí služby Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorizace pro data

  - Integrace s Apache Rangerpro autorizaci pro Hive, Spark SQL a fronty příze.
  - Můžete nastavit řízení přístupu u souborů a složek.

    Další informace naleznete v tématu:

  - [Konfigurace zásad Apache Hive v hdinsightu přilehlém k doméně](./domain-joined/apache-domain-joined-run-hive.md)

- Zobrazení protokolů auditu pro sledování přístupů a nakonfigurovaných zásad.

### <a name="supported-cluster-types"></a>Podporované typy clusterů

V současné době balíček Enterprise Security Package podporují pouze následující typy clusterů:

- Hadoop (pouze hdinsight 3.6)
- Spark
- Kafka
- HBase
- Interaktivní dotaz

### <a name="support-for-azure-data-lake-storage"></a>Podpora úložiště datových jezer Azure

Balíček enterprise security podporuje použití Azure Data Lake Storage jako primárního úložiště i úložiště doplňků.

### <a name="pricing-and-service-level-agreement"></a>Cenová smlouva a smlouva o úrovni služeb

Informace o cenách a sla pro balíček enterprise security naleznete v tématu [HDInsight pricing](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Smlouva o úrovni služeb pro verze clusteru HDInsight

Smlouva o úrovni služeb (SLA) je definována z hlediska _okna podpory_. Okno podpory je doba, po kterou je verze clusteru HDInsight podporována službou microsoft customer service a support. Pokud má verze _datum vypršení platnosti podpory,_ které uplynulo, cluster HDInsight je mimo okno podpory. Datum vypršení platnosti podpory pro zadanou verzi HDInsight X (po dostupnosti novější verze X+1) se vypočítá jako pozdější:  

- Formule 1: Přidejte 180 dní k datu vydání clusteru HDInsight verze X.
- Vzorec 2: Přidejte 90 dní k datu, kdy je cluster HDInsight verze X+1 k dispozici na webu Azure Portal.

Datum _vyřazení_ je datum, po kterém nelze vytvořit verzi clusteru na HDInsight. července 2017 nelze změnit velikost clusteru HDInsight po datu jeho vyřazení.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Poznámky k verzi Hortonworks spojené s verzemi HDInsight

Tato část obsahuje odkazy na poznámky k verzi pro distribuce datové platformy Hortonworks a součásti Apache, které se používají s HDInsight.
* Cluster HDInsight verze 4.0 používá distribuci Hadoop, která je založena na [datové platformě Hortonworks 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* Cluster HDInsight verze 3.6 používá distribuci Hadoop, která je založena na [datové platformě Hortonworks 2.6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Cluster HDInsight verze 3.5 používá distribuci Hadoop, která je založena na [datové platformě Hortonworks 2.5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Cluster HDInsight verze 3.5 je _výchozí_ cluster Hadoop, který se vytvořil na webu Azure Portal.
* Cluster HDInsight verze 3.4 používá distribuci Hadoop, která je založena na [datové platformě Hortonworks 2.4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Cluster HDInsight verze 3.3 používá distribuci Hadoop, která je založena na [datové platformě Hortonworks 2.3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Poznámky k vydání Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) jsou k dispozici na webových stránkách Apache.
  * [Poznámky k verzi Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) jsou k dispozici na webových stránkách Apache.
* Cluster HDInsight verze 3.2 používá distribuci Hadoop, která je založena na [datové platformě Hortonworks 2.2][hdp-2-2].

  * Poznámky k verzi pro konkrétní komponenty Apache jsou k dispozici takto: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)a [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Cluster HDInsight verze 3.1 používá hadoopovou distribuci založenou na [datové platformě Hortonworks 2.1.7][hdp-2-1-7]. HdInsight 3.1 clustery vytvořené před listopadem 7, 2014, jsou založeny na [Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Cluster HDInsight verze 3.0 používá distribuci Hadoop, která je založena na [datové platformě Hortonworks 2.0][hdp-2-0-8].
* Cluster HDInsight verze 2.1 používá hadoopovou distribuci založenou na [datové platformě Hortonworks 1.3][hdp-1-3-0].
* Cluster HDInsight verze 1.6 používá hadoopovou distribuci založenou na [datové platformě Hortonworks 1.1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí konfigurace uzlu a velikosti virtuálních počítačů pro clustery

Další informace o tom, které soupoložky virtuálních počítačů vybrat pro váš cluster, najdete [v tématu Azure HDInsight podrobnosti o konfiguraci clusteru](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Další kroky

- [Nastavení clusteru pro Apache Hadoop, Spark a další na HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Práce v Apache Hadoop na HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
