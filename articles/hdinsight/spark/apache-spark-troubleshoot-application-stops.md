---
title: Aplikace Apache Spark Streaming se zastaví po 24 dnech v Azure HDInsight
description: Aplikace Apache Spark Streaming se zastaví po spuštění po dobu 24 dnů a v souborech protokolu nejsou žádné chyby.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: ff410ea1b6c54d2f58babeb20c68fe95033e9728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894440"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scénář: Aplikace Apache Spark Streaming se zastaví po spuštění po dobu 24 dnů v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Aplikace Apache Spark Streaming se zastaví po spuštění po dobu 24 dnů a v souborech protokolu nejsou žádné chyby.

## <a name="cause"></a>Příčina

Hodnota `livy.server.session.timeout` určuje, jak dlouho apache livy by měl čekat na dokončení relace. Jakmile délka relace dosáhne `session.timeout` hodnoty, livy relace a aplikace jsou automaticky usmrcen.

## <a name="resolution"></a>Řešení

U dlouhotrvajících úloh zvyšte `livy.server.session.timeout` hodnotu použití uj. Ke konfiguraci Livy můžete přistupovat z uzuliny Ambari pomocí adresy URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs`.

Nahraďte `<yourclustername>` název clusteru HDInsight, jak je znázorněno na portálu.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
