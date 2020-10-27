---
title: Řešení chyb při vytváření prostředků ve službě Azure HDInsight
description: V tomto článku jsou uvedené běžné chyby a techniky zmírnění potíží s kapacitou.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.custom: seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: a47781bcdf60d863d6dcf77d70bc0ee496a51388
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538795"
---
# <a name="troubleshoot-resource-creation-failures-in-azure-hdinsight"></a>Řešení chyb při vytváření prostředků ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při interakci se službou Azure HDInsight.

## <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Chyba: nasazení by překročilo kvótu 800.

V Azure platí limit kvóty 800 nasazení na skupinu prostředků. Pro každou skupinu prostředků, předplatné, účet nebo jiné obory se aplikují různé kvóty. Například v rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Při pokusu o nasazení virtuálního počítače s více jádry, než je povoleno, se zobrazí chybová zpráva s oznámením, že kvóta byla překročena.

Pokud chcete tento problém vyřešit, odstraňte nasazení, která už nepotřebujete, pomocí Azure Portal, CLI nebo PowerShellu.

Další informace najdete v tématu [Řešení chyb týkajících se kvót prostředků](../azure-resource-manager/templates/error-resource-quota.md).

## <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Chyba: maximální uzel překročil dostupné jádra v této oblasti.

V rámci předplatného může být nakonfigurované omezení počtu jader v jedné oblasti. Při pokusu o nasazení prostředku s více jádry, než je povoleno, se zobrazí chybová zpráva s oznámením, že kvóta byla překročena.

Pokud chcete požádat o navýšení kvóty, postupujte následovně:

1. Otevřete [Azure Portal](https://portal.azure.com)a vyberte **help + Support (podpora** ).

1. Vyberte **Nová žádost o podporu** .

1. Na kartě **základy** na nové stránce **žádosti o podporu** zadejte tyto informace:

   * **Typ problému:** Vyberte **omezení služby a předplatné (kvóty)** .
   * **Předplatné:** Vyberte předplatné, které chcete upravit.
   * **Typ kvóty:** Vyberte **HDInsight** .

Další informace najdete v tématu [Vytvoření lístku podpory kvůli navýšení počtu jader](hdinsight-capacity-planning.md#quotas).

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se k [@AzureSupport](https://twitter.com/azuresupport) oficiálnímu Microsoft Azuremu účtu pro zlepšení prostředí pro zákazníky. Propojování komunity Azure se správnými zdroji informací: odpovědi, podpora a odborníci.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).