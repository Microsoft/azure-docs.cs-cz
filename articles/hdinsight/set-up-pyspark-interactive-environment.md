---
title: Interaktivní prostředí PySpark s nástroji Azure HDInsight Tools
description: Přečtěte si, jak pomocí nástrojů Azure HDInsight pro kód Visual Studia vytvářet a odesílat dotazy a skripty.
keywords: VScode,Nástroje Azure HDInsight,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interaktivní úl,Interaktivní dotaz
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 2a725f3c5c9e1428079807b5b76dbe72d416a9c7
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393659"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Nastavení interaktivního prostředí PySpark pro kód sady Visual Studio

Následující kroky ukazují, jak nastavit interaktivní prostředí PySpark v kódu VS.

K vytvoření virtuálního prostředí na domovské cestě používáme příkaz **python/pip.** Pokud chcete použít jinou verzi, musíte změnit výchozí verzi příkazu **python/pip** ručně. Další podrobnosti naleznete [v alternativních aktualizacích](https://linux.die.net/man/8/update-alternatives).

1. Nainstalujte [Python](https://www.python.org/downloads/) a [pip](https://pip.pypa.io/en/stable/installing/).

   + Nainstalujte [https://www.python.org/downloads/](https://www.python.org/downloads/)Python z aplikace .
   + Nainstalujte [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) pip z (pokud není nainstalován z instalace Pythonu).
   + Ověřte, zda jsou Python a pip úspěšně nainstalovány pomocí následujících příkazů. (Nepovinné)

        ![Zkontrolovat verzi pip pythonu, příkaz](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Doporučujeme ručně nainstalovat Python místo použití výchozí verze macOS.

2. Nainstalujte **virtualenv** spuštěním příkazu níže.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Ostatní balíčky

Pokud narazíte na chybovou zprávu, nainstalujte požadované balíčky spuštěním následujících příkazů:

   ![Instalace balíčku libkrb5 pro python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Restartujte Kód VS a vraťte se do editoru skriptů, ve které běží **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Další kroky

### <a name="demo"></a>Ukázka

* HDInsight pro Kód VS: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití nástroje Azure HDInsight pro kód Visual Studia](hdinsight-for-vscode.md)
* [Použití sady Azure Toolkit pro IntelliJ k vytváření a odesílání aplikací Apache Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit for IntelliJ k vzdálenému ladění aplikací Apache Spark prostřednictvím SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití sady Azure Toolkit pro IntelliJ k vzdálenému ladění aplikací Apache Spark prostřednictvím sítě VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Vytváření aplikací Apache Spark pomocí nástrojů HDInsight v sadě nástrojů Azure pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace dat Apache Hive pomocí Microsoft Power BI v Azure HDInsightu](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Použití Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
