---
title: PySpark interaktivní prostředí pomocí nástrojů Azure HDInsight
description: Naučte se používat nástroje Azure HDInsight pro Visual Studio Code k vytváření a odesílání dotazů a skriptů.
keywords: VScode, nástroje Azure HDInsight, podregistr, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktivní podregistr, interaktivní dotaz
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 47f774f7b848e34aa7dc219ee4b08fb083cbd813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188204"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Nastavení PySpark interaktivního prostředí pro Visual Studio Code

Následující kroky ukazují, jak nastavit PySpark interaktivní prostředí v VS Code.

K sestavení virtuálního prostředí v cestě domů používáme příkaz **Python/PIP** . Pokud chcete použít jinou verzi, musíte ručně změnit výchozí verzi příkazu **Python/PIP** . Další podrobnosti najdete v tématu [aktualizace – alternativy](https://linux.die.net/man/8/update-alternatives).

1. Nainstalujte [Python](https://www.python.org/downloads/) a [PIP](https://pip.pypa.io/en/stable/installing/).

   * Nainstalujte Python z [https://www.python.org/downloads/](https://www.python.org/downloads/).
   * Nainstalujte PIP z [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (Pokud není nainstalovaný z instalace Pythonu).
   * Pomocí následujících příkazů ověřte, zda jsou Python a PIP úspěšně nainstalovány. (Nepovinné)

        ![Ověření verze Python PIP – příkaz](./media/set-up-pyspark-interactive-environment/check-python-pip-version.png)

     > [!NOTE]
     > Místo používání výchozí verze macOS se doporučuje ručně nainstalovat Python.

2. Nainstalujte **virtualenv** spuštěním příkazu níže.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Další balíčky

Pokud jste pocházeli v chybové zprávě, nainstalujte požadované balíčky spuštěním následujících příkazů:

   ![Nainstalovat balíček libkrb5 pro Python](./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png)

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Restartujte VS Code a pak se vraťte do editoru skriptů, na kterém běží **HDInsight: PySpark Interactive**.

## <a name="next-steps"></a>Další kroky

### <a name="demo"></a>Ukázka

* HDInsight pro VS Code: [video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití nástroje Azure HDInsight pro Visual Studio Code](hdinsight-for-vscode.md)
* [Použití Azure Toolkit for IntelliJ k vytváření a odesílání Apache Spark aplikací Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
