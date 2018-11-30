---
title: Apache Hadoop komponenty a verze – Azure HDInsight
description: Informace o Apache Hadoop komponenty a verze v HDInsight a úrovně služeb, která je k dispozici v této cloudové distribuce datovou platformou Hortonworks.
keywords: verzích systému hadoop, ekosystém součásti platformy hadoop, součásti platformy hadoop, jak zkontrolovat verze systému hadoop
services: hdinsight
ms.reviewer: jasonh
author: kkampf
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: kakampf
ms.openlocfilehash: 2ae0450be420edeb4ef18d0c9e5ca6e1dacaf50d
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497014"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Co je Apache Hadoop komponenty a verze, které jsou k dispozici s HDInsight?

Další informace o [Apache Hadoop](https://hadoop.apache.org/) ekosystému komponenty a verze v Microsoft Azure HDInsight, jakož i Enterprise Security Package. Zjistíte také, jak chcete zkontrolovat verze komponenty systému Hadoop v HDInsight. 

Každá verze HDInsight je Cloudová distribuce verze Hortonworks Data Platform (HDP).

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop součásti, které jsou k dispozici v různých verzích HDInsight
Azure HDInsight podporuje více verzích clusterů systému Hadoop, které mohou být nasazeny v každém okamžiku. Každou volbu verze vytvoří konkrétní verze dané distribuce HDP a sada komponent, které jsou obsaženy v rámci příslušné distribuci. Od 4. dubna 2017 výchozí verze clusteru používat Azure HDInsight 3.6 je a je založen na HDP 2.6.

Verze součástí spojené s verzemi clusterů HDInsight jsou uvedeny v následující tabulce: 

> [!NOTE]
> Výchozí verze pro službu HDInsight mohou změnit bez předchozího upozornění. Pokud máte verzi závislosti, zadejte verzi HDInsight při vytváření clusterů s využitím .NET SDK pomocí Azure Powershellu a rozhraní příkazového řádku Azure Classic.

| Komponenta | HDInsight 4.0 (Preview) | HDInsight 3.6 (výchozí) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight verze 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- | --- |--- |
| Datová platforma Hortonworks |3.0 |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop a YARN |3.1.1 |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.9.1 |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive a HCatalog |-|1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive |3.1.0 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 |-| 0.8.4 |-|-|-|-|-|-|
| Apache Ranger |1.1.0 |0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |2.0.1 |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.7 |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.3.1 |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.2.1 |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |-|0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |5 |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.3.1 |2.3.0, 2.2.0, 2.1.0 |1.6.2, 2.0 |1.6.0 |1.5.2 |1.3.1 (jenom Windows) |-|-|
| Apache Livy |0,5 |0.4 |0.3 |0.3 |0.2 |-|-|-|
| Apache Kafka | 1.1 |1.1, 1.0, 0.10.1 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.7.0 |2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.8.0 |0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |4.2.1 |3.2.8 |-|-|-|
| Apache posuvníku |-| 0.92.0 |-|-|-|-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Zkontrolujte aktuální informace o verzi komponenty systému Hadoop

Verze součástí ekosystému Hadoop spojené s verzemi clusterů HDInsight můžete změnit pomocí aktualizací na HDInsight. Chcete-li ověřit, jaké verze jsou používány pro cluster a zkontrolujte součásti platformy Hadoop, pomocí rozhraní Ambari REST API. **GetComponentInformation** příkaz načte informace o součásti služby. Podrobnosti najdete v tématu [dokumentaci Apache Ambari][ambari-docs].

Další způsob kontroly verze komponenty je v případě clusterů Windows přihlášení ke clusteru pomocí vzdálené plochy a zkontrolovat obsah adresáře C:\apps\dist\.

> [!IMPORTANT]
> Linux je pouze operační systém používaný v HDInsight verze 3.4 a vyšší. Další informace najdete v tématu [vyřazení Windows na HDInsight](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Poznámky k verzi

Zobrazit [zpráva k vydání verze HDInsight](hdinsight-release-notes.md) další poznámky k verzi v nejnovějších verzích HDInsight.

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight
V následujících tabulkách jsou uvedeny verzích HDInsight. Verze HDP, které odpovídají jednotlivým verzím HDInsight jsou uvedeny společně s data vydání produktu. Datum vypršení platnosti a vyřazení podpory jsou také k dispozici, pokud už známý.

### <a name="available-versions"></a>Dostupné verze

Následující tabulka uvádí verze HDInsight, které jsou k dispozici na webu Azure Portal, jakož i jiné metody nasazení, například prostředí PowerShell a sady .NET SDK.

| Verze HDInsight | Verze HDP | OPERAČNÍ SYSTÉM VIRTUÁLNÍHO POČÍTAČE | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení z provozu | Vysoká dostupnost |  Dostupnost na webu Azure portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 <br> (Preview) |HDP 3.0 |Ubuntu 16.0.4 LTS |24. září 2018 | | |Ano |Ano |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4. dubna 2017 | | |Ano |Ano |
| HDInsight 3.5 <br> (Spark) * |HDP 2.6 |Ubuntu 16.0.4 LTS |30. září 2016 |13. března 2019 |13. března 2019 |Ano |Ano |

*&ast; Podpora HDInsight 3.5 se rozšířilo pouze pro typy clusterů Spark*

> [!NOTE]
> Za podporu pro verzi vypršela platnost, nemusí být k dispozici prostřednictvím portálu Microsoft Azure. Ale i nadále být k dispozici pomocí verze clusteru `Version` parametr v prostředí Windows PowerShell [New-AzureRmHDInsightCluster](https://docs.microsoft.com/powershell/module/azurerm.hdinsight/new-azurermhdinsightcluster) příkazu a sady SDK .NET až do verze vyřazením.
>

### <a name="retired-versions"></a>Vyřazené verze

Následující tabulka uvádí verze HDInsight, které jsou **není** k dispozici na webu Azure Portal.

| Verze HDInsight | Verze HDP | OPERAČNÍ SYSTÉM VIRTUÁLNÍHO POČÍTAČE | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení z provozu | Vysoká dostupnost |  Dostupnost na webu Azure portal | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 <br> (Bez Spark) |HDP 2.5 |Ubuntu 16.0.4 LTS |30. září 2016 |5. září 2017 |28. června 2018 |Ano |Ne |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29. března 2016 |29. prosince 2016 |9. ledna 2018 |Ano |Ne |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2. prosince 2015 |27. června 2016 |31. července 2018 |Ano |Ne |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2. prosince 2015 |27. června 2016 |31. července 2017 |Ano |Ne |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS nebo Windows Server 2012 R2 |18. února 2015 |1. března 2016 |1. dubna 2017 |Ano |Ne |
| HDInsight verze 3.1 |HDP 2.1 |Windows Server 2012 R2 |24. června 2014 |18. května 2015 |30. června 2016 |Ano |Ne |
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

### <a name="support-for-azure-data-lake-store"></a>Podpora pro Azure Data Lake Store

Enterprise Security Package podporuje, pomocí Azure Data Lake Store jako primární úložiště a doplňkové úložiště.

### <a name="pricing-and-sla"></a>Ceny a smlouva SLA
Informace o cenách a smlouva SLA pro Enterprise Security Package najdete v tématu [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Konec podpory HDInsight ve Windows
Microsoft Azure HDInsight verze 3.3 byl poslední verze HDInsight ve Windows. Datum vyřazení pro HDInsight ve Windows je 31. července 2018. Pokud máte všechny clustery HDInsight ve Windows 3.3 nebo starší, musí migrovat na HDInsight v Linuxu (HDInsight verze 3.5 nebo novější) před 31. července 2018. Migrace na operační systém Linux umožňuje zachovat možnost vytváření nebo změně velikosti vašich clusterů HDInsight. 27. června 2016 vypršela platnost podpory pro HDInsight verze 3.3 na Windows.

Spouští se s HDInsight verze 3.4, společnost Microsoft vydala pouze v operačním systému Linux HDInsight. V důsledku toho některé součásti v HDInsight jsou k dispozici pro Linux pouze. Patří mezi ně [Apache Ranger](https://ranger.apache.org/), [Apache Kafka](https://kafka.apache.org/), Interactive Query [Apache Spark](https://spark.apache.org/), HDInsight aplikace a Azure Data Lake Store jako primární soubor systému. Budoucí verze HDInsight jsou k dispozici pouze v operačním systému Linux. Nebude žádný budoucí verze HDInsight ve Windows. 

## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Co je na časové ose pro ukončení provozu služby HDInsight na Windows?
31. července 2018, je datum vyřazení pro HDInsight ve Windows. Pokud se liší pro vaši oblast datum plánovaného vyřazení, budete upozorněni samostatně. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Co je dopad ukončení provozu služby HDInsight na Windows pro stávající zákazníky?
Po vyřazení HDInsight ve Windows nelze vytvořit nový cluster HDInsight Windows nebo změňte velikost existujícího clusteru HDInsight Windows. 27. června 2016 vypršela platnost podpory pro HDInsight verze 3.3. Proto neexistuje žádná podpora nebo opravy chyb pro HDInsight 3.3 a předchozími verzemi. Budoucí verze HDInsight jsou k dispozici pouze v operačním systému Linux. Nebude žádný budoucí verze HDInsight ve Windows.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Jaké verze rozhraní HDInsight ve Windows se to týká?
Azure HDInsight verze 3.3 je poslední verze HDInsight pro Windows. Předtím, než byl vyřazen z provozu HDInsight ve Windows, musí migrovat všechny verze clusterů HDInsight Windows 3.3 nebo starší na HDInsight v Linuxu verze 3.5 nebo novější. Migrace clusterů HDInsight v Linuxu umožňuje zachovat možnost vytváření nových clusterů nebo změně velikosti stávajících clusterů. 

### <a name="what-do-i-need-to-do"></a>Co musím udělat?
Migrace clusterů HDInsight Windows na cluster HDInsight Linux podporované před 31. července 2018. Další informace najdete v [dokumentu migrace HDInsight](hdinsight-migrate-from-windows-to-linux.md). Podrobnosti o verzích HDInsight Azure najdete v tématu seznam [podporované verze](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Kde můžu najít typ operačního systému clusteru?
Na webu Azure Portal, přejděte na stránku přehled clusteru HDInsight a vyhledejte **typ clusteru** pod **Essentials**. Typ operačního systému clusteru jsou uvedené na této stránce. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Nelze migrovat do clusteru HDInsight Linux do 31. července 2018. Co je dopad na cluster HDInsight Windows?
V clusteru HDInsight Windows běží jako-se, ale nelze vytvořit nový cluster HDInsight Windows nebo změně velikosti existujícího clusteru HDInsight Windows. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Cluster obsahuje závislost .NET. Jak vyřešit tuto závislost na platformě Linux?
Vaše závislost clusteru Linux lze vyřešit pomocí [projekt Mono](http://www.mono-project.com/). Tato implementace open source technologie .NET je k dispozici u clusterů HDInsight Linux. Další informace najdete v [dokumentu migrace HDInsight](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Já jsem nového zákazníka pro HDInsight ve Windows. Jak můžete vytvořit cluster služby HDInsight Windows?
Od 3. července 2017 můžete pouze stávající zákazníci HDInsight Windows vytvářet nové Windows HDInsight clustery. Noví zákazníci nelze vytvořit cluster HDInsight Windows na webu Azure Portal pomocí Powershellu nebo sady SDK. Doporučujeme vám, že noví zákazníci vytvořit cluster HDInsight Linux. Stávající zákazníci tak můžou vytvářet nové Windows HDInsight clustery až do HDInsight ve Windows vyřazením. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Je zpoplatněné spojené s přesunem z HDInsight ve Windows pro HDInsight v Linuxu?
Ne, ceny je stejný pro HDInsight v obou operačním systému. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Jaké jsou výhody zákazníka související s přechodem na pouze pomocí HDInsight v Linuxu?
* Rychlejší doba uvedení na trh pro technologie open source velkých objemů dat ve službě HDInsight
* Velké komunity a ekosystém pro podporu
* Schopnost vykonávat aktivní přístup k vývoji open sourcové komunity pro Hadoop a dalších technologií pro velké objemy dat

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>HDInsight v Linuxu poskytuje další funkce nad rámec co je k dispozici v HDInsight ve Windows?
Spouští se s HDInsight verze 3.4, společnost Microsoft vydala pouze v operačním systému Linux HDInsight. V důsledku toho některé součásti v HDInsight jsou k dispozici pro Linux pouze. Patří mezi ně Apache Ranger, Kafka, Interactive Query, Spark, aplikací HDInsight a Azure Data Lake Store jako primární soubor systému. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Smlouva o úrovni služeb pro verze clusteru HDInsight
Smlouva o úrovni služeb (SLA) je definován z hlediska _podporu okno_. Okno podpory je časový úsek, který je ve verzi clusteru HDInsight nepodporuje Microsoft zákaznický servis a podporu. Pokud je verze _podporují datum vypršení platnosti_ , který je předán, HDInsight cluster je mimo časový interval pro podporu. Další informace o podporovaných verzích najdete v seznamu [podporované verze clusteru HDInsight](hdinsight-migrate-from-windows-to-linux.md). Datum vypršení platnosti podpora pro zadanou HDInsight verze X (po je dostupná novější verze X + 1) se počítá jako později z:  

* Vzorec 1: Přidejte k datu, kdy byla vydána verze clusteru HDInsight X 180 dnů.
* Vzorec 2: Přidání 90 dní k datu, kdy verze clusteru HDInsight X + 1 je k dispozici na webu Azure portal.

_Vyřazením_ je datum, po jejímž uplynutí verze clusteru nelze pro HDInsight. Od 31. července 2017, nelze změnit velikost clusteru služby HDInsight po datu vyřazení z provozu. 

> [!NOTE]
> Clustery HDInsight Windows (včetně verze, 2.1, 3.0, 3.1, 3.2 nebo 3.3) spustit v Azure hostovaný operační systém řady verze 4, který používá 64bitovou verzi systému Windows Server 2012 R2. Azure hostovaný operační systém řady verze 4 podporuje rozhraní .NET Framework verze 4.0, 4.5, 4.5.1 a 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Poznámky k spojené s HDInsight verze verzi Hortonworks

V části obsahuje odkazy na poznámky k verzi pro datovou platformou Hortonworks distribucích a Apache součásti, které se používají s HDInsight.
* Verze clusteru HDInsight 4.0 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 3.0](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.0/release-notes/content/relnotes.html)
* Verze clusteru HDInsight 3.6 využívá distribuci systému Hadoop, který je založen na [datová platforma Hortonworks 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Verze clusteru HDInsight 3.5 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Verze clusteru HDInsight 3.5 není _výchozí_ cluster Hadoop, který se vytvoří na webu Azure Portal.
* Verze clusteru HDInsight 3.4 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Verze clusteru HDInsight 3.3 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Zpráva k vydání verze Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) jsou k dispozici na webu Apache.
  * [Apache Hive zpráva k vydání verze](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) jsou k dispozici na webu Apache.
* Verze clusteru HDInsight 3.2 využívá distribuci systému Hadoop, který je založen na [datovou platformu Hortonworks 2.2][hdp-2-2].

  * Zpráva k vydání verze pro konkrétní komponenty Apache jsou dostupné takto: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [běžné](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), a [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Verze clusteru HDInsight 3.1 využívá distribuci systému Hadoop, který je založen na [datovou platformou Hortonworks 2.1.7][hdp-2-1-7]. Clustery HDInsight 3.1 vytvořené před listopadem, 7, 2014, jsou založeny na [datovou platformou Hortonworks 2.1.1][hdp-2-1-1].
* Verze clusteru HDInsight 3.0 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Verze clusteru HDInsight 2.1 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Verze clusteru HDInsight 1.6 využívá distribuci systému Hadoop, který je založen na [Hortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí konfigurace a virtuální počítač velikosti uzlů pro clustery
Následující tabulky uvádějí výchozí velikostí virtuálních počítačů (VM) pro clustery HDInsight.

> [!IMPORTANT]
> Pokud potřebujete více než 32 uzlů pracovního procesu v clusteru, musíte vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.
> 
> 

* Všechny podporované oblastech kromě Brazílie – jih a Japonsko – západ:

  | Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | ML Server |
  | --- | --- | --- | --- | --- | --- | --- |
  | Hlavní: výchozí velikosti virtuálního počítače |D3 v2 |D3 v2 | D13, D14 |A4 v2 |D12 v2 |D12 v2 |
  | Hlavní: doporučené velikosti virtuálních počítačů |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A4 v2, instance A8 v2, A2m v2 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Pracovních procesů: výchozí velikosti virtuálního počítače |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Pracovního procesu: doporučené velikosti virtuálních počítačů |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: výchozí velikosti virtuálního počítače | |A4 v2 | |A2 v2 | | |
  | ZooKeeper: doporučené velikosti virtuálních počítačů | |A4 v2, instance A8 v2, A2m v2 | | A2 v2, A4 v2, instance A8 v2 | | |
  | Edge: výchozí velikosti virtuálního počítače | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Edge: Doporučená velikost virtuálního počítače | | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Brazílie – jih a Japonsko – západ pouze (žádné velikosti v2):

  | Typ clusteru | Hadoop | HBase | Interaktivní dotaz |Storm | Spark | Služby ML |
  | --- | --- | --- | --- | --- | --- | --- |
  | Hlavní: výchozí velikosti virtuálního počítače |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | Hlavní: doporučené velikosti virtuálních počítačů |D3, D4, D12 |D3, D4, D12  | D13, D14 |A3, A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Pracovních procesů: výchozí velikosti virtuálního počítače |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Pracovního procesu: doporučené velikosti virtuálních počítačů |D3, D4, D12 |D3, D4, D12  | D13, D14 |D3, D4, D12 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |
  | ZooKeeper: výchozí velikosti virtuálního počítače | |A2 | | A2 | | |
  | ZooKeeper: doporučené velikosti virtuálních počítačů | |A2, A3, A4 | |A2, A3, A4 | | |
  | Edge: velikosti virtuálních počítačů výchozí | | | | | |Windows: D12; Linux: D4 |
  | Edge: doporučené velikosti virtuálních počítačů | | | | | |Windows: D12, D13, D14; Linux: D4, D12, D13, D14 |

> [!NOTE]
> - Hlavní se označuje jako *Nimbus* typ clusteru Storm.
> - Pracovního procesu se označuje jako *Supervisor* typ clusteru Storm.
> - Pracovního procesu se označuje jako *oblasti* HBase typ clusteru.

## <a name="next-steps"></a>Další postup
- [Nastavení clusteru pro Apache Hadoop, Spark a další informace o HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Práce v Apache Hadoop v HDInsight z Windows PC](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.9/bk_HDP_RelNotes/content/ch_relnotes_v229.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: https://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.1.1.16_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
