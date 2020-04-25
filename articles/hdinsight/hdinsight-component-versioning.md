---
title: Apache Hadoop součásti a verze – Azure HDInsight
description: Přečtěte si informace o komponentách a verzích Apache Hadoop ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/09/2020
ms.openlocfilehash: e27b65346ba8d48e36d080d66754db9605259e2b
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82144403"
---
# <a name="what-are-the-apache-hadoop-components-and-versions-available-with-hdinsight"></a>Jaké jsou součásti Apache Hadoop a verze, které jsou k dispozici v HDInsight?

Přečtěte si informace o komponentách a verzích prostředí [Apache Hadoop](https://hadoop.apache.org/) v Microsoft Azure HDInsight a balíček zabezpečení podniku. Přečtěte si také, jak kontrolovat verze komponent Hadoop ve službě HDInsight.

## <a name="apache-hadoop-components-available-with-different-hdinsight-versions"></a>Apache Hadoop komponenty k dispozici v různých verzích HDInsight

Azure HDInsight podporuje víc verzí clusterů Hadoop, které se dají kdykoli nasadit. 4. dubna 2017 je výchozí verze clusteru používaná službou Azure HDInsight 3,6.

Verze komponent přidružených ke clusterům HDInsight jsou uvedené v následující tabulce:

> [!NOTE]  
> Výchozí verze služby HDInsight se může změnit bez předchozího upozornění. Pokud máte závislost verze, určete verzi HDInsight při vytváření clusterů pomocí sady .NET SDK s Azure PowerShell a Azure Classic CLI.

| Součást              | HDInsight 4.0 | HDInsight 3,6 (výchozí)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop a PŘÍZe | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache prasete             | 0.16.0        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 on ESP Interactive Query) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.0.2         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | bodu         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.3.1, 2,4    | 2.3.0, 2.2.0, 2.1.0         |
| Apache Livy            | 0,5           | 0,4, 0,4, 0,3               |
| Apache Kafka           | 1.1.1, 2,1    | 1,1, 1,0 * (viz poznámka níže) |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> Kvůli důležitým informacím o výkonu systému skončila podpora pro Kafka verze 0,10 v březnu 2019.

## <a name="check-for-current-hadoop-component-version-information"></a>Vyhledat aktuální informace o verzi komponenty Hadoop

