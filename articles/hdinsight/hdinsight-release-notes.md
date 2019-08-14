---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 7c31520efd881e8e0b5ed309f62d273bac59c0e3
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945016"
---
# <a name="release-notes"></a>Poznámky k verzi

Tento článek poskytuje informace o nejnovějších aktualizacích vydaných verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v [článku Správa verzí služby HDInsight](hdinsight-component-versioning.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky pro Open Source Apache Hadoop a Apache Spark analýzy v Azure.

## <a name="new-features"></a>Nové funkce

Další informace o důležitých změnách v HDInsight 4,0 najdete v tématu [co je nového v HDI 4,0?](../hdinsight/hdinsight-version-release.md).

## <a name="component-versions"></a>Verze komponent

Oficiální verze Apache všech komponent HDInsight 4,0 jsou uvedené níže. Uvedené součásti představují verze nejaktuálnějších stabilních verzí, které jsou k dispozici.

- Apache Ambari 2.7.1
- Apache Hadoop 3.1.1
- 2\.0.0 Apache HBA
- Apache Hive 3.1.0
- Apache Kafka 1.1.1, 2.1.0
- Apache Mahout 0.9.0 +
- Apache Oozie 4.2.0
- Apache Phoenix 4.7.0
- Apache prasete 0.16.0
- Apache Ranger 0.7.0
- 0\.92.0 posuvník Apache
- Apache Spark 2.3.1, 2.4.0
- Apache Sqoop 1.4.7
- Apache TEZ 0.9.1
- Apache Zeppelin 0.8.0
- Apache ZooKeeper 3.4.6

Kromě výše uvedených verzí jsou někdy součástí distribuce HDP i novější verze komponent Apache. V tomto případě jsou tyto novější verze uvedené v tabulce Technical Preview a neměly by nahrazovat verze výše uvedeného seznamu v produkčním prostředí.

## <a name="apache-patch-information"></a>Informace o opravě Apache

Další informace o opravách dostupných v HDInsight 4,0 najdete v seznamu oprav pro každý produkt v následující tabulce.

| Název produktu | Informace o opravě |
|---|---|
| Ambari | [Informace o opravě Ambari](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.1.0/bk_ambari-release-notes/content/ambari_relnotes-2.7.1.0-patch-information.html) |
| Hadoop | [Informace o opravě Hadoop](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hadoop.html) |
| HBase | [HBA – informace o opravě](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_hbase.html) |
| Hive  | Tato verze poskytuje 3.1.0 podregistru bez dalších oprav Apache.  |
| Kafka | Tato verze poskytuje Kafka 1.1.1 bez dalších oprav Apache. |
| Oozie | [Informace o opravě Oozie](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_oozie.html) |
| Phoenix | [Phoenix – informace o opravě](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_phoenix.html) |
| Pig | [Informace o opravě prasete](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_pig.html) |
| Ranger | [Informace o opravě Ranger](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_ranger.html) |
| Spark | [Informace o opravě Spark](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_spark.html) |
| Sqoop | Tato verze poskytuje Sqoop 1.4.7 bez dalších oprav Apache. |
| Tez | Tato verze poskytuje tez 0.9.1 bez dalších oprav Apache. |
| Zeppelin | Tato verze poskytuje Zeppelin 0.8.0 bez dalších oprav Apache. |
| Zookeeper | [Informace o opravě Zookeeper](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/patch_zookeeper.html) |

## <a name="fixed-common-vulnerabilities-and-exposures"></a>Opravené běžné chyby zabezpečení a ohrožení

Další informace o problémech se zabezpečením vyřešených v této verzi najdete v tématu Hortonworks "opravenými běžnými ohroženími zabezpečení [a hrozbami pro HDP 3.0.1](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.0.1/release-notes/content/cve.html).

## <a name="known-issues"></a>Známé problémy

### <a name="replication-is-broken-for-secure-hbase-with-default-installation"></a>U zabezpečených HBA s výchozí instalací je replikace přerušená.

V případě HDInsight 4,0 proveďte následující kroky:

1. Povolte komunikaci mezi clustery.
1. Přihlaste se k aktivnímu hlavnímu uzlu.
1. Stáhněte si skript, který povolí replikaci pomocí následujícího příkazu:

    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Zadejte příkaz `sudo kinit <domainuser>`.
1. Zadáním následujícího příkazu spusťte skript:

    ```
    sudo bash hdi_enable_replication.sh -m <hn0> -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```
V případě HDInsight 3,6 postupujte takto:

1. Přihlaste se k aktivnímu HMaster ZK.
1. Stáhněte si skript, který povolí replikaci pomocí následujícího příkazu:
    ```
    sudo wget https://raw.githubusercontent.com/Azure/hbase-utils/master/replication/hdi_enable_replication.sh
    ```
1. Zadejte příkaz `sudo kinit -k -t /etc/security/keytabs/hbase.service.keytab hbase/<FQDN>@<DOMAIN>`.
1. Zadejte následující příkaz:

    ```bash
    sudo bash hdi_enable_replication.sh -s <srclusterdns> -d <dstclusterdns> -sp <srcclusterpasswd> -dp <dstclusterpasswd> -copydata
    ```

### <a name="phoenix-sqlline-stops-working-after-migrating-hbase-cluster-to-hdinsight-40"></a>Phoenix SqlLine přestane pracovat po migraci clusteru HBA do HDInsight 4,0

Proveďte následující kroky:

1. Přetáhněte následující tabulky Phoenix:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.MUTEX`
    1. `SYSTEM.CATALOG`
1. Pokud nemůžete odstranit žádnou z tabulek, restartujte HBA a vymažte všechna připojení k tabulkám.
1. Spusťte `sqlline.py` znovu. Phoenix znovu vytvoří všechny tabulky, které jste odstranili v kroku 1.
1. Znovu vygenerujte tabulky a zobrazení v Phoenixu pro data z adaptérů HBA.

### <a name="phoenix-sqlline-stops-working-after-replicating-hbase-phoenix-metadata-from-hdinsight-36-to-40"></a>Phoenix SqlLine přestane pracovat po replikaci adaptérů HBA z HDInsight 3,6 na 4,0

Proveďte následující kroky:

1. Před provedením replikace přejdete do cílového clusteru 4,0 a spustí `sqlline.py`se. Tento příkaz vygeneruje tabulky v Phoenixu `SYSTEM.MUTEX` , `SYSTEM.LOG` například a, které existují pouze v 4,0.
1. Přetáhněte následující tabulky:
    1. `SYSTEM.FUNCTION`
    1. `SYSTEM.SEQUENCE`
    1. `SYSTEM.STATS`
    1. `SYSTEM.CATALOG`
1. Spuštění replikace HBA

## <a name="deprecation"></a>Vyřazení

Služby Apache Storm a ML nejsou v HDInsight 4,0 dostupné.
