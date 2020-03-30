---
title: Výjimka úložiště po resetování připojení v Azure HDInsight
description: Výjimka úložiště po resetování připojení v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/08/2019
ms.openlocfilehash: a7af6407191577112f936bfb9048985e85c868ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887219"
---
# <a name="scenario-storage-exception-after-connection-reset-in-azure-hdinsight"></a>Scénář: Výjimka úložiště po resetování připojení v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nelze vytvořit novou tabulku Apache HBase.

## <a name="cause"></a>Příčina

Během procesu zkrácení tabulky došlo k problému s připojením úložiště. Položka tabulky byla odstraněna v tabulce metadat HBase. Všechny kromě jednoho souboru objektů blob byly odstraněny.

I když tam byl `/hbase/data/default/ThatTable` žádný objekt blob složky volal sedí v úložišti. Ovladač WASB našel existenci výše uvedeného souboru objektů blob a `/hbase/data/default/ThatTable` neumožnil vytvořit žádný objekt blob volaný, protože předpokládal, že nadřazené složky existují, takže vytvoření tabulky se nezdaří.

## <a name="resolution"></a>Řešení

1. Z ui Apache Ambari restartujte aktivní HMaster. To umožní jeden ze dvou pohotovostních HMaster stává aktivní a nový aktivní HMaster znovu načte informace o tabulce metadat. Tak neuvidíte `already-deleted` tabulku v HMaster ui.

1. Osamocený soubor blob najdete z nástrojů ui, jako je Cloud Explorer nebo spuštění příkazu jako `hdfs dfs -ls /xxxxxx/yyyyy`. Chcete-li odstranit tento objekt blob, spusťte jej. `hdfs dfs -rmr /xxxxx/yyyy` Například, `hdfs dfs -rmr /hbase/data/default/ThatTable/ThatFile`.

Nyní můžete vytvořit novou tabulku se stejným názvem v HBase.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
