---
title: Hadoop součásti a verze - Azure HDInsight | Microsoft Docs
description: Další komponent systému Hadoop a verzí v HDInsight a úrovně služeb, která je k dispozici v této cloudové distribuce softwaru Hortonworks Data Platform.
keywords: hadoop verze součástí ekosystému hadoop, komponent systému hadoop, jak zkontrolovat hadoop verze
services: hdinsight
editor: cgronlun
manager: asadk
author: kkampf
tags: azure-portal
documentationcenter: ''
ms.assetid: 367b3f4a-f7d3-4e59-abd0-5dc59576f1ff
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: kakampf
ms.openlocfilehash: f79d80a2743fb1a46fd2f17ec3b99d97e3b3d8d8
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111020"
---
# <a name="what-are-the-hadoop-components-and-versions-available-with-hdinsight"></a>Co jsou komponent systému Hadoop a verze, které jsou k dispozici v prostředí HDInsight?

Další informace o součásti ekosystém Apache Hadoop a verzí v Microsoft Azure HDInsight, stejně jako balíček zabezpečení organizace. Také informace o zjišťování verze součástí Hadoop v HDInsight. 

Každá verze HDInsight je distribuce cloudu verze Hortonworks Data Platform (HDP).

## <a name="hadoop-components-available-with-different-hdinsight-versions"></a>Hadoop součásti, které jsou k dispozici s různými verzemi HDInsight
Azure HDInsight podporuje více verzích clusterů Hadoop, které lze nasadit kdykoli. Každou verzi volbu vytvoří na konkrétní verzi distribuce softwaru HDP a sadu součástí, které jsou obsaženy v rámci této distribuce. Od verze 4. dubna 2017 je výchozí verze clusteru používá Azure HDInsight je 3.6 a je založená na softwaru HDP 2.6.

Verze součástí přidružené verze clusteru HDInsight jsou uvedené v následující tabulce: 

> [!NOTE]
> Výchozí verze pro službu HDInsight mohou změnit bez předchozího upozornění. Pokud máte verzi závislostí, zadejte verzi HDInsight, při vytváření clusterů pomocí .NET SDK služby Azure PowerShell a rozhraní příkazového řádku Azure.

| Komponenta | HDInsight 3.6 (výchozí) | HDInsight 3.5 | HDInsight 3.4 | HDInsight 3.3 | HDInsight 3.2 | HDInsight 3.1 | HDInsight 3.0 |
| --- | --- | --- | --- | --- | --- | --- |--- |
| Datová platforma Hortonworks |2.6 |2.5 |2.4 |2.3 |2.2 |2.1.7 |2.0 |
| Apache Hadoop a YARN |2.7.3 |2.7.3 |2.7.1 |2.7.1 |2.6.0 |2.4.0 |2.2.0 |
| Apache Tez |0.7.0 |0.7.0 |0.7.0 |0.7.0 |0.5.2 |0.4.0 |-|
| Apache Pig |0.16.0 |0.16.0 |0.15.0 |0.15.0 |0.14.0 |0.12.1 |0.12.0 |
| Apache Hive a HCatalog |1.2.1 |1.2.1 |1.2.1 |1.2.1 |0.14.0 |0.13.1 |0.12.0 |
| Apache Hive2 | 2.1.0 |-|-|-|-|-|-|
| Apache Tez Hive2 | 0.8.4 |-|-|-|-|-|-|
| Apache Ranger | 0.7.0 |0.6.0 |-|-|-|-|-|
| Apache HBase |1.1.2 |1.1.2 |1.1.2 |1.1.1 |0.98.4 |0.98.0 |-|
| Apache Sqoop |1.4.6 |1.4.6 |1.4.6 |1.4.6 |1.4.5 |1.4.4 |1.4.4 |
| Apache Oozie |4.2.0 |4.2.0 |4.2.0 |4.2.0 |4.1.0 |4.0.0 |4.0.0 |
| Apache Zookeeper |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.6 |3.4.5 |3.4.5 |
| Apache Storm |1.1.0 |1.0.1 |0.10.0 |0.10.0 |0.9.3 |0.9.1 |-|
| Apache Mahout |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0+ |0.9.0 |0.9.0 |-|
| Apache Phoenix |4.7.0 |4.7.0 |4.4.0 |4.4.0 |4.2.0 |4.0.0.2.1.7.0-2162 |-|
| Apache Spark |2.3.0, 2.2.0, 2.1.0 |1.6.2, 2.0 |1.6.0 |1.5.2 |1.3.1 (jenom Windows) |-|-|
| Apache Livy |0.4 |0,3 |0,3 |0.2 |-|-|-|
| Apache Kafka | 1.0, 0.10.1 | 0.10.0 | 0.9.0 |-|-|-|-|
| Apache Ambari | 2.6.0 | 2.4.0 | 2.2.1 | 2.1.0 |-|-|-|
| Apache Zeppelin | 0.7.0 |-|-|-|-|-|-|
| Mono |4.2.1 |4.2.1 |3.2.8 |-|-|-|
| Apache posuvníku | 0.92.0 |-|-|-|-|-|-|

