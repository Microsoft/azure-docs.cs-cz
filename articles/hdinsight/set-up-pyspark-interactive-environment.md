---
title: Nástroje pro Azure HDInsight – nastavení interaktivního prostředí PySpark pro Visual Studio Code
description: Další informace o použití nástroje Azure HDInsight pro Visual Studio Code k vytvoření a odeslání dotazů a skriptů.
keywords: VScode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 1/17/2019
ms.openlocfilehash: ef33d8962848636ee53ac6fd3f084b9c2a59e29d
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820567"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Nastavení interaktivního prostředí PySpark pro Visual Studio Code

Následující kroky ukazují, jak nastavit interaktivního prostředí PySpark ve VS Code.

Používáme **python a pip** příkaz k vytvoření virtuálního prostředí v cestě Domovská stránka. Pokud chcete použít jinou verzi, musíte změnit výchozí verzi **python a pip** příkaz ručně. Další podrobnosti najdete v tématu [aktualizace alternativy](https://linux.die.net/man/8/update-alternatives).

1. Nainstalujte [Python](https://www.python.org/) a [pip](https://pip.pypa.io/en/stable/installing/).
   
   + Instalace Pythonu z [ https://pip.pypa.io/en/stable/installing ](https://www.python.org/).
   + Instalace pip z [ https://pip.pypa.io/en/stable/installing ](https://pip.pypa.io/en/stable/installing/). (Pokud není nainstalovaná v instalaci Pythonu)
   + Ověření Python a pip se instalují úspěšně pomocí následujících příkazů. (Volitelné)
 
        ![Verze Pythonu pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

    > [!NOTE]
    > Doporučuje se ručně nainstalovat Python místo použití výchozí verze MacOS.


2. Nainstalujte **virtualenv** spuštěním následujícího příkazu.
   
   ```
   pip install virtualenv
   ```

3. Pouze Linux nainstalujte požadované balíčky spuštěním příkazů zmeškáte, pokud narazíte na chybovou zprávu.
   
    ![Verze Pythonu pip](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)
       
   ```
   sudo apt-get install libkrb5-dev 
   ```

   ```
   sudo apt-get install python-dev
   ```

4. Restartujte VS Code a pak přejděte zpět na editor skriptů, na kterém běží **HDInsight: PySpark interaktivní**.

## <a name="next-steps"></a>Další postup

### <a name="demo"></a>Ukázka
* HDInsight pro VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](hdinsight-for-vscode.md)
* [Vytvoření a odesílání aplikací Apache Spark Scala pomocí sady Azure Toolkit pro IntelliJ](spark/apache-spark-intellij-tool-plugin.md)
* [Ladění aplikací Apache Spark vzdáleně přes SSH pomocí sady Azure Toolkit pro IntelliJ](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Ladění aplikací Apache Spark vzdáleně přes síť VPN pomocí sady Azure Toolkit pro IntelliJ](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Vytváření aplikací Apache Spark pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace dat pomocí Microsoft Power BI v Azure HDInsight s Apache Hive](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Použití Apache Zeppelinu ke spuštění dotazy Apache Hive v Azure HDInsight ](hdinsight-connect-hive-zeppelin.md)
