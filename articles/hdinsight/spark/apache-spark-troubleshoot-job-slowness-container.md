---
title: Pomalé Apache Spark, když kontejner úložiště Azure obsahuje mnoho souborů – HDInsight
description: Apache Spark úloha běží pomalu, pokud kontejner úložiště Azure obsahuje mnoho souborů v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/21/2019
ms.openlocfilehash: 6e92016c0dcd65ee5b7f43c2c257b7d2198d6552
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736265"
---
# <a name="scenario-apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Scénář: Apache Spark úloha běží pomalu, pokud kontejner úložiště Azure obsahuje mnoho souborů v Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění clusteru HDInsight se úloha Apache Spark, která se zapisuje do kontejneru úložiště Azure, zpomaluje, když existuje mnoho souborů nebo podsložek. Například při zápisu do nového kontejneru trvá 20 sekund, ale přibližně 2 minuty při zápisu do kontejneru, který obsahuje soubory 200 tisíc.

## <a name="cause"></a>Příčina

Toto je známý problém Sparku. Zpomalení přicházejí z `ListBlob` operací a `GetBlobProperties` během provádění úlohy Sparku.

Aby bylo možné sledovat oddíly, Spark musí udržovat `FileStatusCache` , který obsahuje informace o struktuře adresářů. Pomocí této mezipaměti může Spark analyzovat cesty a znát dostupné oddíly. Výhodou sledování oddílů je, že Spark při čtení dat mění jenom nezbytné soubory. Aby bylo možné tyto informace aktualizovat v aktuálním stavu, musí Spark při psaní nových dat zobrazit seznam všech souborů v adresáři a aktualizovat tuto mezipaměť.

V Spark 2,1 ale nemusíme aktualizovat mezipaměť po každém zápisu, Spark zkontroluje, jestli se stávající sloupec oddílu shoduje s navrhovanou operací zápisu v aktuální žádosti o zápis, takže bude také vést k vypsání operací na začátku každého zápisu.

Tento problém s výkonem by měl být vyřešen v Spark 2,2 při zápisu dat v režimu připojení.

## <a name="resolution"></a>Řešení

Když vytvoříte dělenou datovou sadu, je důležité použít schéma dělení na oddíly, které omezí počet souborů, které Spark musí v `FileStatusCache`seznamu aktualizovat.

V každé n-tého mikrodávce, kde N% 100 = = 0 (100 je pouze příklad), přesuňte existující data do jiného adresáře, který lze načíst pomocí Sparku.

## <a name="next-steps"></a>Další postup

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [podpory komunity Azure](https://azure.microsoft.com/support/community/).

* Připojte se [@AzureSupport](https://twitter.com/azuresupport) k oficiálnímu Microsoft Azuremu účtu pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure se správnými zdroji: odpověďmi, podporou a odborníky.

* Pokud potřebujete další pomoc, můžete odeslat žádost o podporu z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). V řádku nabídek vyberte **Podpora** a otevřete centrum pro **pomoc a podporu** . Podrobnější informace najdete v tématu [jak vytvořit žádost o podporu Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Přístup ke správě předplatných a fakturační podpoře jsou součástí vašeho předplatného Microsoft Azure a technická podpora je poskytována prostřednictvím některého z [plánů podpory Azure](https://azure.microsoft.com/support/plans/).
