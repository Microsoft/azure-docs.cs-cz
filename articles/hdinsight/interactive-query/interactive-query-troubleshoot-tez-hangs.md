---
title: Apache Tez aplikace přestane reagovat v Azure HDInsight
description: Apache Tez aplikace přestane reagovat v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/09/2019
ms.openlocfilehash: 4e4d1ec77d989fd354e8b3938a052be94ff4ba34
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92539628"
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