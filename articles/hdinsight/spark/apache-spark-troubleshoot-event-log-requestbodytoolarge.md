---
title: Chyba RequestBodyTooLarge z aplikace Apache Spark App – Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge se zobrazí v protokolu pro aplikaci streamování Apache Spark ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: 73ae646cb083841ee1d55b6c7ce6af7180cef08e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98929435"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge "se zobrazuje v protokolu aplikací streamování aplikace Apache Spark v HDInsight

Tento článek popisuje postup řešení potíží a možná řešení potíží při používání komponent Apache Spark v clusterech Azure HDInsight.

## <a name="issue"></a>Problém

Chyba: `NativeAzureFileSystem ... RequestBodyTooLarge` zobrazí se v protokolu ovladače pro aplikaci Apache Spark streamování.

## <a name="cause"></a>Příčina

Váš soubor protokolu událostí Spark pravděpodobně zasáhne omezení délky souboru pro WASB.

V Spark 2,3 každá aplikace Spark generuje jeden soubor protokolu událostí Spark. Soubor protokolu událostí Spark pro aplikaci pro streamování Sparku se v době, kdy je aplikace spuštěná, dál zvětšuje. V současné době má soubor v WASB limit bloku 50000 a výchozí velikost bloku je 4 MB. Proto je ve výchozí konfiguraci maximální velikost souboru 195 GB. Azure Storage však zvýšila maximální velikost bloku na 100 MB, což efektivně zavedlo omezení jednoho souboru na 4,75 TB. Další informace najdete v tématu [škálovatelnost a výkonnostní cíle pro úložiště objektů BLOB](../../storage/blobs/scalability-targets.md).

## <a name="resolution"></a>Řešení

K dispozici jsou tři řešení pro tuto chybu:

* Zvětšete velikost bloku na až 100 MB. V uživatelském rozhraní Ambari upravte konfigurační vlastnost HDFS `fs.azure.write.request.size` (nebo ji vytvořte v `Custom core-site` oddílu). Nastavte vlastnost na větší hodnotu, například: 33554432. Uložte aktualizovanou konfiguraci a restartujte příslušné součásti.

* Pravidelně zastavte a znovu odešlete úlohu Spark-streamování.

* Pomocí HDFS můžete ukládat protokoly událostí Spark. Použití HDFS pro úložiště může způsobit ztrátu dat událostí Spark během škálování clusteru nebo upgrady Azure.

    1. Udělejte změny v `spark.eventlog.dir` `spark.history.fs.logDirectory` uživatelském rozhraní Ambari a pomocí něj:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Vytvořit adresáře na HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Všechny ovlivněné služby restartujte přes uživatelské rozhraní Ambari.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]