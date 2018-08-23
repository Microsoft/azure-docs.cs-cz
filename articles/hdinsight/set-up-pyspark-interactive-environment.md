---
title: Nástroje pro Azure HDInsight – nastavení interaktivního prostředí PySpark pro Visual Studio Code
description: Další informace o použití nástroje Azure HDInsight pro Visual Studio Code k vytvoření a odeslání dotazů a skriptů.
keywords: VScode, Azure HDInsight Tools, Hive, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, Interactive Hive, Interactive Query
services: hdinsight
ms.service: hdinsight
author: jejiang
ms.author: jejiang
editor: jasonwhowell
ms.topic: conceptual
ms.date: 10/27/2017
ms.openlocfilehash: f7048665ea5bfbf6d4c1110a5823cf9acbfd0272
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054620"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Nastavení interaktivního prostředí PySpark pro Visual Studio Code

Následující kroky ukazují, jak nainstalovat balíčky Pythonu spuštěním **HDInsight: PySpark interaktivní**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Nastavení interaktivního prostředí PySpark v systémech macOS a Linux
Pokud používáte **python 3.x**, budete muset použít příkaz **pip3** pro následující kroky:

1. Ujistěte se, že **Python** a **pip** jsou nainstalovány.
 
    ![Verze Pythonu pip](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Instalace Jupyteru.
    ```
    sudo pip install jupyter
    ```
   Může se zobrazit následující chybová zpráva na Linuxu a macOS:

   ![Chyba: 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Nainstalujte **libkrb5 dev** (pro pouze Linux). Může se zobrazit následující chybová zpráva:

   ![Chyba 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Nainstalujte **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Ujistěte se, že **ipywidgets** je správně nainstalována, spusťte následující:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Instalace jádra obálky](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Instalace jádra obálky. Spustit **pip zobrazit sparkmagic**. Výstup zobrazuje cestu **sparkmagic** instalace. 

    ![sparkmagic umístění](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Přejděte do umístění a potom spusťte:

   ```Python2
   sudo jupyter-kernelspec install sparkmagic/kernels/pysparkkernel   
   ```
   ```Python3
   sudo jupyter-kernelspec install sparkmagic/kernels/pyspark3kernel
   ```

   ![instalace kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-install.png)
7. Zkontrolujte stav instalace.

    ```
    jupyter-kernelspec list
    ```
    ![seznam kernelspec jupyter](./media/set-up-pyspark-interactive-environment/jupyter-kernelspec-list.png)

    Pro dostupná jádra: 
    - **python2** a **pysparkkernel** odpovídají **python 2.x**. 
    - **pythonu3** a **pyspark3kernel** odpovídají **python 3.x**. 

8. Restartujte VS Code a pak přejděte zpět na editor skriptů, na kterém běží **HDInsight: PySpark interaktivní**.

## <a name="next-steps"></a>Další postup

### <a name="demo"></a>Ukázka
* HDInsight pro VS Code: [videa](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](hdinsight-for-vscode.md)
* [Vytvoření a odesílání aplikací Spark Scala pomocí sady Azure Toolkit pro IntelliJ](spark/apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes síť VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse k vytvoření aplikací Spark](spark/apache-spark-eclipse-tool-plugin.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace dat Hive pomocí Microsoft Power BI ve službě Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Použití Zeppelinu ke spouštění dotazů Hive v Azure HDInsight ](hdinsight-connect-hive-zeppelin.md)
