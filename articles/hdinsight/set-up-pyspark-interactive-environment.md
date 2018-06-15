---
title: Nastavení nástroje Azure HDInsight - PySpark interaktivní prostředí pro Visual Studio Code | Microsoft Docs
description: Další informace o použití nástroje Azure HDInsight pro Visual Studio Code pro vytvoření a odesílání dotazů a skriptů.
Keywords: VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: jejiang
manager: ''
editor: ''
tags: azure-portal
ms.assetid: ''
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 4ab7b95861fcd1ff75f8ac84e4f00aedb6e526f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31407227"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Nastavení interaktivní prostředí PySpark pro Visual Studio Code

Následující kroky vám ukážou, jak nainstalovat balíčky Pythonu spuštěním **HDInsight: PySpark interaktivní**.


## <a name="set-up-the-pyspark-interactive-environment-on-macos-and-linux"></a>Nastavení interaktivní prostředí PySpark v systému macOS a Linux
Pokud používáte **python 3.x**, budete muset použít příkaz **pip3** pro následující kroky:

1. Zajistěte, aby **Python** a **pip** jsou nainstalovány.
 
    ![verze pip Python](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

2.  Nainstalujte Jupyter.
    ```
    sudo pip install jupyter
    ```
   Zobrazí se následující chybová zpráva na Linuxu a systému macOS:

   ![Chyba 1](./media/set-up-pyspark-interactive-environment/error1.png)

   ```Resolve:
    sudo pip uninstall asyncio
    sudo pip install trollies
    ```

3. Nainstalujte **libkrb5 dev** (pro pouze Linux). Zobrazí se následující chybová zpráva:

   ![Chyba 2](./media/set-up-pyspark-interactive-environment/error2.png)
       
   ```Resolve:
   sudo apt-get install libkrb5-dev 
   ```

3. Nainstalujte **sparkmagic**.
   ```
   sudo pip install sparkmagic
   ```

4. Ujistěte se, že **ipywidgets** je správně nainstalován spuštěním následující:
   ```
   sudo jupyter nbextension enable --py --sys-prefix widgetsnbextension
   ```
   ![Nainstalujte jádrech obálky](./media/set-up-pyspark-interactive-environment/ipywidget-enable.png)
 

5. Nainstalujte jádrech obálku. Spustit **pip zobrazit sparkmagic**. Výstup ukazuje cestu pro **sparkmagic** instalace. 

    ![sparkmagic umístění](./media/set-up-pyspark-interactive-environment/sparkmagic-location.png)
   
6. Přejděte do umístění a pak spusťte:

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

    Pro dostupné jádra: 
    - **python2** a **pysparkkernel** odpovídají **python 2.x**. 
    - **python3** a **pyspark3kernel** odpovídají **python 3.x**. 

8. Restartujte VS Code a poté přejděte zpět na editor skriptů, které běží **HDInsight: PySpark interaktivní**.

## <a name="next-steps"></a>Další postup

### <a name="demo"></a>Ukázka
* HDInsight pro VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](hdinsight-for-vscode.md)
* [Použití nástrojů Azure pro IntelliJ k vytvoření a odeslání aplikací Spark Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně přes SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně prostřednictvím sítě VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používat nástroje HDInsight pro vytvoření aplikací Spark v Azure nástrojů pro Eclipse](spark/apache-spark-eclipse-tool-plugin.md)
* [Použití nástroje HDInsight pro IntelliJ s Hortonworks karanténě](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace dat Hive pomocí Microsoft Power BI ve službě Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Použití Zeppelin ke spouštění dotazů Hive v Azure HDInsight ](hdinsight-connect-hive-zeppelin.md)
