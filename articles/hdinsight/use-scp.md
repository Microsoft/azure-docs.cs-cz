---
title: Použití spojovacího bodu služby s Apache Hadoop ve službě Azure HDInsight
description: Tento dokument poskytuje informace o připojení ke službě HDInsight pomocí příkazů SSH a SCP.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: bb90db8a15a872fc28b3d09183228a67edf290e3
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82103227"
---
# <a name="use-scp-with-apache-hadoop-in-azure-hdinsight"></a>Použití spojovacího bodu služby s Apache Hadoop ve službě Azure HDInsight

Tento článek poskytuje informace o bezpečném přenosu souborů s clusterem HDInsight.

## <a name="copy-files"></a>Kopírování souborů

Ke kopírování souborů na jednotlivé uzly clusteru nebo z nich je možné použít nástroj `scp`. Například následující příkaz zkopíruje soubor `test.txt` z místního systému na primární hlavní uzel:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Protože po `:` není zadaná žádná cesta, soubor se umístí do domovského adresáře `sshuser`.

Následující příklad zkopíruje soubor `test.txt` z domovského adresáře `sshuser` na primárním hlavním uzlu do místního systému:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

Nástroj `scp` má přístup pouze k systému souborů jednotlivých uzlů v rámci clusteru. Nedá se použít pro přístup k datům v úložišti, které je kompatibilní s HDFS pro cluster.

Pokud potřebujete nahrát prostředek pro použití z relace SSH, použijte `scp`. Nahrajte například skript Pythonu a potom tento skript spusťte z relace SSH.

Informace o přímém načítání dat do úložiště kompatibilního se systémem HDFS najdete v následujících dokumentech:

* [HDInsight používající Azure Storage](hdinsight-hadoop-use-blob-storage.md).
* [HDInsight používající Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md).

## <a name="next-steps"></a>Další kroky

* [Použití SSH s HDInsightem](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Použití hraničních uzlů v HDInsightu](hdinsight-apps-use-edge-node.md#access-an-edge-node)
