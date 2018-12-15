---
title: Použití Apache Pig s SSH v clusteru HDInsight – Azure
description: Zjistěte, jak se připojit ke clusteru Apache Hadoop založené na Linuxu pomocí SSH a pak použijte Pig příkaz ke spuštění příkazů Pig Latin interaktivně nebo jako úlohu služby batch.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a674ee52ae5e8f8f800d4584a53c808ceae70156
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53435051"
---
# <a name="run-apache-pig-jobs-on-a-linux-based-cluster-with-the-pig-command-ssh"></a>Spouštět úlohy Apache Pig na cluster založených na Linuxu pomocí příkazu Pig (SSH)

[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Zjistěte, jak interaktivně spouštět úlohy Apache Pig z připojení SSH ke clusteru HDInsight. Programovacím jazyce Pig Latin můžete popsat transformace, které se použijí pro vstupní data pro vytvoření požadovaného výstupu.

> [!IMPORTANT]  
> Kroky v tomto dokumentu vyžadují cluster HDInsight se systémem Linux. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="ssh"></a>Připojení přes SSH

Pomocí SSH se připojte ke clusteru HDInsight. Následující příklad se připojí ke clusteru s názvem **myhdinsight** jako účet s názvem **sshuser**:

```bash
ssh sshuser@myhdinsight-ssh.azurehdinsight.net
```

Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a id="pig"></a>Použití příkazu Apache Pig

1. Jakmile budete připojeni, pomocí následujícího příkazu spusťte Pig rozhraní příkazového řádku (CLI):

    ```bash
    pig
    ```

    Za okamžik, příkazový řádek se změní`grunt>`.

2. Zadejte následující příkaz:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    ```

    Tento příkaz načte obsah souboru sample.log do PROTOKOLŮ. Obsah souboru můžete zobrazit pomocí následujícího příkazu:

    ```piglatin
    DUMP LOGS;
    ```

3. V dalším kroku transformaci dat s použitím regulárních výrazů k extrakci jenom úroveň protokolování z každého záznamu pomocí následujícího příkazu:

    ```piglatin
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    ```

    Můžete použít **VYPSAT** k zobrazení dat po transformaci. V takovém případě použijte `DUMP LEVELS;`.

4. Pokračujte v použití transformací pomocí příkazů v následující tabulce:

    | Pig Latin – příkaz | Co dělá příkaz |
    | ---- | ---- |
    | `FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;` | Odebere řádky, které obsahují hodnotu null pro úroveň protokolování a uloží výsledky do `FILTEREDLEVELS`. |
    | `GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;` | Seskupí řádky podle úroveň protokolu a uloží výsledky do `GROUPEDLEVELS`. |
    | `FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;` | Vytvoří sadu dat, která obsahuje všechny jedinečné protokoly hodnota úrovně a jak často se vyvolá. Datová sada ukládána do `FREQUENCIES`. |
    | `RESULT = order FREQUENCIES by COUNT desc;` | Řadí úrovních protokolování podle počtu (sestupně) a uloží do `RESULT`. |

    > [!TIP]  
    > Použití `DUMP` abyste viděli výsledek transformace po provedení každého kroku.

5. Můžete také uložit výsledky transformace s využitím `STORE` příkazu. Například následující příkaz uloží `RESULT` k `/example/data/pigout` adresář na výchozí úložiště pro cluster:

    ```piglatin
    STORE RESULT into '/example/data/pigout';
    ```

   > [!NOTE]  
   > Jsou data uložená v zadaném adresáři v souborech s názvem `part-nnnnn`. Pokud adresář již existuje, zobrazí se chybová zpráva.

6. Pro ukončení řádku grunt, zadejte následující příkaz:

    ```piglatin
    QUIT;
    ```

### <a name="pig-latin-batch-files"></a>Pig Latin dávkové soubory

Můžete také Pig příkaz ke spuštění Pig Latin obsažené v souboru.

1. Po ukončení řádku grunt, použijte následující příkaz k vytvoření souboru s názvem `pigbatch.pig`:

    ```bash
    nano ~/pigbatch.pig
    ```

2. Zadejte nebo vložte následující řádky:

    ```piglatin
    LOGS = LOAD '/example/data/sample.log';
    LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
    FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
    GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
    FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
    RESULT = order FREQUENCIES by COUNT desc;
    DUMP RESULT;
    ```

    Až budete hotovi, použijte __Ctrl__ + __X__, __Y__a potom __Enter__ k uložení souboru.

3. Použijte následující příkaz pro spuštění `pigbatch.pig` soubor pomocí příkazu Pig.

    ```bash
    pig ~/pigbatch.pig
    ```

    Po dokončení úlohy služby batch, zobrazí se následující výstup:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)


## <a id="nextsteps"></a>Další kroky

Obecné informace o Pig v HDInsight najdete v následujícím dokumentu:

* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)

Další informace o další způsoby, jak pracovat s Hadoop v HDInsight najdete v následujících dokumentech:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)
