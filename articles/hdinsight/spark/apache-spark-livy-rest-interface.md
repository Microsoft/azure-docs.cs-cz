---
title: Použití Livy Spark k odesílání úloh do clusteru Spark na Azure HDInsight
description: Přečtěte si, jak pomocí rozhraní APACHE Spark REST API vzdáleně odesílat úlohy Spark do clusteru Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: ac3904284ebf20fa1d5e75f9249732be3963f677
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206278"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Odesílání vzdálených úloh do clusteru HDInsight Spark pomocí rozhraní Apache Spark REST API

Naučte se používat [Apache Livy](https://livy.incubator.apache.org/), rozhraní APACHE Spark REST API, které se používá k odesílání vzdálených úloh do clusteru Azure HDInsight Spark. Podrobnou dokumentaci naleznete v [tématu Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Livy můžete použít ke spuštění interaktivních prostředí Spark nebo k odeslání dávkových úloh, které mají být spuštěny na Sparku. Tento článek popisuje použití Livy k odeslání dávkových úloh. Úryvky v tomto článku používají cURL k volání rozhraní REST API do koncového bodu Livy Spark.

## <a name="prerequisites"></a>Požadavky

Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Odeslání dávkové úlohy Apache Livy Spark

Před odesláním dávkové úlohy je nutné nahrát nádobu aplikace do úložiště clusteru přidruženého ke clusteru. Můžete k tomu použít nástroj příkazového řádku [AzCopy](../../storage/common/storage-use-azcopy.md). Existují různé další klienty, které můžete použít k nahrání dat. Více o nich najdete na [uploadu dat pro pracovní místa Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Příklady

* Pokud je soubor jar v úložišti clusteru (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Pokud chcete předat název souboru jar a název třídy jako součást vstupního souboru (v tomto příkladu input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Získání informací o daržích Livy Spark spuštěné v clusteru

Syntaxe:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Příklady

* Pokud chcete načíst všechny dávky Livy Spark spuštěné v clusteru:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Pokud chcete načíst konkrétní dávku s daným ID dávky

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Odstranění dávkové úlohy Livy Spark

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Příklad

Odstranění dávkové úlohy s `5`ID dávky .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark a vysoká dostupnost

Livy poskytuje vysokou dostupnost pro úlohy Spark spuštěné v clusteru. Zde je několik příkladů.

* Pokud služba Livy klesne po vzdáleném odeslání úlohy do clusteru Spark, úloha bude nadále spuštěna na pozadí. Když livy je zálohovat, obnoví stav úlohy a hlásí ji zpět.
* Jupyter notebooky pro HDInsight jsou poháněny Livy v back-endu. Pokud poznámkový blok používá úlohu Spark a služba Livy se restartuje, poznámkový blok bude dál spouštět buňky kódu.

## <a name="show-me-an-example"></a>Ukažte mi příklad

V této části se podíváme na příklady použití Livy Spark k odeslání dávkové úlohy, sledování průběhu úlohy a jeho odstranění. Aplikace, kterou používáme v tomto příkladu, je aplikace vyvinutá v článku [Vytvořte samostatnou aplikaci Scala a spusťte v clusteru HDInsight Spark](apache-spark-create-standalone-application.md). Kroky zde předpokládají:

* Už jste zkopírovali přes nádobu aplikace do účtu úložiště přidruženého ke clusteru.
* Máte CuRL nainstalován na počítači, kde se snažíte tyto kroky.

Proveďte následující kroky:

1. Pro snadné použití nastavte proměnné prostředí. Tento příklad je založen na prostředí systému Windows, revidovat proměnné podle potřeby pro vaše prostředí. `CLUSTERNAME`Nahraďte `PASSWORD` a s příslušnými hodnotami.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Ověřte, zda je v clusteru spuštěna Livy Spark. Můžeme tak učinit získáním seznamu spuštěných šarží. Pokud spouštěte úlohu pomocí Livy poprvé, výstup by měl vrátit nulu.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Měli byste získat výstup podobný následujícímu fragmentu:

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

    Všimněte si, jak poslední řádek ve výstupu říká **total:0**, což naznačuje, žádné spuštěné dávky.

1. Dovolte nám nyní odeslat dávkovou úlohu. Následující úryvek používá vstupní soubor (input.txt) k předání názvu jar a názvu třídy jako parametrů. Pokud používáte tyto kroky z počítače se systémem Windows, je doporučeným přístupem použití vstupního souboru.

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

    Všimněte si, jak poslední řádek výstupu **říká, že stav:starting**. To také říká, **id:0**. Zde **je 0** ID dávky.

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

    Výstup nyní zobrazuje **state:success**, což naznačuje, že úloha byla úspěšně dokončena.

1. Pokud chcete, můžete nyní dávku odstranit.

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

    Poslední řádek výstupu ukazuje, že dávka byla úspěšně odstraněna. Odstranění úlohy, když je spuštěna, také zabije úlohu. Pokud odstraníte úlohu, která byla úspěšně dokončena nebo jinak, odstraní informace o úloze úplně.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aktualizace konfigurace Livy počínaje verzí HDInsight 3.5

Clustery HDInsight 3.5 a vyšší ve výchozím nastavení zakážou použití místních cest k souborům pro přístup k ukázkovým datovým souborům nebo sklenicím. Doporučujeme místo toho `wasbs://` použít cestu k přístupu ke sklenicím nebo ukázkovým datovým souborům z clusteru.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Odesílání úloh Livy pro cluster v rámci virtuální sítě Azure

Pokud se připojíte ke clusteru HDInsight Spark z virtuální sítě Azure, můžete se přímo připojit k Livy v clusteru. V takovém případě je `http://<IP address of the headnode>:8998/batches`adresa URL koncového bodu Livy . Zde je **8998** port, na kterém Livy běží na hlavovém uzlu clusteru. Další informace o přístupu ke službám na neveřejných portech najdete v [tématu Porty používané službami Apache Hadoop na hdinsightu](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Další kroky

* [Dokumentace rozhraní APACHE Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
