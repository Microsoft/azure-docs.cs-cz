---
title: Apache Hadoop součásti a verze – Azure HDInsight
description: Přečtěte si informace o komponentách a verzích Apache Hadoop ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 08/13/2020
ms.openlocfilehash: 82297b81ebe99301574e99ef438cc81c711dbeff
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95533680"
---
# <a name="apache-components-and-versions-available-for-azure-hdinsight"></a>Součásti a verze Apache dostupné pro Azure HDInsight

V tomto článku se dozvíte o komponentách a verzích prostředí [Apache Hadoop](https://hadoop.apache.org/) v Azure HDInsight a balíček zabezpečení podniku. Naučíte se také, jak kontrolovat verze komponent Hadoop v HDInsight.

## <a name="apache-components-available-with-different-hdinsight-versions"></a>Dostupné součásti Apache s různými verzemi HDInsight

Azure HDInsight podporuje víc verzí clusterů Hadoop, které se dají kdykoli nasadit. 4. dubna 2017 byla výchozí verze clusteru používaná službou Azure HDInsight 3,6.

Verze komponent spojené s verzemi clusterů HDInsight jsou uvedené v následující tabulce.

> [!NOTE]
> Výchozí verze služby HDInsight se může změnit bez předchozího upozornění. Pokud máte závislost verze, určete verzi HDInsight při vytváření clusterů pomocí sady .NET SDK pomocí Azure PowerShell a rozhraní příkazového řádku Azure Classic.

| Součást              | HDInsight 4.0 | HDInsight 3,6 (výchozí)     |
|------------------------|---------------|-----------------------------|
| Apache Hadoop a PŘÍZe | 3.1.1         | 2.7.3                       |
| Apache Tez             | 0.9.1         | 0.7.0                       |
| Apache prasete             | 0.16.1        | 0.16.0                      |
| Apache Hive            | 3.1.0         | 1.2.1 (2.1.0 on ESP Interactive Query) |
| Apache Tez Hive2       | -             | 0.8.4                       |
| Apache Ranger          | 1.1.0         | 0.7.0                       |
| Apache HBase           | 2.1.6         | 1.1.2                       |
| Apache Sqoop           | 1.4.7         | 1.4.6                       |
| Apache Oozie           | bodu         | 4.2.0                       |
| Apache Zookeeper       | 3.4.6         | 3.4.6                       |
| Apache Storm           | -             | 1.1.0                       |
| Apache Mahout          | -             | 0.9.0 +                      |
| Apache Phoenix         | 5             | 4.7.0                       |
| Apache Spark           | 2.4.4         | 2.3.2.                      |
| Apache Livy            | 0,5           | 0,4.                        |
| Apache Kafka           | 2.1.1         | 1,1                         |
| Apache Ambari          | 2.7.0         | 2.6.0                       |
| Apache Zeppelin        | 0.8.0         | 0.7.3                       |
| Mono                   | 4.2.1         | 4.2.1                       |

> [!NOTE]
> HDInsight 4,0: Spark 2,4 a Kafka 2,1 jsou plně podporované. Typy clusterů Spark 2,3 a Kafka 1,1 však nejsou obsluhované. HDInsight 3,6: Spark 2,3 a Kafka 1,1 jsou plně podporované.  

## <a name="check-for-current-apache-component-version-information"></a>Vyhledat aktuální informace o verzi součásti Apache

Verze komponent prostředí Hadoop spojené s verzemi clusterů HDInsight se můžou měnit s aktualizacemi služby HDInsight. Chcete-li zkontrolovat komponenty Hadoop a ověřit, které verze jsou používány pro cluster, použijte REST API Ambari. Příkaz **GetComponentInformation** načte informace o součástech služby. Další informace najdete v [dokumentaci k Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

### <a name="release-notes"></a>Poznámky k verzi

Další poznámky k verzi nástroje HDInsight najdete v [poznámkách k verzi HDInsight](hdinsight-release-notes.md).

## <a name="supported-hdinsight-versions"></a>Podporované verze HDInsight

### <a name="support-expiration-and-retirement-for-hdinsight-versions"></a>Podpora vypršení platnosti a vyřazení verzí HDInsight

**Vypršení platnosti podpory** znamená, že Microsoft už neposkytuje podporu pro konkrétní verzi HDInsight. A už není k dispozici prostřednictvím Azure Portal pro vytváření clusteru. Tyto verze se pořád dají vytvořit pomocí rozhraní příkazového řádku Azure nebo různých sad SDK.

**Vyřazení** znamená, že existující clustery verze HDInsight i nadále fungují tak, jak jsou. Nové clustery této verze se nedají vytvořit prostřednictvím žádného prostředku, který zahrnuje rozhraní příkazového řádku a sady SDK. Jiné funkce roviny ovládacího prvku, jako je ruční škálování a automatické škálování, nemusí po vyřazení verze fungovat. Podpora není k dispozici pro vyřazené verze.

V následujících tabulkách jsou uvedeny verze HDInsight. Data vypršení platnosti podpory a datum vyřazení jsou také k dispozici, když jsou známy.

### <a name="available-versions"></a>Dostupné verze

Tato tabulka obsahuje seznam verzí HDInsight, které jsou k dispozici v Azure Portal a prostřednictvím jiných metod nasazení, jako je PowerShell a .NET SDK.

| Verze HDInsight | Operační systém virtuálního počítače | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  Dostupnost v Azure Portal |
| --- | --- | --- | --- | --- | --- | --- |
| HDInsight 4.0 |Ubuntu 16.0.4 LTS |Září 24, 2018 | | |Ano |Ano |
| HDInsight 3,6 |Ubuntu 16.0.4 LTS |4. dubna 2017      | * 30. června 2021 |30. června 2021 |Ano |Ano |

* Rozšiřujeme časový rámec podpory pro určité typy clusterů HDInsight 3,6.

| Typ clusteru                    | Verze frameworku | Aktuální doba platnosti podpory        | Nové datum vypršení platnosti podpory |
|---------------------------------|-------------------|-----------------------------------|-----------------------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 31. prosince 2020                      | 30. června 2021               |
| HDInsight 3,6 Spark             | 2.3               | 31. prosince 2020                      | 30. června 2021               |
| HDInsight 3,6 Spark             | 2,2               | Vyřazení 30. června 2020          |                             |
| HDInsight 3,6 Spark             | 2.1               | Vyřazení 30. června 2020          |                             |
| HDInsight 3,6 Kafka             | 1,1               | 31. prosince 2020                      | 30. června 2021               |
| HDInsight 3,6 Kafka             | 1,0               | Vyřazení 30. června 2020.         |                             |
| Adaptéry HDInsight 3,6             | 1,1               | 31. prosince 2020                      | 30. června 2021               |
| Interaktivní dotaz HDInsight 3,6 | 2.1               | 31. prosince 2020                      | 30. června 2021               |
| HDInsight 3,6 – zaplavení             | 1,1               | 31. prosince 2020                      | 30. června 2021               |
| Služby HDInsight 3,6 ML      | 9,3               | 31. prosince 2020                      | 31. prosince 2020                |

> [!NOTE]
> Po vypršení platnosti verze clusteru nemusí být k dispozici prostřednictvím Azure Portal. V některých případech jsou verze clusteru dál k dispozici pomocí parametru **Version** v příkazu Windows PowerShell [New-AZHDINSIGHTCLUSTER](/powershell/module/az.hdinsight/new-azhdinsightcluster) a .NET SDK, dokud není datum vyřazení verze.

### <a name="retired-versions"></a>Vyřazené verze

Tato tabulka obsahuje seznam verzí HDInsight, které nejsou k dispozici v Azure Portal.

| Verze HDInsight | Verze HDP | Operační systém virtuálního počítače | Datum vydání | Datum vypršení platnosti podpory | Datum vyřazení | Vysoká dostupnost |  Dostupnost v Azure Portal |
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
> Clustery s vysokou dostupností se dvěma hlavními uzly se ve výchozím nastavení nasazují pro HDInsight verze 2,1 a novější. Nejsou k dispozici pro clustery HDInsight verze 1,6.

## <a name="service-level-agreement-for-hdinsight-cluster-versions"></a>Smlouva o úrovni služeb pro verze clusteru HDInsight

Smlouva o úrovni služeb je definovaná jako _okno podpory_. Okno podpory je časový interval, po který podporuje zákaznická služba a podpora Microsoftu verzi HDInsight. Pokud verze prošla _datem vypršení platnosti podpory_, cluster HDInsight je mimo okno podpory. Podpora vypršení platnosti pro HDInsight verze X (po dostupnosti novější verze X + 1) je novější z těchto verzí:

- **Vzorec 1:** Přidejte 180 dní do data, kdy byl vydán cluster HDInsight verze X.
- **Vzorec 2:** Do data přidejte 90 dní, kdy je ve Azure Portal k dispozici cluster HDInsight verze X + 1.

_Datum vyřazení_ je datum, po jehož uplynutí nelze ve službě HDInsight vytvořit verzi clusteru. Od 31. července 2017 nemůžete změnit velikost clusteru HDInsight po jeho datu vyřazení.

## <a name="default-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí konfigurace uzlů a velikosti virtuálních počítačů pro clustery

Další informace o tom, které SKU virtuálních počítačů vybrat pro váš cluster, najdete v tématu [Podrobnosti o konfiguraci clusteru Azure HDInsight](hdinsight-supported-node-configuration.md).

## <a name="next-steps"></a>Další kroky

- [Nastavení clusteru pro Apache Hadoop, Spark a další funkce v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Balíček zabezpečení podniku](./enterprise-security-package.md)
- [Poznámky k verzi Hortonworks přidružené k verzím Azure HDInsight](./hortonworks-release-notes.md)
- [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
