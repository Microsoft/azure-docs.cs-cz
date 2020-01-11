---
title: Nepovedlo se přihlásit k Zeppelin ve službě Azure HDInsight.
description: Nepovedlo se přihlásit k Zeppelin ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/12/2019
ms.openlocfilehash: e9a81d458d1bab68bf94e9e9d0ebd87fac4580c8
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75896152"
---
# <a name="scenario-unable-to-sign-in-to-apache-zeppelin-in-azure-hdinsight"></a>Scénář: nejde se přihlásit k Apache Zeppelin ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Po změně hesla pro přidání ve službě Active Directory se nepovedlo přihlásit k Apache Zeppelin.

## <a name="cause"></a>Příčina

Uživatel uvedený v `activeDirectoryRealm.systemUsername` souboru `shiro_ini` změnil heslo služby Active Directory.

## <a name="resolution"></a>Rozlišení

1. Ověřte, zda je změněné heslo hlavní příčinou zahrnutí `activeDirectoryRealm.systemPassword = <new password>` v konfiguraci `shiro_ini` Zeppelin v Ambari. Odeberte nastavení `activeDirectoryRealm.hadoopSecurityCredentialPath`. Níže je umístění `shiro ini`.

    ![Shiro](./media/domain-joined-zeppelin-signin/shiro.png)

1. Pokud se teď uživatelé můžou k Zeppelin přihlašovat po kroku 1, vytvořte nový soubor `jceks` s novým heslem a nahraďte `activeDirectoryRealm.hadoopSecurityCredentialPath` novým souborem.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