## <a name="check-for-current-hadoop-component-version-information"></a>Zkontrolujte informace o verzi součástí aktuální Hadoop

Verze součástí ekosystému Hadoop přidružené verze clusteru HDInsight můžete změnit pomocí aktualizací na HDInsight. Ke kontrole komponenty Hadoop a k ověření, která verze se používá pro cluster s podporou použít Ambari REST API. **GetComponentInformation** příkaz načte informace o součásti služby. Podrobnosti najdete v tématu [Ambari dokumentace][ambari-docs].

Pro clustery systému Windows Další způsob kontroly verze komponenty je zkontrolujte obsah adresáře C:\apps\dist\ a přihlaste se ke clusteru pomocí vzdálené plochy.

> [!IMPORTANT]
> Linux je jenom operační systém používaný v HDInsight verze 3.4 nebo novější. Další informace najdete v tématu [vyřazení Windows v HDInsight](#hdinsight-windows-retirement).

### <a name="release-notes"></a>Poznámky k verzi

V tématu [poznámky k verzi HDInsight](hdinsight-release-notes.md) pro další poznámky na nejnovější verze služby HDInsight.

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight
V následujících tabulkách jsou uvedeny verzích HDInsight. Verze softwaru HDP, které odpovídají každé HDInsight verze jsou uvedeny společně s daty verzi produktu. Podpora vypršení platnosti a vyřazení kalendářní data jsou tu taky, při jejich jste známé.

### <a name="available-versions"></a>K dispozici verze

Následující tabulka uvádí verze služby HDInsight, které jsou k dispozici v portálu Azure, jakož i jiné metody nasazení, třeba prostředí PowerShell a .NET SDK.

| HDInsight verze | Verze softwaru HDP | OPERAČNÍM SYSTÉMEM VIRTUÁLNÍHO POČÍTAČE | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  K dispozici na portálu Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.6 |HDP 2.6 |Ubuntu 16.0.4 LTS |4. dubna 2017 | | |Ano |Ano |
| HDInsight 3.3 |HDP 2.3 |Windows Server 2012 R2 |2. prosince 2015 |27. června 2016 |31. července 2018 |Ano |Ne |

> [!NOTE]
> Po podpora pro verze vypršela platnost, nemusí být k dispozici prostřednictvím portálu Microsoft Azure. Ale verzích clusterů nadále k dispozici pomocí `Version` parametr v prostředí Windows PowerShell [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) příkaz a .NET SDK dokud verze datu vyřazení.
>

### <a name="retired-versions"></a>Vyřazené verze

Následující tabulka uvádí verze HDInsight, které jsou **není** k dispozici na portálu Azure.

| HDInsight verze | Verze softwaru HDP | OPERAČNÍM SYSTÉMEM VIRTUÁLNÍHO POČÍTAČE | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  K dispozici na portálu Azure | 
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3.5 |HDP 2.5 |Ubuntu 16.0.4 LTS |30. září 2016 |5 září 2017 |28 června 2018 |Ano |Ne |
| HDInsight 3.4 |HDP 2.4 |Ubuntu 14.0.4 LTS |29. března 2016 |29. prosinci 2016 |9 leden 2018 |Ano |Ne |
| HDInsight 3.3 |HDP 2.3 |Ubuntu 14.0.4 LTS |2. prosince 2015 |27. června 2016 |31. července 2017 |Ano |Ne |
| HDInsight 3.2 |HDP 2.2 |Ubuntu 12.04 LTS nebo Windows Server 2012 R2 |18 únor 2015 |1. března 2016 |1. dubna 2017 |Ano |Ne |
| HDInsight 3.1 |HDP 2.1 |Windows Server 2012 R2 |24. června 2014 |18 květen 2015 |30. června 2016 |Ano |Ne |
| HDInsight 3.0 |HDP 2.0 |Windows Server 2012 R2 |11. února 2014 |17. září 2014 |30. června 2015 |Ano |Ne |
| HDInsight 2.1 |HDP 1.3 |Windows Server 2012 R2 |28 říjen 2013 |12 může 2014 |31 květen 2015 |Ano |Ne |
| HDInsight 1.6 |HDP 1.1 | |28 říjen 2013 |26. dubna 2014 |31 květen 2015 |Ne |Ne |

> [!NOTE]
> Vysoce dostupný clustery se dvěma uzly head nasazených ve výchozím nastavení pro HDInsight verze 2.1 nebo novější. Nejsou k dispozici pro verzi 1.6 clustery služby HDInsight.

## <a name="enterprise-security-package-for-hdinsight"></a>Balíček zabezpečení Enterprise pro HDInsight

Podnikové zabezpečení je volitelné balíček, který můžete přidat jako součást pracovního postupu vytvoření clusteru v clusteru HDInsight. Balíček zabezpečení Enterprise podporuje:

- Integrace se službou Active Directory pro ověřování.

    V minulosti můžete vytvořit pouze clustery HDInsight s místní správce a místní uživatel SSH. Všechny soubory, složky, tabulky a sloupce, můžete přístup k místní správce.  Pomocí balíčku zabezpečení organizace, můžete povolit řízení přístupu na základě rolí díky integraci clustery HDInsight se vlastní služby Active Directory, mezi které patří místní služby Active Directory, Azure Active Directory Domain Services nebo služby Active Directory na IaaS virtuální počítač. Správce domény na clusteru můžete udělit uživatelům používat jejich vlastní podniková (doména) uživatelské jméno a heslo pro přístup ke clusteru. 

    Další informace naleznete v tématu:

    - [Úvod do Hadoop zabezpečení s clustery HDInsight připojený k doméně](./domain-joined/apache-domain-joined-introduction.md)
    - [Plánování Azure připojené k doméně clusterů systému Hadoop v HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurace prostředí izolovaného prostoru připojený k doméně](./domain-joined/apache-domain-joined-configure.md)
    - [Konfigurace clusterů HDInsight připojený k doméně pomocí nástroje Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)

- Ověřování dat

    - Integrace s Apache škálu pro autorizaci pro Hive, Spark SQL a Yarn fronty.
    - Můžete nastavit řízení přístupu k souborům a složkám.

    Další informace naleznete v tématu:

    - [Nakonfigurovat zásady Hive v HDInsight připojený k doméně](./domain-joined/apache-domain-joined-run-hive.md)

- Zobrazte protokoly auditu monitorování přístupů a nakonfigurované zásady. 

### <a name="supported-cluster-types"></a>Typy podporované clusteru

V současné době pouze následující typy clusteru podporují balíček zabezpečení organizace:

- Hadoop (pouze HDInsight 3.6)
- Spark
- Interaktivní dotaz

### <a name="support-for-azure-data-lake-store"></a>Podpora pro Azure Data Lake Store

Balíček zabezpečení organizace podporuje, pomocí Azure Data Lake Store jako primárního úložiště a rozšíření úložiště.

### <a name="pricing-and-sla"></a>Ceny a smlouva SLA
Informace o cenách a SLA pro balíček Enterprise zabezpečení najdete v tématu [HDInsight ceny](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="hdinsight-windows-retirement"></a>Konec podpory HDInsight ve Windows
Microsoft Azure HDInsight verze 3.3 byl poslední verze služby HDInsight v systému Windows. Datum vyřazení pro HDInsight v systému Windows je 31 července 2018. Pokud máte všechny clustery HDInsight v systému Windows 3.3 nebo dřívější, musíte před 31 července 2018 migrovat do HDInsight v Linuxu (HDInsight verze 3.5 nebo novější). Migrace na operační systém Linux umožňuje zachovat umožňuje vytvořit nebo změnit velikost clusterů HDInsight. 27. června 2016 vypršela platnost podpory pro HDInsight verze 3.3 v systému Windows.

Počínaje HDInsight verze 3.4, společnost Microsoft vydala HDInsight pouze na operační systém Linux. V důsledku toho některé součásti v HDInsight jsou k dispozici pro Linux jenom. Patří mezi ně Apache škálu, Kafka, interaktivní dotazu, Spark, aplikace HDInsight a Azure Data Lake Store jako systém primární soubor. Budoucích verzích HDInsight jsou dostupné pouze pro operační systém Linux. Budou existovat žádné budoucích verzích HDInsight v systému Windows. 

## <a name="faqs"></a>Nejčastější dotazy

### <a name="what-is-the-timeline-for-retiring-hdinsight-on-windows"></a>Co je k dispozici pro vyřazení HDInsight v systému Windows?
31. července 2018, je datum vyřazení pro HDInsight v systému Windows. Pokud plánované vyřazení datum se liší pro vaši oblast, upozornění se zobrazí samostatně. 

### <a name="what-is-the-impact-of-retiring-hdinsight-on-windows-for-existing-customers"></a>Co je dopad vyřazení HDInsight v systému Windows pro stávající zákazníky služby?
Po vyřazení HDInsight v systému Windows nelze vytvořit nový cluster HDInsight Windows, nebo přizpůsobit existující cluster HDInsight Windows. Podpora pro HDInsight verze 3.3 platnost 27. června 2016. Proto není žádná podpora nebo opravy chyb pro HDInsight 3.3 nebo starší verze. Budoucích verzích HDInsight jsou dostupné pouze pro operační systém Linux. Budou existovat žádné budoucích verzích HDInsight v systému Windows.
 
### <a name="which-versions-of-hdinsight-on-windows-are-affected"></a>Jaké verze HDInsight v systému Windows se problém týká?
Azure HDInsight verze 3.3 je poslední verzi HDInsight pro systém Windows. Předtím, než je vyřazeno HDInsight v systému Windows, všechny verze HDInsight Windows clustery 3.3 nebo dřívější musí být migrovány do HDInsight v Linuxu verze 3.5 nebo novější. Migrace clusterů do HDInsight v Linuxu umožňuje zachovat možnost vytvoření nových clusterů nebo změnit velikost stávajících clusterů. 

### <a name="what-do-i-need-to-do"></a>Co je potřeba udělat?
Migrace clusterů HDInsight Windows do podporovaných clusteru HDInsight Linux před 31 července 2018. Další informace v [dokumentu migrace HDInsight](hdinsight-migrate-from-windows-to-linux.md). Podrobnosti o Azure HDInsight verze najdete v tématu seznam [podporované verze](hdinsight-component-versioning.md#supported-hdinsight-versions). 

### <a name="where-do-i-find-the-cluster-os-type"></a>Kde typ operačního systému clusteru
Na portálu Azure přejděte na stránku přehled HDInsight Cluster a vyhledejte **clusteru typ** pod **Essentials**. Typy operačního systému clusteru jsou uvedené na této stránce. 

### <a name="i-cant-migrate-to-an-hdinsight-linux-cluster-by-july-31-2018-what-is-the-impact-to-my-hdinsight-windows-cluster"></a>Nelze migrovat do clusteru služby HDInsight Linux podle 31 července 2018. Co je dopad na můj clusteru se systémem HDInsight Windows?
Cluster HDInsight Windows běží jako-se, ale nelze vytvořit nový cluster HDInsight Windows, nebo přizpůsobit existující cluster HDInsight Windows. 

### <a name="my-cluster-has-a-net-dependency-how-do-i-resolve-this-dependency-on-linux"></a>Moje clusteru má závislost na rozhraní .NET. Jak lze vyřešit tuto závislost na platformě Linux?
Vaše závislost clusteru Linux lze vyřešit pomocí [Mono projektu](http://www.mono-project.com/). Tato implementace open-source .NET je k dispozici u clusterů HDInsight Linux. Další informace v [dokumentu migrace HDInsight](hdinsight-migrate-from-windows-to-linux.md). 

### <a name="im-a-new-customer-for-hdinsight-on-windows-how-can-i-create-an-hdinsight-windows-cluster"></a>Jsem nového odběratele pro HDInsight v systému Windows. Jak můžete vytvořit cluster služby HDInsight Windows?
Od verze 3. července 2017 mohou pouze stávající zákazníky služby HDInsight Windows vytvářet nová okna HDInsight clustery. Nové zákazníky nelze vytvořit cluster služby HDInsight Windows na portálu Azure pomocí prostředí PowerShell nebo sady SDK. Doporučujeme vám, že nové zákazníky vytvoření clusteru Linux HDInsight. Stávající zákazníky služby můžete vytvořit nové HDInsight Windows clustery až HDInsight v systému Windows datu vyřazení. 

### <a name="is-there-a-pricing-impact-associated-with-moving-from-hdinsight-on-windows-to-hdinsight-on-linux"></a>Je k dispozici cenovou dopad přidružené přechod z prostředí HDInsight v systému Windows do HDInsight v Linuxu?
Ne, cenách je stejný pro HDInsight na buď operačního systému. 

### <a name="what-are-the-customer-advantages-associated-with-the-move-to-only-using-hdinsight-on-linux"></a>Jaké jsou výhody zákazníka spojené s přechodem na jenom pomocí HDInsight v Linuxu?
* Rychlejší čas na trh pro technologie open source velkých objemů dat pomocí služby HDInsight
* Velké komunity a ekosystém pro podporu
* Schopnost vykonávat active vývoj komunitou open source pro Hadoop a další technologie velkých objemů dat

### <a name="does-hdinsight-on-linux-provide-additional-functionality-beyond-what-is-available-in-hdinsight-on-windows"></a>Poskytuje prostředí HDInsight v Linuxu další funkce nad rámec dostupné v prostředí HDInsight v systému Windows?
Počínaje HDInsight verze 3.4, společnost Microsoft vydala HDInsight pouze na operační systém Linux. V důsledku toho některé součásti v HDInsight jsou k dispozici pro Linux jenom. Patří mezi ně Apache škálu, Kafka, interaktivní dotazu, Spark, aplikace HDInsight a Azure Data Lake Store jako systém primární soubor. 

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Dohoda o úrovni služeb pro verze clusteru HDInsight
Smlouvu o úrovni služeb (SLA) je definován z hlediska _podporu okno_. Okno podporu je doba, kterou verze clusteru HDInsight podporuje Microsoft zákaznický servis a podporu. Pokud má verzi _podporu datum vypršení platnosti_ , byla úspěšná, HDInsight cluster je mimo okno podpory. Další informace o podporovaných verzích najdete v seznamu [podporované verze clusteru HDInsight](hdinsight-migrate-from-windows-to-linux.md). Datum vypršení platnosti podpora pro zadanou HDInsight verze X (po je k dispozici novější verze X + 1) se počítá jako později z:  

* Vzorec 1: Přidání 180 dní k datu, kdy byl vydán verze clusteru HDInsight X.
* Vzorec 2: Přidejte 90 dní k datu, když je k dispozici na portálu Azure verze clusteru HDInsight X + 1.

_Datu vyřazení_ je datum, po jejímž uplynutí nelze vytvořit verze clusteru HDInsight. Od 31 července 2017, nelze změnit velikost clusteru služby HDInsight po datu jeho vyřazení. 

> [!NOTE]
> Clustery HDInsight Windows (včetně verze 2.1, 3.0, 3.1, 3.2 a 3.3) spustit v Azure hostovaného operačního systému rodiny verze 4, který používá 64bitová verze systému Windows Server 2012 R2. Skupina Azure hostovaných operačních systémů verze 4 podporuje rozhraní .NET Framework verze 4.0, 4.5, 4.5.1 a 4.5.2.

## <a name="hortonworks-release-notes-associated-with-hdinsight-versions"></a>Poznámky k spojené s HDInsight verze verzi Hortonworks

Tato část poskytuje odkazy na poznámky k verzi pro distribuce softwaru Hortonworks Data Platform a Apache součásti, které se používají v prostředí HDInsight.
* Verze clusteru HDInsight 3.6 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 2.6](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.6.0/bk_release-notes/content/ch_relnotes.html).
* Verze clusteru HDInsight 3.5 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 2.5](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.5.0/bk_release-notes/content/ch_relnotes_v250.html). Verze clusteru HDInsight 3.5 je _výchozí_ cluster Hadoop, který je vytvořený na portálu Azure.
* Verze clusteru HDInsight 3.4 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 2.4](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.4.0/bk_HDP_RelNotes/content/ch_relnotes_v240.html).
* Verze clusteru HDInsight 3.3 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html).

  * [Poznámky k verzi Apache Storm](https://storm.apache.org/2015/11/05/storm0100-released.html) jsou k dispozici na webu Apache.
  * [Apache Hive poznámky k verzi](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843) jsou k dispozici na webu Apache.
* Verze clusteru HDInsight 3.2 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 2.2][hdp-2-2].

  * Release notes for specific Apache components are available as follows: [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), and [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).
