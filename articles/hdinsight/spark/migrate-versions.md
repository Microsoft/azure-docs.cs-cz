---
title: Migrace úloh Apache Spark 2,1 nebo 2,2 na 2,3 nebo 2,4-Azure HDInsight
description: Naučte se migrovat Apache Spark 2,1 a 2,2 na 2,3 nebo 2,4.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: af1894d2f63357006e87fa8e4533f135ecc02f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944744"
---
# <a name="migrate-apache-spark-21-and-22-workloads-to-23-and-24"></a>Migrace úloh Apache Spark 2,1 a 2,2 na 2,3 a 2,4

Tento dokument vysvětluje, jak migrovat Apache Spark úlohy ve Sparku 2,1 a 2,2 na 2,3 nebo 2,4.

Jak je popsáno v [poznámkách k verzi](../hdinsight-release-notes.md#upcoming-changes)od 1. července 2020, následující konfigurace clusteru se nepodporují a zákazníci nebudou moct vytvářet nové clustery s těmito konfiguracemi:
 - Spark 2,1 a 2,2 v clusteru HDInsight 3,6 Spark
 - Spark 2,3 v clusteru HDInsight 4,0 Spark

Existující clustery v těchto konfiguracích budou spuštěné, a to bez podpory Microsoftu. Pokud jste ve službě HDInsight 2,1 nebo 2,2 ve službě HDInsight 3,6, přejděte v červnu 3,6 na Spark 2,3 v HDInsight 30 2020, aby se předešlo potenciálnímu přerušení systému/podpory. Pokud jste v Sparku 2,3 v clusteru HDInsight 4,0, přejděte do Sparku 2,4 v HDInsight 4,0 v červnu 30 2020, aby se předešlo potenciálnímu přerušení systému/podpory.

Obecné informace o migraci clusteru HDInsight z 3,6 na 4,0 najdete v tématu [migrace clusteru HDInsight na novější verzi](../hdinsight-upgrade-cluster.md). Obecné informace o migraci na novější verzi Apache Spark najdete v tématu [Apache Spark: zásady správy verzí](https://spark.apache.org/versioning-policy.html).

## <a name="guidance-on-spark-version-upgrades-on-hdinsight"></a>Doprovodné materiály k upgradům verze Sparku ve službě HDInsight

| Scénář upgradu | Mechanismus | Co je potřeba zvážit | Integrace Spark/podregistr |
|------------------|-----------|--------------------|------------------------|
|HDInsight 3,6 Spark 2,1 až HDInsight 3,6 Spark 2,3| Opětovné vytvoření clusterů pomocí HDInsight Spark 2,3 | Přečtěte si následující články: <br> [Apache Spark: upgrade ze Spark SQL 2,2 na 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: upgrade ze Spark SQL 2,1 na 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Žádná změna |
|HDInsight 3,6 Spark 2,2 až HDInsight 3,6 Spark 2,3 | Opětovné vytvoření clusterů pomocí HDInsight Spark 2,3 | Přečtěte si následující články: <br> [Apache Spark: upgrade ze Spark SQL 2,2 na 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Žádná změna |
| HDInsight 3,6 Spark 2,1 až HDInsight 4,0 Spark 2,4 | Opětovné vytvoření clusterů pomocí HDInsight 4,0 Spark 2,4 | Přečtěte si následující články: <br> [Apache Spark: upgrade ze Spark SQL 2,3 na 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: upgrade ze Spark SQL 2,2 na 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) <br><br> [Apache Spark: upgrade ze Spark SQL 2,1 na 2,2](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-21-to-22) | Integrace Sparku a podregistru se změnila v HDInsight 4,0. <br><br> Spark a podregistr v HDInsight 4,0 používají nezávislé katalogy pro přístup k SparkSQL nebo tabulkám podregistru. Tabulka vytvořená Sparkem v katalogu Spark. Tabulka vytvořená podregistrem v katalogu podregistru Toto chování se liší od HDInsight 3,6, ve kterém se společný katalog pro podregistr a Spark sdílí. Integrace podregistru a Sparku v HDInsight 4,0 spoléhá na umožní (podregistr Warehouse Connector). UMOŽNÍ funguje jako most mezi Sparkem a podregistrem. Seznamte se s konektorem skladiště pro podregistr. <br> Pokud chcete metastore sdílet mezi podregistrem a Sparkem, můžete v HDInsight 4,0 změnit vlastnost metastore. Catalog. default na podregistr v clusteru Spark. Tuto vlastnost najdete v Ambari Advanced spark2-podregistr-site-override. Je důležité si uvědomit, že sdílení metastore funguje jenom pro externí tabulky podregistru, takže pokud máte interní/spravované tabulky podregistru nebo tabulky s kyselým obsahem, nebude to fungovat. <br><br>Další informace najdete v článku [migrace úloh v podregistru Azure HDInsight 3,6 na HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) .<br><br> |
| HDInsight 3,6 Spark 2,2 až HDInsight 4,0 Spark 2,4 | Opětovné vytvoření clusterů pomocí HDInsight 4,0 Spark 2,4 | Přečtěte si následující články: <br> [Apache Spark: upgrade ze Spark SQL 2,3 na 2,4](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-23-to-24) <br><br> [Apache Spark: upgrade ze Spark SQL 2,2 na 2,3](https://spark.apache.org/docs/latest/sql-migration-guide.html#upgrading-from-spark-sql-22-to-23) | Integrace Sparku a podregistru se změnila v HDInsight 4,0. <br><br> Spark a podregistr v HDInsight 4,0 používají nezávislé katalogy pro přístup k SparkSQL nebo tabulkám podregistru. Tabulka vytvořená Sparkem v katalogu Spark. Tabulka vytvořená podregistrem v katalogu podregistru Toto chování se liší od HDInsight 3,6, ve kterém se společný katalog pro podregistr a Spark sdílí. Integrace podregistru a Sparku v HDInsight 4,0 spoléhá na umožní (podregistr Warehouse Connector). UMOŽNÍ funguje jako most mezi Sparkem a podregistrem. Seznamte se s konektorem skladiště pro podregistr. <br> Pokud chcete metastore sdílet mezi podregistrem a Sparkem, můžete v HDInsight 4,0 změnit vlastnost metastore. Catalog. default na podregistr v clusteru Spark. Tuto vlastnost najdete v Ambari Advanced spark2-podregistr-site-override. Je důležité si uvědomit, že sdílení metastore funguje jenom pro externí tabulky podregistru, takže pokud máte interní/spravované tabulky podregistru nebo tabulky s kyselým obsahem, nebude to fungovat. <br><br>Další informace najdete v článku [migrace úloh v podregistru Azure HDInsight 3,6 na HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md) .|

## <a name="next-steps"></a>Další kroky

* [Migrace clusteru HDInsight na novější verzi](../hdinsight-upgrade-cluster.md)
* [Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0](../interactive-query/apache-hive-migrate-workloads.md)
