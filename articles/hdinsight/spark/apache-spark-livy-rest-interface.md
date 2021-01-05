---
title: Použití Livy Sparku k odesílání úloh do clusteru Spark v Azure HDInsight
description: Naučte se, jak pomocí Apache Spark REST API vzdáleně odesílat úlohy Spark do clusteru Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: b5795172517ba2e707d66ebe486c51d31575bd0d
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821872"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Odesílání vzdálených úloh do clusteru HDInsight Spark pomocí rozhraní Apache Spark REST API

Naučte se používat [Apache Livy](https://livy.incubator.apache.org/), Apache Spark REST API, která se používá k odesílání vzdálených úloh do clusteru Azure HDInsight Spark. Podrobnou dokumentaci najdete v tématu [Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Livy můžete použít ke spouštění interaktivních prostředí Spark nebo k odeslání dávkových úloh na Spark. Tento článek pojednává o použití Livy k odesílání dávkových úloh. Fragmenty v tomto článku používají oblé k tomu, aby REST API volání do koncového bodu Livy Spark.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Odeslání dávkové úlohy pro Apache Livy Spark

Před odesláním úlohy služby Batch je nutné nahrát jar aplikace do úložiště clusteru přidruženého ke clusteru. Můžete k tomu použít nástroj příkazového řádku [AzCopy](../../storage/common/storage-use-azcopy-v10.md). Existují různé další klienty, které můžete použít k nahrání dat. Další informace najdete v informacích o [nahrání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Příklady

* Pokud je soubor JAR v úložišti clusteru (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Pokud chcete předat název souboru jar a ClassName jako součást vstupního souboru (v tomto příkladu input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Získat informace o dávkách Livy Spark běžících na clusteru

Syntaxe:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
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
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Příklad

Odstranění dávkové úlohy s ID dávky `5` .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark a vysoká dostupnost

Livy poskytuje vysokou dostupnost pro úlohy Sparku spuštěné v clusteru. Tady je několik příkladů.

* Pokud se služba Livy přestane vypínat po vzdáleném odeslání úlohy do clusteru Spark, úloha pokračuje běžet na pozadí. Když se Livy zálohuje, obnoví se stav úlohy a ohlásí se zpátky.
* Jupyter poznámkové bloky pro HDInsight jsou napájené Livy v back-endu. Pokud je v poznámkovém bloku spuštěná úloha Sparku a služba Livy se restartuje, Poznámkový blok bude dál spouštět buňky kódu.

## <a name="show-me-an-example"></a>Zobrazit příklad

V této části se podíváme na příklady, jak pomocí Livy Spark odeslat dávkovou úlohu, monitorovat průběh úlohy a pak ji odstranit. Aplikace, kterou používáme v tomto příkladu, je [vytvořená samostatnou aplikací Scala a spuštění v clusteru HDInsight Spark](apache-spark-create-standalone-application.md). Postup předpokládá:

* Už jste zkopírovali přes jar aplikace do účtu úložiště přidruženého ke clusteru.
* Nastavili jste na počítač, na který se pokoušíte tyto kroky, máte nainstalovanou svou práci.

Proveďte tyto kroky:

1. Pro snadné použití nastavte proměnné prostředí. Tento příklad je založený na prostředí Windows, podle potřeby si můžete prohlédnout proměnné, které jsou potřeba pro vaše prostředí. Nahraďte `CLUSTERNAME` a `PASSWORD` příslušnými hodnotami.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Ověřte, že je v clusteru spuštěný Livy Spark. Můžete to udělat tak, že získáme seznam spuštěných dávek. Pokud spouštíte úlohu s použitím Livy poprvé, výstup by měl vracet nula.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
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

1. Nyní můžeme odeslat dávkovou úlohu. Následující fragment kódu používá vstupní soubor (input.txt) k předání názvu jar a názvu třídy jako parametrů. Pokud spouštíte tyto kroky z počítače s Windows, je doporučený postup pro použití vstupního souboru.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Parametry v souboru **input.txt** jsou definovány takto:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
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

    Všimněte si, jak poslední řádek výstupu říká **stav: spouští** se. Také uvádí, **ID: 0**. Tady je **0** ID dávky.

1. Nyní můžete načíst stav této konkrétní dávky pomocí ID dávky.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
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
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Výstup nyní zobrazuje **stav: úspěch**, což naznačuje, že byla úloha úspěšně dokončena.

1. Pokud chcete, můžete teď dávku odstranit.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
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

    Poslední řádek výstupu znázorňuje, že se dávka úspěšně odstranila. Úloha se odstraní, i když je spuštěná, a také úlohu ukončuje. Odstraníte-li úlohu, která byla dokončena, úspěšně nebo jinak, odstraní informace o úloze.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aktualizace konfigurace Livy počínaje verzí HDInsight 3,5

Clustery HDInsight 3,5 a vyšší, ve výchozím nastavení zakažte používání místních cest k souborům pro přístup k ukázkovým datovým souborům nebo jar. Doporučujeme použít `wasbs://` místo toho cestu k přístupu k jar nebo ukázkovým datovým souborům z clusteru.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Odesílání úloh Livy pro cluster v rámci virtuální sítě Azure

Pokud se připojíte ke clusteru HDInsight Spark z Virtual Network Azure, můžete se přímo připojit k Livy v clusteru. V takovém případě je adresa URL koncového bodu Livy `http://<IP address of the headnode>:8998/batches` . Zde je **8998** port, na kterém Livy běží na hlavnímu uzlu clusteru. Další informace o přístupu ke službám na neveřejných portech najdete v tématu [porty používané Apache Hadoop službami v HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Další kroky

* [Dokumentace k REST API Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)