* Verze clusteru HDInsight verze 3.1 používá distribuce Hadoop, která je založena na [softwaru Hortonworks Data Platform 2.1.7][hdp-2-1-7]. Clustery HDInsight 3.1 vytvořené před listopadu, 7, 2014, jsou založeny na [softwaru Hortonworks Data Platform 2.1.1][hdp-2-1-1].
* Verze clusteru HDInsight 3.0 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 2.0][hdp-2-0-8].
* Verze clusteru HDInsight 2.1 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 1.3][hdp-1-3-0].
* Verze clusteru HDInsight 1.6 používá distribuce Hadoop, která je založena na [Hortonworks Data Platform 1.1][hdp-1-1-0].






## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Velikost virtuálního počítače a konfigurace uzlu výchozí pro clustery
V následujících tabulkách jsou uvedeny výchozí velikosti virtuálního počítače (VM) pro clustery služby HDInsight.

> [!IMPORTANT]
> Pokud potřebujete více než 32 uzlů pracovního procesu v clusteru, je třeba vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.
> 
> 

* Všechny podporované oblasti s výjimkou Brazílie – jih a Japonsko – západ:

  | Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | ML serveru |
  | --- | --- | --- | --- | --- | --- | --- |
  | HEAD: velikost virtuálního počítače výchozí |D3 v2 |D3 v2 | D13, D14 |A3 |D12 v2 |D12 v2 |
  | HEAD: doporučené velikosti virtuálních počítačů |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |A3 A4, A5 |D12 v2, D13 v2, D14 v2 |D12 v2, D13 v2, D14 v2 |
  | Worker: velikost virtuálního počítače výchozí |D3 v2 |D3 v2  | D13, D14 |D3 v2 |Windows: D12 v2; Linux: D4 v2 |Windows: D12 v2; Linux: D4 v2 |
  | Pracovní: doporučené velikosti virtuálních počítačů |D3 v2, D4 v2, D12 v2 |D3 v2, D4 v2, D12 v2  | D13, D14 |D3 v2, D4 v2, D12 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
  | ZooKeeper: velikost virtuálního počítače výchozí | |A3 | |A2 | | |
  | ZooKeeper: doporučené velikosti virtuálních počítačů | |A3 A4, A5 | | A2, A3, A4 | | |
  | Okraj: velikost virtuálního počítače výchozí | | | | | |Windows: D12 v2; Linux: D4 v2 |
  | Okraj: Doporučená velikost virtuálního počítače | | | | | |Windows: D12 v2, D13 v2, D14 v2; Linux: D4 v2, D12 v2, D13 v2, D14 v2 |
