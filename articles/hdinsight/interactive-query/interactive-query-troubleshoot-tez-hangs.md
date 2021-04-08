---
title: Apache Tez aplikace přestane reagovat v Azure HDInsight
description: Apache Tez aplikace přestane reagovat v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/09/2019
ms.openlocfilehash: 56c68c26ae953034283031e2427b7a4afadee94e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98935938"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Scénář: Apache Tez aplikace přestane reagovat ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Po odeslání Apache Hive úlohy se v tez zobrazí stav úlohy je spuštěno, ale neprojeví se v něm žádný průběh.

## <a name="cause"></a>Příčina

Bylo odesláno příliš mnoho úloh; fronta dlouhého příze.

## <a name="resolution"></a>Řešení

Naplánujte horizontální navýšení kapacity clusteru nebo stačí počkat, až se vyprázdní fronta příze.

Ve výchozím nastavení `yarn.scheduler.capacity.maximum-applications` řídí maximální počet aplikací, které jsou spuštěny nebo čekají na vyřízení, a má výchozí hodnotu `10000` .

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).