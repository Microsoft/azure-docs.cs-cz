---
title: Nepovedlo se přihlásit ke clusteru Azure HDInsight.
description: Řešení potíží s tím, proč se nepovedlo přihlásit Apache Hadoop clusteru v Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894058"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scénář: nejde se přihlásit ke clusteru Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nepovedlo se přihlásit ke clusteru Azure HDInsight.

## <a name="cause"></a>Příčina

Důvody se mohou lišit. Mějte na paměti, že při přihlašování ke clusteru nebo řídicím panelům aplikací použijte přihlašovací údaje clusteru nebo přihlašovací údaje HTTP. Ke vzdálenému připojení použijte přihlašovací údaje Vzdálené plochy nebo Secure Shell (SSH).

## <a name="resolution"></a>Řešení

Při řešení běžných problémů zkuste použít jeden nebo několik následujících kroků.

* Zkuste otevřít řídicí panel clusteru na nové kartě prohlížeče v režimu ochrany osobních údajů.

* Pokud nemůžete odvolat přihlašovací údaje SSH, můžete [přihlašovací údaje resetovat v uživatelském rozhraní Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
