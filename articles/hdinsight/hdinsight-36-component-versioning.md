---
title: Apache Hadoop součásti a verze – Azure HDInsight 3,6
description: Přečtěte si informace o komponentách a verzích Apache Hadoop v Azure HDInsight 3,6.
ms.service: hdinsight
ms.topic: conceptual
author: deshriva
ms.author: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: 32b287c3d7b1974db5a079d1ee84aaafad3faed7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727684"
---
# <a name="hdinsight-36-component-versions"></a>Verze komponent HDInsight 3,6

V tomto článku se dozvíte o komponentách a verzích prostředí Apache Hadoop v Azure HDInsight 3,6.

## <a name="support-for-hdinsight-36"></a>Podpora pro HDInsight 3,6

Od 1. července 2021 Microsoft nabídne podpora Basic pro určité typy clusterů HDI 3,6.
Následující tabulka uvádí časový rámec podpory pro typy clusterů HDInsight 3,6.

| Typ clusteru                    | Verze frameworku | Doba platnosti Standard Support       | podpora Basic datum vypršení platnosti | Datum vyřazení |
|---------------------------------|-------------------|-----------------------------------|------------------------------|-----------------|
| HDInsight 3,6 Hadoop            | 2.7.3             | 30. června 2021                     | 3. dubna 2022                | 4. dubna 2022 |
| HDInsight 3,6 Spark             | 2.3               | 30. června 2021                     | 3. dubna 2022                | 4. dubna 2022 |
| HDInsight 3,6 Kafka             | 1.1               | 30. června 2021                     | 3. dubna 2022                | 4. dubna 2022 |
| Adaptéry HDInsight 3,6             | 1.1               | 30. června 2021                     | 3. dubna 2022                | 4. dubna 2022 |
| Interaktivní dotaz HDInsight 3,6 | 2.1               | 30. června 2021                     | 3. dubna 2022                | 4. dubna 2022 |
| HDInsight 3,6 – zaplavení             | 1.1               | 30. června 2021                     | 3. dubna 2022                | 4. dubna 2022 |
| Služby HDInsight 3,6 ML      | 9.3               | -                                 | -                            | 31. prosince 2020 |
| HDInsight 3,6 Spark             | 2,2               | -                                 | -                            | 30. června 2020 |
| HDInsight 3,6 Spark             | 2.1               | -                                 | -                            | 30. června 2020 |
| HDInsight 3,6 Kafka             | 1.0               | -                                 | -                            | 30. června 2020 |

## <a name="apache-components-available-with-hdinsight-version-36"></a>Součásti Apache dostupné ve verzi HDInsight 3,6

Verze komponent OSS přidružené k HDInsight 3,6 jsou uvedené v následující tabulce.

| Součást              | HDInsight 3,6 (výchozí)     |
|------------------------|-----------------------------|
| Apache Hadoop a PŘÍZe | 2.7.3                       |
| Apache Tez             | 0.7.0                       |
| Apache prasete             | 0.16.0                      |
| Apache Hive            | (2.1.0 on ESP Interactive Query) |
| Apache Tez Hive2       | 0.8.4                       |
| Apache Ranger          | 0.7.0                       |
| Apache HBase           | 1.1.2                       |
| Apache Sqoop           | 1.4.6                       |
| Apache Oozie           | 4.2.0                       |
| Apache Zookeeper       | 3.4.6                       |
| Apache Storm           | 1.1.0                       |
| Apache Mahout          | 0.9.0 +                      |
| Apache Phoenix         | 4.7.0                       |
| Apache Spark           | 2.3.2.                      |
| Apache Livy            | 0,4.                        |
| Apache Kafka           | 1.1                         |
| Apache Ambari          | 2.6.0                       |
| Apache Zeppelin        | 0.7.3                       |
| Mono                   | 4.2.1                       |

## <a name="next-steps"></a>Další kroky

- [Nastavení clusteru pro Apache Hadoop, Spark a další funkce v HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Balíček zabezpečení podniku](./enterprise-security-package.md)
- [Práce v Apache Hadoop ve službě HDInsight z počítače s Windows](hdinsight-hadoop-windows-tools.md)
