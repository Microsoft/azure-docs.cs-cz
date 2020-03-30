---
title: V Azure HDInsight nelze číst protokol apache příze
description: Řešení potíží s kroky a možná řešení problémů při interakci s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/23/2020
ms.openlocfilehash: f7861ae4980f13fbd87780ab2d26fa3376f33532
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76776193"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>Scénář: V Azure HDInsight nelze číst protokol apache příze

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Protokoly příze Apache nalezené z účtu úložiště nejsou čitelné pro člověka. Analyzátor souborů nefunguje a vytvoří následující chybovou zprávu:

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>Příčina

Protokol Apache Yarn je `IndexFile` agregován do formátu, což analyzátor souborů nepodporuje.

## <a name="resolution"></a>Řešení

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Z ui Ambari přejděte na **YARN** > **Configs** > **Advanced Advanced** > **yarn-site**.

1. Pro úložiště WASB: Výchozí `yarn.log-aggregation.file-formats` `IndexedFormat,TFile`hodnota pro je . Změňte hodnotu na `TFile`.

1. Pro úložiště ADLS: Výchozí `yarn.nodemanager.log-aggregation.compression-type` `gz`hodnota pro je . Změňte hodnotu na `none`.

1. Uložte změnu a restartujte všechny služby obsahující tuto chybu.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
