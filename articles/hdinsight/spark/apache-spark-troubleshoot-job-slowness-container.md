---
title: Apache Spark pomalu, když má Azure HDInsight Storage mnoho souborů
description: Apache Spark úloha běží pomalu, pokud kontejner úložiště Azure obsahuje mnoho souborů v Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/21/2019
ms.openlocfilehash: d256292956b42c65722ea5f9c87d6835297d1d40
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219089"
---
# <a name="apache-spark-job-run-slowly-when-the-azure-storage-container-contains-many-files-in-azure-hdinsight"></a>Úlohy Apache Sparku jsou pomalé, když kontejner úložiště Azure obsahuje velké množství souborů ve službě Azure HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Při spuštění clusteru HDInsight se úloha Apache Spark, která se zapisuje do kontejneru úložiště Azure, zpomaluje, když existuje mnoho souborů nebo podsložek. Například při zápisu do nového kontejneru trvá 20 sekund, ale přibližně 2 minuty při zápisu do kontejneru, který obsahuje soubory 200 tisíc.

## <a name="cause"></a>Příčina

Toto je známý problém Sparku. Zpomalení přicházejí z `ListBlob` `GetBlobProperties` operací a během provádění úlohy Sparku.

Aby bylo možné sledovat oddíly, Spark musí udržovat, `FileStatusCache` který obsahuje informace o struktuře adresářů. Pomocí této mezipaměti může Spark analyzovat cesty a znát dostupné oddíly. Výhodou sledování oddílů je, že Spark při čtení dat mění jenom nezbytné soubory. Aby bylo možné tyto informace aktualizovat v aktuálním stavu, musí Spark při psaní nových dat zobrazit seznam všech souborů v adresáři a aktualizovat tuto mezipaměť.

V Spark 2,1 ale nemusíme aktualizovat mezipaměť po každém zápisu, Spark zkontroluje, jestli se stávající sloupec oddílu shoduje s navrhovanou operací zápisu v aktuální žádosti o zápis, takže bude také vést k vypsání operací na začátku každého zápisu.

Tento problém s výkonem by měl být vyřešen v Spark 2,2 při zápisu dat v režimu připojení.

Ve Spark 2,3 se očekává stejné chování jako Spark 2,2.

## <a name="resolution"></a>Řešení

Když vytvoříte dělenou datovou sadu, je důležité použít schéma dělení na oddíly, které omezí počet souborů, které Spark musí v seznamu aktualizovat `FileStatusCache` .

V každé n-tého mikrodávce, kde N %100 = = 0 (100 je pouze příklad), přesuňte existující data do jiného adresáře, který lze načíst pomocí Sparku.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]
