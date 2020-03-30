---
title: Nelze se přihlásit do clusteru Azure HDInsight
description: Řešení potíží s tím, proč se nelze přihlásit do clusteru Apache Hadoop v Azure HDInsightu
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: 2099d1b7583017733498946a5866ab37de43ba9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894058"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Scénář: Nelze se přihlásit do clusteru Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při interakci s clustery Azure HDInsight.

## <a name="issue"></a>Problém

Nelze se přihlásit do clusteru Azure HDInsight.

## <a name="cause"></a>Příčina

Důvody se mohou lišit. Nezapomeňte, že při přihlašování k řídicím panelu clusteru nebo aplikace použijte přihlašovací údaje "přihlášení clusteru" nebo http. Ke vzdálenému připojení použijte přihlašovací údaje Vzdálené plochy nebo Secure Shell (SSH).

## <a name="resolution"></a>Řešení

Při řešení běžných problémů zkuste použít jeden nebo několik následujících kroků.

* Zkuste otevřít řídicí panel clusteru na nové kartě prohlížeče v režimu ochrany osobních údajů.

* Pokud si nemůžete vzpomenout na svá pověření SSH, můžete [obnovit pověření v rámci ui Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
