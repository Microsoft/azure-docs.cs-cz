---
title: PySpark interaktivní prostředí pomocí nástrojů Azure HDInsight
description: Naučte se používat nástroje Azure HDInsight pro Visual Studio Code k vytváření a odesílání dotazů a skriptů.
keywords: VScode, nástroje Azure HDInsight, podregistr, Python, PySpark, Spark, HDInsight, Hadoop, LLAP, interaktivní podregistr, interaktivní dotaz
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-python
ms.date: 04/23/2020
ms.openlocfilehash: a6247116cdf579691e48883231f57712da36c4ad
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864563"
---
# <a name="set-up-the-pyspark-interactive-environment-for-visual-studio-code"></a>Nastavení PySpark interaktivního prostředí pro Visual Studio Code

Následující kroky ukazují, jak nastavit interaktivní prostředí PySpark v VSCode. Tento krok je určen pouze pro uživatele, kteří nejsou v systému Windows.

K sestavení virtuálního prostředí v cestě domů používáme příkaz **Python/PIP** . Pokud chcete použít jinou verzi, musíte ručně změnit výchozí verzi příkazu **Python/PIP** . Další podrobnosti najdete v tématu [aktualizace – alternativy](https://linux.die.net/man/8/update-alternatives).

1. Nainstalujte [Python](https://www.python.org/downloads/) a [PIP](https://pip.pypa.io/en/stable/installing/).

   * Nainstalujte Python z [https://www.python.org/downloads/](https://www.python.org/downloads/) . 
   * Nainstalujte PIP z [https://pip.pypa.io/en/stable/installing](https://pip.pypa.io/en/stable/installing/) (Pokud není nainstalovaný z instalace Pythonu).
   * Volitelně můžete ověřit, zda jsou Python a PIP úspěšně nainstalovány, pomocí příkazů `python --version` a v `pip --version` uvedeném pořadí. 

     > [!NOTE]
     > Místo používání výchozí verze macOS se doporučuje ručně nainstalovat Python.

2. Nainstalujte **virtualenv** spuštěním příkazu níže.

   ```bash
   pip install virtualenv
   ```

## <a name="other-packages"></a>Další balíčky

Pokud jste v systému Linux nacházeli v níže uvedené chybové zprávě, nainstalujte požadované balíčky spuštěním následujících dvou příkazů.

   :::image type="content" source="./media/set-up-pyspark-interactive-environment/install-libkrb5-package.png" alt-text="Nainstalovat balíček libkrb5 pro Python" border="true":::

```bash
sudo apt-get install libkrb5-dev
```

```bash
sudo apt-get install python-dev
```

Restartujte VSCode a pak se vraťte do editoru VSCode a spusťte příkaz **Spark: PySPark Interactive** .

## <a name="next-steps"></a>Další kroky

### <a name="demo"></a>Ukázka

* HDInsight pro VS Code: [video](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití nástroje Azure HDInsight pro Visual Studio Code](hdinsight-for-vscode.md)
* [Použití Azure Toolkit for IntelliJ k vytváření a odesílání Apache Spark aplikací Scala](spark/apache-spark-intellij-tool-plugin.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](spark/apache-spark-jupyter-notebook-install-locally.md)
