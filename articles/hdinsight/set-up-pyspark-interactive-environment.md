---
title: Nástroje Azure HDInsight – PySpark interaktivní prostředí pro Visual Studio Code
description: Naučte se používat nástroje Azure HDInsight pro Visual Studio Code k vytváření a odesílání dotazů a skriptů.
keywords: VScode, nástroje Azure HDInsight, podregistr, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktivní podregistr, interaktivní dotaz
author: jejiang
ms.author: jejiang
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f62f7994818e487202e35e4931472355fb6ac3d2
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71130181"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Nastavení PySpark interaktivního prostředí pro Visual Studio Code

Následující kroky ukazují, jak nastavit PySpark interaktivní prostředí v VS Code.

K sestavení virtuálního prostředí v cestě domů používáme příkaz **Python/PIP** . Pokud chcete použít jinou verzi, musíte ručně změnit výchozí verzi příkazu **Python/PIP** . Další podrobnosti najdete v tématu [aktualizace – alternativy](https://linux.die.net/man/8/update-alternatives).

1. Nainstalujte [Python](https://www.python.org/downloads/) a [PIP](https://pip.pypa.io/en/stable/installing/).

   + Nainstalujte Python z [https://www.python.org/downloads/](https://www.python.org/downloads/).
   + Nainstalujte PIP z [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (Pokud není nainstalovaný z instalace Pythonu).
   + Pomocí následujících příkazů ověřte, zda jsou Python a PIP úspěšně nainstalovány. Volitelné

        ![Ověření verze Python PIP – příkaz](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Místo používání výchozí verze macOS se doporučuje ručně nainstalovat Python.

2. Nainstalujte **virtualenv** spuštěním příkazu níže.

   ```
   pip install virtualenv
   ```

## <a name="other-packages"></a>Další balíčky

Pokud narazíte na chybovou zprávu, nainstalujte požadované balíčky spuštěním následujících příkazů:

   ![Nainstalovat balíček libkrb5 pro Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```
sudo apt-get install libkrb5-dev
```

```
sudo apt-get install python-dev
```

Restartujte vs Code a pak se vraťte do editoru skriptů, na kterém běží **HDInsight: PySpark Interactive**

## <a name="next-steps"></a>Další kroky

### <a name="demo"></a>Ukázka
* HDInsight pro VS Code: [Video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití nástroje Azure HDInsight pro Visual Studio Code](hdinsight-for-vscode.md)
* [Použití Azure Toolkit for IntelliJ k vytváření a odesílání Apache Spark aplikací Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím SSH](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím sítě VPN](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů HDInsight v Azure Toolkit for Eclipse k vytváření Apache Sparkch aplikací](spark/apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](spark/apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](spark/apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
* [Vizualizace Apache Hive dat pomocí Microsoft Power BI ve službě Azure HDInsight](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [Použití Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](./interactive-query/hdinsight-connect-hive-zeppelin.md)