* Brazílie – jih a Japonsko – západ pouze (žádné velikosti v2):

  | Typ clusteru | Hadoop | HBase | Interaktivní dotaz |Storm | Spark | ML serveru |
  | --- | --- | --- | --- | --- | --- | --- |
  | HEAD: velikost virtuálního počítače výchozí |D3 |D3  | D13, D14 |A3 |D12 |D12 |
  | HEAD: doporučené velikosti virtuálních počítačů |D3, D4, D12 |D3, D4, D12  | D13, D14 |A3 A4, A5 |D12, D13, D14 |D12, D13, D14 |
  | Worker: velikost virtuálního počítače výchozí |D3 |D3  | D13, D14 |D3 |Windows: D12; Linux: D4 |Windows: D12; Linux: D4 |
  | Pracovní: doporučené velikosti virtuálních počítačů |D3, D4, D12 |D3, D4, D12  | D13, D14 |D3, D4, D12 |Windows: D12 D13, D14; Linux: D4, D14 D12 D13, |Windows: D12 D13, D14; Linux: D4, D14 D12 D13, |
  | ZooKeeper: velikost virtuálního počítače výchozí | |A2 | | A2 | | |
  | ZooKeeper: doporučené velikosti virtuálních počítačů | |A2, A3, A4 | |A2, A3, A4 | | |
  | Hraniční: velikosti virtuálních počítačů výchozí | | | | | |Windows: D12; Linux: D4 |
  | Okraj: doporučené velikosti virtuálních počítačů | | | | | |Windows: D12 D13, D14; Linux: D4, D14 D12 D13, |

> [!NOTE]
> - HEAD se označuje jako *Nimbus* Storm clusteru typu.
> - Pracovního procesu se označuje jako *nadřízeného* Storm clusteru typu.
> - Pracovního procesu se označuje jako *oblast* HBase clusteru typu.

## <a name="next-steps"></a>Další postup
- [Instalace clusteru pro Hadoop, Spark a další v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Práce v Hadoop v HDInsight ze systému Windows PC](hdinsight-hadoop-windows-tools.md)

[Supported HDInsight versions]:(#supported-hdinsight-versions)

[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/
