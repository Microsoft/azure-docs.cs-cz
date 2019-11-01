---
title: Apache Spark pomalu, když má Azure HDInsight Storage mnoho souborů
description: Apache Spark úloha běží pomalu, pokud kontejner úložiště Azure obsahuje mnoho souborů v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/21/2019
ms.openlocfilehash: 82280532fa91923bd08d8ff3164dc841282c392c
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241160"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Apache Spark úloha běží pomalu, pokud kontejner úložiště Azure obsahuje mnoho souborů v Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění clusteru HDInsight se úloha Apache Spark, která se zapisuje do kontejneru úložiště Azure, zpomaluje, když existuje mnoho souborů nebo podsložek. Například při zápisu do nového kontejneru trvá 20 sekund, ale přibližně 2 minuty při zápisu do kontejneru, který obsahuje soubory 200 tisíc.

## <a name="cause"></a>Příčina

Toto je známý problém Sparku. Zpomalení přicházejí z operací `ListBlob` a `GetBlobProperties` během provádění úlohy Sparku.

Aby mohl Spark sledovat oddíly, musí udržovat `FileStatusCache`, který obsahuje informace o struktuře adresářů. Pomocí této mezipaměti může Spark analyzovat cesty a znát dostupné oddíly. Výhodou sledování oddílů je, že Spark při čtení dat mění jenom nezbytné soubory. Aby bylo možné tyto informace aktualizovat v aktuálním stavu, musí Spark při psaní nových dat zobrazit seznam všech souborů v adresáři a aktualizovat tuto mezipaměť.

V Spark 2,1 ale nemusíme aktualizovat mezipaměť po každém zápisu, Spark zkontroluje, jestli se stávající sloupec oddílu shoduje s navrhovanou operací zápisu v aktuální žádosti o zápis, takže bude také vést k vypsání operací na začátku každého zápisu.

Tento problém s výkonem by měl být vyřešen v Spark 2,2 při zápisu dat v režimu připojení.

## <a name="resolution"></a>Rozlišení

Když vytvoříte dělenou datovou sadu, je důležité použít schéma dělení na oddíly, které omezí počet souborů, které Spark musí uvést k aktualizaci `FileStatusCache`.

V každé n-tého mikrodávce, kde N %100 = = 0 (100 je pouze příklad), přesuňte existující data do jiného adresáře, který lze načíst pomocí Sparku.

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení prostředí pro zákazníky tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
