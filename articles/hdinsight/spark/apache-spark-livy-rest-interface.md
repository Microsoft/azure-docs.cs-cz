---
title: Používat k odesílání, úlohy Spark clusteru v Azure HDInsight Livy Spark | Microsoft Docs
description: Naučte se používat k odesílání úloh Spark vzdáleně do clusteru Azure HDInsight Apache Spark REST API.
keywords: Apache spark rest api, livy spark
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2817b779-1594-486b-8759-489379ca907d
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 29cf245a03b38be4f5396a3c83c966a27cf038f3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Použití Apache Spark REST API vzdálené úlohy do clusteru HDInsight Spark

Další informace o použití Livy, Apache Spark REST API, které se používá k odesílání vzdálené úloh do clusteru Azure HDInsight Spark. Podrobnou dokumentaci najdete v tématu [ http://livy.incubator.apache.org/ ](http://livy.incubator.apache.org/).

Livy můžete použít ke spuštění interaktivních Spark nutný nebo odesílat dávkové úlohy můžou běžet na Spark. V tomto článku bude zmíněn pomocí Livy odesílat dávkové úlohy. Fragmenty kódu v tomto článku pomocí cURL provádět volání rozhraní REST API ke koncovému bodu Livy Spark.

**Požadavky:**

* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](http://curl.haxx.se/). Tento článek používá cURL k ukazují, jak provádět volání rozhraní REST API proti clusteru služby HDInsight Spark.

## <a name="submit-a-livy-spark-batch-job"></a>Odeslání Livy Spark dávkovou úlohu
Před odesláním dávkovou úlohu, musíte nahrát jar aplikace v úložišti clusteru, který je přidružen ke clusteru. Můžete použít [ **AzCopy**](../../storage/common/storage-use-azcopy.md), nástroj příkazového řádku, tak. Existují různé klienty, které můžete použít k nahrání data. Můžete najít další informace o nich v [nahrávání dat pro úlohy Hadoop do HDInsight](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Příklady**:

* Pokud je soubor jar v úložišti clusteru (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Pokud chcete předat jako součást vstupní soubor jar název souboru a název třídy (v tomto příkladu vstup.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Informace o Livy Spark dávek běžící v clusteru
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Příklady**:

* Pokud chcete načíst všechny dávky Livy Spark běžící v clusteru:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
* Pokud chcete načíst konkrétní dávky s danou ID dávky
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Odstranit Livy Spark dávkovou úlohu
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Příklad**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark a vysoká dostupnost
Livy poskytuje vysokou dostupnost pro Spark úloh spuštěných v clusteru. Tady je několik příkladů.

* Pokud službu Livy přestane fungovat po odeslání úlohy vzdáleně na clusteru Spark, úloha dál běžet na pozadí. Při zálohování Livy obnoví stav úlohy a sestavy zpátky.
* Poznámkové bloky Jupyter pro HDInsight jsou zapnuté pomocí Livy v back-end. Pokud do poznámkového bloku běží úlohy Spark a získá restartována Livy, poznámkového bloku dál běžet buňky kódu. 

## <a name="show-me-an-example"></a>Zobrazit příklad
V této části se podíváme na příklady použití Livy Spark k odeslání úlohy batch, sledovat průběh úlohy a poté jej odstraňte. Aplikace v tomto příkladu používáme je vyvinuté v článku [vytvořit samostatnou Scala aplikací a ke spuštění v clusteru HDInsight Spark](apache-spark-create-standalone-application.md). Tady postup předpokládá, že:

* Zkopíruje přes jar aplikací mít již k účtu úložiště, který je přidružen ke clusteru.
* Máte CuRL nainstalovaná na počítači, kde se pokoušíte tyto kroky.

Proveďte následující kroky:

1. Dejte nám nejdřív ověřte, zda Livy Spark je spuštěna v clusteru. Jsme to provést tím, že získáme seznam spuštěných dávky. Pokud používáte úlohu pomocí Livy poprvé, výstup by měl vrátit nula.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Měli byste obdržet výstup podobná následující fragment kódu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Všimněte si, jak uvádí poslední řádek ve výstupu **celkem: 0**, která navrhuje žádné spuštěné dávky.

2. Dejte nám teď odešlete dávkovou úlohu. Následující fragment kódu používá vstupní soubor (vstup.txt) jako parametry předat název jar a název třídy. Pokud používáte tyto kroky ze počítači se systémem Windows, pomocí vstupní soubor se o doporučený postup.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Parametry v souboru **vstup.txt** jsou definovány takto:
   
        { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
   
    Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:
   
        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Všimněte si, jak uvádí poslední řádek výstupu **stavu: spouštění**. Také uvádí, **id: 0**. Zde **0** je ID dávky.

3. Nyní můžete načíst stav této konkrétní batch pomocí ID dávky.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Nyní ukazuje výstup **stavu: Úspěch**, který naznačuje, že úloha byla úspěšně dokončena.

4. Pokud chcete, můžete nyní odstranit dávky.
   
        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
   
    Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Poslední řádek výstupu ukazuje, že dávka byla úspěšně odstraněna. Odstranění úlohy, když je spuštěná, také ukončí úlohu. Pokud odstraníte úlohu, která byla dokončena úspěšně, nebo jinak, odstraní informace o úlohách úplně.

## <a name="using-livy-spark-on-hdinsight-35-clusters"></a>Pomocí Livy Spark v clusterech HDInsight 3.5

Cluster HDInsight 3.5, ve výchozím nastavení, zakáže použití místní cesty souborů přístup ukázkových datových souborů nebo souborů JAR. Doporučujeme, abyste použili `wasb://` cesta místo toho k přístupu k JAR nebo vzorová data souborů z clusteru. Pokud chcete používat místní cestu, je nutné aktualizovat konfiguraci Ambari odpovídajícím způsobem. Postupujte následovně:

1. Přejděte na portál Ambari pro cluster. Webové uživatelské rozhraní Ambari je k dispozici v clusteru HDInsight na https://**CLUSTERNAME**. azurehdidnsight.net, kde CLUSTERNAME představuje název clusteru.

2. V levém navigačním, klikněte na **Livy**a potom klikněte na **konfigurací**.

3. V části **livy výchozí** přidejte název vlastnosti `livy.file.local-dir-whitelist` a jeho hodnotu nastavte **"/"** Pokud chcete povolit úplný přístup k systému souborů. Pokud chcete povolit přístup pouze pro konkrétního adresáře, zadejte cestu k tomuto adresáři jako hodnota.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Odesílání úloh Livy pro cluster se v rámci virtuální sítě Azure

Pokud se připojit ke clusteru HDInsight Spark z v rámci virtuální síť Azure, které mohou připojovat přímo k Livy v clusteru. V takovém případě je adresa URL pro koncový bod Livy `http://<IP address of the headnode>:8998/batches`. Zde **8998** je port, na kterém běží Livy na headnode clusteru. Další informace o přístup ke službám na neveřejný portech najdete v tématu [porty používané služby Hadoop v HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Řešení potíží

Tady jsou některé problémy, které se mohou vyskytnout při pro vzdálené úlohy odeslání clustery Spark pomocí Livy.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Použití externí jar z další úložiště není podporováno

**Problém:** Pokud vaše úlohy Livy Spark odkazuje na externí jar z další úložiště účet přidružený ke clusteru, se nezdaří úlohy.

**Řešení:** Ujistěte se, že je k dispozici v výchozí úložiště přidružený k clusteru HDInsight jar, kterou chcete použít.





## <a name="next-step"></a>Další krok

* [Dokumentace k Livy REST API](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

