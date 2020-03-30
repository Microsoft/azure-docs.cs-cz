---
title: Apache Spark je pomalý, když má úložiště Azure HDInsight mnoho souborů
description: Úloha Apache Spark běží pomalu, když kontejner úložiště Azure obsahuje mnoho souborů v Azure HDInsightu
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: e389c05a6de85287bc86eff510e137f470837e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894322"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Úlohy Apache Sparku jsou pomalé, když kontejner úložiště Azure obsahuje velké množství souborů ve službě Azure HDInsight

Tento článek popisuje kroky řešení potíží a možná řešení problémů při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění clusteru HDInsight se úloha Apache Spark, která zapisuje do kontejneru úložiště Azure, stane pomalou, když existuje mnoho souborů/podsložek. Například trvá 20 sekund při zápisu do nového kontejneru, ale asi 2 minuty při zápisu do kontejneru, který má soubory 200 kS.

## <a name="cause"></a>Příčina

Jedná se o známý problém Spark. Pomalost pochází `ListBlob` `GetBlobProperties` z a operace během provádění úlohy Spark.

Chcete-li sledovat oddíly, `FileStatusCache` Spark musí udržovat, který obsahuje informace o adresářové struktuře. Pomocí této mezipaměti spark můžete analyzovat cesty a být vědomi dostupných oddílů. Výhodou sledování oddílů je, že Spark se při čtení dat dotýká pouze potřebných souborů. Aby byly tyto informace aktuální, musí Spark při psaní nových dat vypsat všechny soubory pod adresářem a aktualizovat tuto mezipaměť.

V Spark 2.1, i když nepotřebujeme aktualizovat mezipaměť po každém zápisu, Spark zkontroluje, zda existující sloupec oddílu odpovídá navrhovanému v aktuálním požadavku na zápis, takže také povede k výpisu operací na začátku každého zápisu.

V Spark 2.2 při zápisu dat s režimem připojení by měl být tento problém s výkonem opraven.

## <a name="resolution"></a>Řešení

Při vytváření sady dělených dat je důležité použít schéma dělení, které omezí počet souborů, které `FileStatusCache`má Spark vypsat, aby aktualizoval .

Pro každou n-té mikro dávku, kde N % 100 == 0 (100 je jen příklad), přesuňte existující data do jiného adresáře, který může být načten Spark.

## <a name="next-steps"></a>Další kroky

Pokud jste problém nezjistili nebo se vám nedaří problém vyřešit, navštivte jeden z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory Azure Community Support](https://azure.microsoft.com/support/community/).

* Spojte [@AzureSupport](https://twitter.com/azuresupport) se s oficiálním účtem Microsoft Azure, který zlepšuje zákaznickou zkušenost tím, že propojuje komunitu Azure se správnými prostředky: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [webu Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na řádku nabídek vyberte **Podpora** nebo otevřete centrum **Nápověda + podpora.** Podrobnější informace najdete v části [Jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Přístup ke správě předplatného a fakturační podpoře je součástí vašeho předplatného Microsoft Azure a technická podpora se poskytuje prostřednictvím jednoho z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
