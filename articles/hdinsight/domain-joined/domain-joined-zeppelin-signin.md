---
title: Nepovedlo se přihlásit k Zeppelin ve službě Azure HDInsight.
description: Nepovedlo se přihlásit k Zeppelin ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/12/2019
ms.openlocfilehash: 14f4cd7588405dbf351124b1e469fd96f9b38274
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68977401"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Scénář: Nepovedlo se přihlásit k Apache Zeppelin ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Po změně hesla pro přidání ve službě Active Directory se nepovedlo přihlásit k Apache Zeppelin.

## <a name="cause"></a>Příčina

Uživatel uvedený v `activeDirectoryRealm.systemUsername` `shiro_ini` souboru změnil heslo služby Active Directory.

## <a name="resolution"></a>Řešení

1. Ověřte, zda je změněné heslo hlavní příčinou zahrnutí `activeDirectoryRealm.systemPassword = <new password>` do konfigurace Zeppelin `shiro_ini` v Ambari. `activeDirectoryRealm.hadoopSecurityCredentialPath` Odeberte nastavení. Níže je umístění `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Pokud se teď uživatelé můžou k Zeppelin přihlašovat po kroku 1, vytvořte nový `jceks` soubor s novým heslem a `activeDirectoryRealm.hadoopSecurityCredentialPath` nahraďte ho novým souborem.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
