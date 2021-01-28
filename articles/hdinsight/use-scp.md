---
title: Použití spojovacího bodu služby s Apache Hadoop ve službě Azure HDInsight
description: Tento dokument poskytuje informace o připojení ke službě HDInsight pomocí příkazů SSH a SCP.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: 927b8c55008c3e01d8ff1dd09c46cfa3c6618026
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946275"
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
* [HDInsight používající Azure Data Lake Storage Gen1](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md).

## <a name="next-steps"></a>Další kroky

* [Použití SSH s HDInsightem](./hdinsight-hadoop-linux-use-ssh-unix.md)
* [Použití hraničních uzlů v HDInsightu](hdinsight-apps-use-edge-node.md#access-an-edge-node)
