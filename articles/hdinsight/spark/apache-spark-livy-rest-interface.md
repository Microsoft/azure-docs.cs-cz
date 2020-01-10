---
title: Použití Livy Sparku k odesílání úloh do clusteru Spark v Azure HDInsight
description: Naučte se, jak pomocí Apache Spark REST API vzdáleně odesílat úlohy Spark do clusteru Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: da654beec730d0bfc04548402c1158ebaaf80c6f
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75748365"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Použití Apache Spark REST API k odesílání vzdálených úloh do clusteru HDInsight Spark

Naučte se používat [Apache Livy](https://livy.incubator.apache.org/), [Apache Spark](https://spark.apache.org/) REST API, která se používá k odesílání vzdálených úloh do clusteru Azure HDInsight Spark. Podrobnou dokumentaci najdete v tématu [https://livy.incubator.apache.org/](https://livy.incubator.apache.org/).

Livy můžete použít ke spouštění interaktivních prostředí Spark nebo k odeslání dávkových úloh na Spark. Tento článek pojednává o použití Livy k odesílání dávkových úloh. Fragmenty v tomto článku používají oblé k tomu, aby REST API volání do koncového bodu Livy Spark.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](https://curl.haxx.se/). V tomto článku se používá oblé k předvedení REST API volání do clusteru HDInsight Spark.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Odeslání dávkové úlohy pro Apache Livy Spark

Před odesláním úlohy služby Batch je nutné nahrát jar aplikace do úložiště clusteru přidruženého ke clusteru. K tomu můžete použít [AzCopy](../../storage/common/storage-use-azcopy.md)(nástroj příkazového řádku). Existují různé další klienty, které můžete použít k nahrání dat. Další informace najdete v informacích o [nahrání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Příklady

* Pokud je soubor JAR v úložišti clusteru (WASB)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Pokud chcete předat název souboru jar a ClassName jako součást vstupního souboru (v tomto příkladu Input. txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Získat informace o dávkách Livy Spark běžících na clusteru

Syntaxe:

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Příklady

* Pokud chcete načíst všechny dávky Livy Spark běžící v clusteru:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
    ```

* Pokud chcete načíst konkrétní dávku s daným ID dávky?

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Odstranění dávkové úlohy Livy Spark

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Příklad:

Odstranění dávkové úlohy pomocí `5`dávky s ID.

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark a vysoká dostupnost

Livy poskytuje vysokou dostupnost pro úlohy Sparku spuštěné v clusteru. Tady je několik příkladů.

* Pokud se služba Livy přestane vypínat po vzdáleném odeslání úlohy do clusteru Spark, úloha pokračuje běžet na pozadí. Když se Livy zálohuje, obnoví se stav úlohy a ohlásí se zpátky.
* Jupyter poznámkové bloky pro HDInsight jsou napájené Livy v back-endu. Pokud je v poznámkovém bloku spuštěná úloha Sparku a služba Livy se restartuje, Poznámkový blok bude dál spouštět buňky kódu. 

## <a name="show-me-an-example"></a>Zobrazit příklad

V této části se podíváme na příklady, jak pomocí Livy Spark odeslat dávkovou úlohu, monitorovat průběh úlohy a pak ji odstranit. Aplikace, kterou používáme v tomto příkladu, je [vytvořená samostatnou aplikací Scala a spuštění v clusteru HDInsight Spark](apache-spark-create-standalone-application.md). Tento postup předpokládá, že:

* Už jste zkopírovali přes jar aplikace do účtu úložiště přidruženého ke clusteru.
* Máte nainstalovanou na počítač, na který chcete provést tento postup.

Proveďte následující kroky:

1. Nejdřív nám ověřte, že na clusteru běží Livy Spark. Můžete to udělat tak, že získáme seznam spuštěných dávek. Pokud spouštíte úlohu s použitím Livy poprvé, výstup by měl vracet nula.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

    Měl by se zobrazit výstup podobný následujícímu fragmentu kódu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Všimněte si, jak poslední řádek ve výstupu uvádí **součet: 0**, který navrhuje žádné spuštěné dávky.

2. Nyní můžeme odeslat dávkovou úlohu. Následující fragment kódu používá vstupní soubor (Input. txt) k předání názvu jar a názvu třídy jako parametrů. Pokud spouštíte tyto kroky z počítače se systémem Windows, je doporučený postup pro použití vstupního souboru.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Parametry v souboru **input. txt** jsou definovány takto:

    ```text
    { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    ```output
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
    ```

    Všimněte si, jak poslední řádek výstupu říká **stav: spouští**se. Také uvádí, **ID: 0**. Tady je **0** ID dávky.

3. Nyní můžete načíst stav této konkrétní dávky pomocí ID dávky.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Výstup nyní zobrazuje **stav: úspěch**, což naznačuje, že byla úloha úspěšně dokončena.

4. Pokud chcete, můžete teď dávku odstranit.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Zobrazený výstup by měl vypadat přibližně jako následující fragment kódu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Poslední řádek výstupu znázorňuje, že se dávka úspěšně odstranila. Úloha se odstraní, když je spuštěná, a také úlohu ukončuje. Odstraníte-li úlohu, která byla dokončena, úspěšně nebo jinak, odstraní informace o úloze.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aktualizace konfigurace Livy počínaje verzí HDInsight 3,5

Clustery HDInsight 3,5 a vyšší, ve výchozím nastavení zakažte používání místních cest k souborům pro přístup k ukázkovým datovým souborům nebo jar. Pro přístup k jar nebo ukázkovým datovým souborům z clusteru doporučujeme použít místo toho cestu `wasb://`.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Odesílání úloh Livy pro cluster v rámci virtuální sítě Azure

Pokud se připojíte ke clusteru HDInsight Spark z Virtual Network Azure, můžete se přímo připojit k Livy v clusteru. V takovém případě je adresa URL koncového bodu Livy `http://<IP address of the headnode>:8998/batches`. Zde je **8998** port, na kterém Livy běží na hlavnímu uzlu clusteru. Další informace o přístupu ke službám na neveřejných portech najdete v tématu [porty používané Apache Hadoop službami v HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Další kroky

* [Dokumentace k REST API Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)