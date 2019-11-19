---
title: Apache Hadoop součásti a verze – Azure HDInsight
description: Přečtěte si informace o komponentách a verzích Apache Hadoop ve službě Azure HDInsight.
keywords: verze systému Hadoop, součásti ekosystému Hadoop, součásti systému Hadoop, postup kontroly verze Hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 598f423bdb15b1d4975633db19c28b399fde484b
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166799"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Jaké jsou součásti Apache Hadoop a verze, které jsou k dispozici v HDInsight?

Přečtěte si informace o komponentách a verzích ekosystému [Apache Hadoop](https://hadoop.apache.org/) v Microsoft Azure HDInsight a také o balíček zabezpečení podniku. Přečtěte si také, jak kontrolovat verze komponent Hadoop ve službě HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop komponenty k dispozici v různých verzích HDInsight

Azure HDInsight podporuje víc verzí clusterů Hadoop, které se dají kdykoli nasadit. Od 4. dubna 2017 je výchozí verze clusteru používaná v Azure HDInsight 3,6.

Verze komponent přidružených ke clusterům HDInsight jsou uvedené v následující tabulce: 

> [!NOTE]  
> Výchozí verze služby HDInsight se může změnit bez předchozího upozornění. Pokud máte závislost verze, určete verzi HDInsight při vytváření clusterů pomocí sady .NET SDK s Azure PowerShell a Azure Classic CLI.

| Komponenta              | HDInsight 4.0 | HDInsight 3,6 (výchozí)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop a PŘÍZe | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache Pig             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 2.1.0, 1.2.1                |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.1         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | 4.3.1         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0+                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2.4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2.1    | 1,1, 1,0 * (viz poznámka níže) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.0                       |
| Výstupu                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Kvůli důležitým informacím o výkonu systému skončila podpora pro Kafka verze 0,10 v březnu 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Vyhledat aktuální informace o verzi komponenty Hadoop

Verze komponent ekosystému Hadoop přidružené k verzím clusterů HDInsight se můžou měnit s aktualizacemi HDInsight. Chcete-li zkontrolovat komponenty Hadoop a ověřit, které verze jsou používány pro cluster, použijte REST API Ambari. Příkaz **GetComponentInformation** načte informace o součástech služby. Podrobnosti najdete v dokumentaci k [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Poznámky k verzi

Další poznámky k verzi pro nejnovější verze služby HDInsight najdete v tématu [poznámky k verzi HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight

V následujících tabulkách jsou uvedeny verze HDInsight. K dispozici jsou také data vypršení platnosti podpory a datum vyřazení, pokud jsou známy.

### <a name="available-versions"></a>Dostupné verze

V následující tabulce jsou uvedeny verze HDInsight, které jsou k dispozici v Azure Portal a také jiné metody nasazení jako PowerShell a .NET SDK.

| Verze HDInsight | OPERAČNÍ SYSTÉM VIRTUÁLNÍHO POČÍTAČE | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  Dostupnost v Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |24. září 2018 | | |Ano |Ano |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4\. dubna 2017 | 30. června 2020 |31. prosince 2020 |Ano |Ano |


> [!NOTE]  
> Po vypršení platnosti podpory pro verzi nemusí být k dispozici prostřednictvím portál Microsoft Azure. Verze clusteru budou ale dál k dispozici pomocí parametru `Version` v příkazu Windows PowerShell [New-AzHDInsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) a .NET SDK až do data vyřazení verze.
>

### <a name="retired-versions"></a>Vyřazené verze

V následující tabulce jsou uvedeny verze HDInsight, **které nejsou k dispozici v** Azure Portal.

| Verze HDInsight | Verze HDP | OPERAČNÍ SYSTÉM VIRTUÁLNÍHO POČÍTAČE | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  Dostupnost na Azure Portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |30. září 2016 |5\. září 2017 |28. června 2018 |Ano |Ne |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |29. března 2016 |29. prosince 2016 |9\. ledna 2018 |Ano |Ne |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2\. prosince 2015 |27. června 2016 |31. července 2018 |Ano |Ne |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2\. prosince 2015 |27. června 2016 |31. července 2017 |Ano |Ne |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS nebo Windows Server 2012 R2 |18. února 2015 |1\. března 2016 |1\. dubna 2017 |Ano |Ne |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |24. června 2014 |18. května 2015 |30. června 2016 |Ano |Ne |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |11. února 2014 |17. září 2014 |30. června 2015 |Ano |Ne |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |28. října 2013 |12. května 2014 |31. května 2015 |Ano |Ne |
| HDInsight 1,6 |HDP 1.1 | |28. října 2013 |26. dubna 2014 |31. května 2015 |Ne |Ne |

> [!NOTE]  
> Clustery s vysokou dostupností se dvěma hlavními uzly se ve výchozím nastavení nasazují pro HDInsight verze 2,1 a novější. Pro clustery HDInsight verze 1,6 nejsou k dispozici.

## <a name="enterprise-security-package-for-hdinsight"></a>Balíček zabezpečení podniku pro HDInsight

Enterprise Security je volitelný balíček, který můžete přidat do clusteru HDInsight jako součást pracovního postupu vytvoření clusteru. Balíček zabezpečení podniku podporuje:

- Integrace se službou Active Directory pro ověřování.

    V minulosti můžete vytvářet clustery HDInsight jenom s místním správcem a místním uživatelem SSH. Místní uživatel správce má přístup ke všem souborům, složkám, tabulkám a sloupcům.  Pomocí Balíček zabezpečení podniku můžete povolit řízení přístupu na základě role integrací clusterů HDInsight s vlastní službou Active Directory, která zahrnuje místní službu Active Directory, Azure Active Directory Domain Services nebo Active Directory v IaaS. virtuální počítač. Správce domény v clusteru může uživatelům udělit přístup ke clusteru pomocí vlastního uživatelského jména a hesla v podnikové síti (domény). 

    Další informace naleznete v tématu:

    - [Úvod do zabezpečení Apache Hadoop s využitím clusterů HDInsight připojených k doméně](./domain-joined/hdinsight-security-overview.md)
    - [Plánování clusterů Apache Hadoop připojených k doméně Azure v HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurace prostředí izolovaného prostoru (sandbox) připojeného k doméně](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurace clusterů HDInsight připojených k doméně pomocí Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Autorizace pro data

  - Integrace s Apache Ranger pro autorizaci pro podregistry, Spark SQL a přízové fronty.
  - Můžete nastavit řízení přístupu k souborům a složkám.

    Další informace naleznete v tématu:

  - [Konfigurace zásad Apache Hive v HDInsight připojené k doméně](./domain-joined/apache-domain-joined-run-hive.md)

- Prohlédněte si protokoly auditu a sledujte přístup a nakonfigurované zásady. 

### <a name="supported-cluster-types"></a>Podporované typy clusterů

V současné době jsou Balíček zabezpečení podniku podporovány pouze následující typy clusterů:

- Hadoop (pouze HDInsight 3,6)
- Spark
- Interaktivní dotaz

### <a name="support-for-azure-data-lake-storage"></a>Podpora Azure Data Lake Storage

Balíček zabezpečení podniku podporuje použití Azure Data Lake Storage jako primárního úložiště i úložiště doplňku.

### <a name="pricing-and-service-level-agreement"></a>Ceny a smlouva o úrovni služeb

Informace o cenách a smlouvě SLA pro Balíček zabezpečení podniku najdete v tématu [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).


## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Smlouva o úrovni služeb pro verze clusteru HDInsight

Smlouva o úrovni služeb (SLA) je definována v souvislosti s _oknem podpory_. Okno podpory je časový interval, po který je podporovaná verze clusteru HDInsight zákaznická služba a podpora Microsoftu. Pokud má verze prošlé _Datum vypršení platnosti podpory_ , cluster HDInsight je mimo okno podpory. Datum vypršení platnosti podpory pro zadanou verzi HDInsight X (po dostupnosti novější verze X + 1) se počítá jako pozdější z těchto:  

* Vzorec 1: přidejte 180 dní do data, kdy byl vydán cluster HDInsight verze X.
* Vzorec 2: přidejte 90 dní do data, kdy je ve Azure Portal k dispozici cluster HDInsight verze X + 1.

_Datum vyřazení_ je datum, po jehož uplynutí nelze ve službě HDInsight vytvořit verzi clusteru. Od 31. července 2017 nemůžete změnit velikost clusteru HDInsight po jeho datu vyřazení. 

> [!NOTE]  
> Clustery Windows HDInsight (včetně verzí 2,1, 3,0, 3,1, 3,2 a 3,3) se spouštějí v řadě 4 hostovaného operačního systému Azure, která používá systémovou verzi Windows serveru 64 R2. Řada operačních systémů hosta Azure verze 4 podporuje .NET Framework verze 4,0, 4,5, 4.5.1 a 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Poznámky k verzi Hortonworks přidružené k verzím HDInsight

V části najdete odkazy na poznámky k verzi pro distribuce datových platforem Hortonworks a součásti Apache používané v HDInsight.
* Cluster HDInsight verze 4,0 používá distribuci Hadoop založenou na [Hortonworks datové platformě 3,0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html) .
* Cluster HDInsight verze 3,6 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 2,6](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Cluster HDInsight verze 3,5 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 2,5](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Cluster HDInsight verze 3,5 je _výchozím_ clusterem Hadoop, který je vytvořený v Azure Portal.
* Cluster HDInsight verze 3,4 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 2,4](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Cluster HDInsight verze 3,3 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 2,3](https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Poznámky k verzi Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) jsou k dispozici na webu Apache.
  * [Poznámky k verzi Apache Hive](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) jsou k dispozici na webu Apache.
* Cluster HDInsight verze 3,2 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 2,2][hdp-2-2].

  * Poznámky k verzi pro konkrétní součásti Apache jsou k dispozici takto: [podregistr 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [prase 0,14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBA 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [nitě 2,6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2,0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112)a [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Cluster HDInsight verze 3,1 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 2.1.7][hdp-2-1-7]. Clustery HDInsight 3,1 vytvořené před listopadu 2014 7. listopadu jsou založené na [Hortonworks datové platformě 2.1.1][hdp-2-1-1].
* Cluster HDInsight verze 3,0 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 2,0][hdp-2-0-8].
* Cluster HDInsight verze 2,1 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 1,3][hdp-1-3-0].
* Cluster HDInsight verze 1,6 používá distribuci Hadoop, která je založená na [Hortonworks datové platformě 1,1][hdp-1-1-0].

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí konfigurace uzlů a velikosti virtuálních počítačů pro clustery

Další informace o tom, které SKU virtuálních počítačů vybrat pro váš cluster, najdete v tématu [Podrobnosti o konfiguraci clusteru Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Další kroky
- [Nastavení clusteru pro Apache Hadoop, Spark a další funkce v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html
