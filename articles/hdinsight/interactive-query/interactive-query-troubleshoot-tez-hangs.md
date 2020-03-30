---
title: Aplikace Apache Tez přestane reagovat v Azure HDInsight
description: Aplikace Apache Tez přestane reagovat v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: ec5a0d6e8c0a5236ae3929560e81033d983d4dfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75895113"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Scénář: Aplikace Apache Tez přestane reagovat v Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Po odeslání úlohy Apache Hive je z pohledu Tez stav úlohy "Spuštěno", ale nezdá se, že by dosáhl nějakého pokroku

## <a name="cause"></a>Příčina

Příliš mnoho pracovních míst předložených; dlouhá fronta příze.

## <a name="resolution"></a>Řešení

Vertikálně navýšit kapacitu clusteru, nebo jen počkejte, až se vyprázdní fronta příze.

Ve `yarn.scheduler.capacity.maximum-applications` výchozím nastavení řídí maximální počet spuštěných nebo čekajících `10000`aplikací a ve výchozím nastavení je nastaven na hodnotu .

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure pro zlepšení zákaznického prostředí. Propojení komunity Azure se správnými prostředky: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najděte v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
