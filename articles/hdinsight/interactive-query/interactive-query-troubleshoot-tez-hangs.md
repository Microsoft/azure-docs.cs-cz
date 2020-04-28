---
title: Apache Tez aplikace přestane reagovat v Azure HDInsight
description: Apache Tez aplikace přestane reagovat v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: ec5a0d6e8c0a5236ae3929560e81033d983d4dfb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "75895113"
---
# <a name="scenario-apache-tez-application-hangs-in-azure-hdinsight"></a>Scénář: Apache Tez aplikace přestane reagovat ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Po odeslání Apache Hive úlohy se v tez zobrazí stav úlohy je spuštěno, ale neprojeví se v něm žádný průběh.

## <a name="cause"></a>Příčina

Bylo odesláno příliš mnoho úloh; fronta dlouhého příze.

## <a name="resolution"></a>Řešení

Naplánujte horizontální navýšení kapacity clusteru nebo stačí počkat, až se vyprázdní fronta příze.

Ve výchozím `yarn.scheduler.capacity.maximum-applications` nastavení řídí maximální počet aplikací, které jsou spuštěny nebo čekají na vyřízení, `10000`a má výchozí hodnotu.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
