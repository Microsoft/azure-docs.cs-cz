---
title: Nepovedlo se vytvořit Jupyter Notebook ve službě Azure HDInsight
description: Přečtěte si o krocích při řešení potíží a možných řešeních pro problémy při komunikaci s clustery Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/11/2020
ms.openlocfilehash: d5f6612e22522452efb5eecd14c6c825bf29e6d5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938673"
---
# <a name="unable-to-create-jupyter-notebook-in-azure-hdinsight"></a>Nepovedlo se vytvořit Jupyter Notebook ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění Jupyter Notebook obdržíte chybovou zprávu, která obsahuje:

```error
Cannot convert notebook to v5 because that version doesn't exist
```

## <a name="cause"></a>Příčina

Neshoda verzí.

## <a name="resolution"></a>Řešení

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Otevřete `_version.py` spuštěním následujícího příkazu:

    ```bash
    sudo nano /usr/bin/anaconda/lib/python2.7/site-packages/nbformat/_version.py
    ```

1. Změňte **5** na **4** , takže upravený řádek se zobrazí takto:

    ```python
    version_info = (4, 0, 3)
    ```

    Uložte změny zadáním **CTRL + X**, **Y** a **ENTER**.

1. Z webového prohlížeče přejděte do `https://CLUSTERNAME.azurehdinsight.net/#/main/services/JUPYTER` umístění, kde `CLUSTERNAME` je název vašeho clusteru.

1. Restartujte službu Jupyter.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