Verze komponent prostředí Hadoop spojené s verzemi clusterů HDInsight se můžou měnit s aktualizacemi služby HDInsight. Chcete-li zkontrolovat komponenty Hadoop a ověřit, které verze jsou používány pro cluster, použijte REST API Ambari. Příkaz **GetComponentInformation** načte informace o součástech služby. Podrobnosti najdete v dokumentaci k [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Poznámky k verzi

Další poznámky k verzi pro nejnovější verze služby HDInsight najdete v tématu [poznámky k verzi HDInsight](hdinsight-release-notes.md) .

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Podpora vypršení platnosti a vyřazení verzí HDInsight

**Vypršení platnosti podpory** znamená, že společnost Microsoft už nebude poskytovat podporu pro zadanou verzi HDInsight. A nebude už k dispozici prostřednictvím Azure Portal pro vytváření clusteru. Tyto verze ale pořád můžou být vytvořené pomocí Azure CLI nebo různých sad SDK.

**Vyřazení** verze HDInsight znamená, že existující clustery budou i nadále běžet tak, jak jsou. Nové clustery této verze ale nejdou vytvářet jakýmkoli způsobem (včetně CLI a sad SDK). Další funkce roviny ovládacího prvku (například ruční škálování a automatické škálování) nemusí po vyřazení verze fungovat ani po vyřazení verzí. Podpora není k dispozici pro vyřazené verze.

V následujících tabulkách jsou uvedeny verze HDInsight. K dispozici jsou také data vypršení platnosti podpory a datum vyřazení, pokud jsou známy.

### <a name="available-versions"></a>Dostupné verze

V následující tabulce jsou uvedeny verze HDInsight, které jsou k dispozici v Azure Portal a dalších metodách nasazení, jako jsou PowerShell a .NET SDK.

| Verze HDInsight | Operační systém virtuálního počítače | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  Dostupnost v Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |Září 24, 2018 | | |Ano |Ano |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4. dubna 2017 | 31. prosince 2020 |31. prosince 2020 |Ano |Ano |

Spark 2,1, 2,2 & Kafka 1,0 Podpora skončí 30. června 2020.

> [!NOTE]  
> Po vypršení platnosti podpory pro verzi nemusí být k dispozici prostřednictvím portál Microsoft Azure. Verze clusteru budou ale dál dostupné pomocí `Version` parametru v příkazu Windows PowerShellu [New-AZHDINSIGHTCLUSTER](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) a .NET SDK až do data vyřazení verze.

### <a name="retired-versions"></a>Vyřazené verze

V následující tabulce jsou uvedeny verze HDInsight, které **nejsou** k dispozici v Azure Portal.

| Verze HDInsight | Verze HDP | Operační systém virtuálního počítače | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  Dostupnost na Azure Portal |
| --- | --- | --- | --- | --- | --- | --- | --- |
| HDInsight 3,5 |HDP 2,5 |Ubuntu 16.0.4 LTS |30. září 2016 |5. září 2017 |28. června 2018 |Ano |Ne |
| HDInsight 3,4 |HDP 2,4 |Ubuntu 14.0.4 LTS |29. března 2016 |29. prosince 2016 |9. ledna 2018 |Ano |Ne |
| HDInsight 3,3 |HDP 2,3 |Windows Server 2012 R2 |2. prosince 2015 |Červen 27, 2016 |31. července 2018 |Ano |Ne |
| HDInsight 3,3 |HDP 2,3 |Ubuntu 14.0.4 LTS |2. prosince 2015 |Červen 27, 2016 |31. července 2017 |Ano |Ne |
| HDInsight 3,2 |HDP 2,2 |Ubuntu 12,04 LTS nebo Windows Server 2012 R2 |18. února 2015 |1. března 2016 |1. dubna 2017 |Ano |Ne |
| HDInsight 3,1 |HDP 2,1 |Windows Server 2012 R2 |24. června 2014 |18. května 2015 |30. června 2016 |Ano |Ne |
| HDInsight 3,0 |HDP 2,0 |Windows Server 2012 R2 |11. února 2014 |17. září 2014 |30. června 2015 |Ano |Ne |
| HDInsight 2,1 |HDP 1,3 |Windows Server 2012 R2 |28. října 2013 |12. května 2014 |31. května 2015 |Ano |Ne |
| HDInsight 1,6 |HDP 1,1 | |28. října 2013 |26. dubna 2014 |31. května 2015 |Ne |Ne |

> [!NOTE]  
> Clustery s vysokou dostupností se dvěma hlavními uzly se ve výchozím nastavení nasazují pro HDInsight verze 2,1 a novější. Pro clustery HDInsight verze 1,6 nejsou k dispozici.

## <a name="enterprise-security-package-for-hdinsight"></a>Balíček zabezpečení podniku pro HDInsight

Enterprise Security je volitelný balíček, který můžete přidat do clusteru HDInsight jako součást pracovního postupu vytvoření clusteru. Balíček zabezpečení podniku podporuje:

- Integrace se službou Active Directory pro ověřování.

    V minulosti jste vytvořili clustery HDInsight s místními uživateli a místními uživateli SSH. Místní uživatel správce má přístup ke všem souborům, složkám, tabulkám a sloupcům.  Pomocí Balíček zabezpečení podniku můžete povolit řízení přístupu na základě role integrací služby HDInsight se službou Active Directory. Který zahrnuje místní službu Active Directory Azure Active Directory Domain Services. Nebo Active Directory na virtuálním počítači s IaaS. Správce domény v clusteru může uživatelům udělit vlastní uživatelské jméno a heslo v podnikovém (doméně).

    Další informace naleznete v tématu:

    - [Úvod do zabezpečení Apache Hadoop s využitím clusterů HDInsight připojených k doméně](./domain-joined/hdinsight-security-overview.md)
    - [Plánování clusterů Apache Hadoop připojených k doméně Azure v HDInsight](./domain-joined/apache-domain-joined-architecture.md)
    - [Konfigurace prostředí sandboxu připojeného k doméně](./domain-joined/apache-domain-joined-configure.md)
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
- Kafka
- HBase
- Interaktivní dotaz

### <a name="support-for-azure-data-lake-storage"></a>Podpora Azure Data Lake Storage

Balíček zabezpečení podniku podporuje použití Azure Data Lake Storage jako primárního úložiště i úložiště doplňku.

### <a name="pricing-and-service-level-agreement-sla"></a>Ceny a smlouvy o úrovni služeb (SLA)

Informace o cenách a smlouvě SLA pro Balíček zabezpečení podniku najdete v tématu [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Smlouva o úrovni služeb pro verze clusteru HDInsight

Smlouva o úrovni služeb (SLA) je definovaná jako _okno podpory_. Okno podpory je časové období, ve `Microsoft Customer Service and Support`kterém je verze HDInsight podporovaná. Pokud má verze prošlé _Datum vypršení platnosti podpory_, cluster HDInsight je mimo okno podpory. Podpora vypršení platnosti pro HDInsight verze X (po dostupnosti novější verze X + 1) je pozdější:  

- Vzorec 1: přidejte 180 dní do data, kdy byl vydán cluster HDInsight verze X.
- Vzorec 2: přidejte 90 dní do data, kdy je ve Azure Portal k dispozici cluster HDInsight verze X + 1.

_Datum vyřazení_ je datum, po jehož uplynutí nelze ve službě HDInsight vytvořit verzi clusteru. Od 31. července 2017 nemůžete změnit velikost clusteru HDInsight po jeho datu vyřazení.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí konfigurace uzlů a velikosti virtuálních počítačů pro clustery

Další informace o tom, které SKU virtuálních počítačů vybrat pro váš cluster, najdete v tématu [Podrobnosti o konfiguraci clusteru Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Další kroky

- [Nastavení clusteru pro Apache Hadoop, Spark a další funkce v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
- [Poznámky k verzi Hortonworks přidružené k verzím Azure HDInsight](./hortonworks-release-notes.md)
