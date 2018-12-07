---
title: Použití Sparku Livy odesílat úlohy do clusteru Spark v Azure HDInsight
description: Zjistěte, jak použít rozhraní Apache Spark REST API k odeslání úloh Spark vzdáleně do clusteru Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 999b5ca1f229677b9e11068f4beaa6e2704afd3d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53012568"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Pomocí rozhraní Apache Spark REST API můžete odesílat vzdálené úlohy ke clusteru HDInsight Spark

Další informace o použití [Apache Livy](https://livy.incubator.apache.org/), [Apache Spark](https://spark.apache.org/) rozhraní REST API, které se používá k odeslání vzdálené úlohy do clusteru Azure HDInsight Spark. Podrobnou dokumentaci najdete v tématu [ http://livy.incubator.apache.org/ ](http://livy.incubator.apache.org/).

Livy můžete použít ke spuštění interaktivních Spark prostředí nebo odesílání úloh služby batch běžet ve Sparku. Tento článek pojednává o pomocí Livy odesílat úlohy služby batch. Fragmenty kódu v tomto článku používáme nástroj cURL k volání rozhraní REST API ke koncovému bodu Livy Spark.

**Požadavky:**

* Cluster Apache Spark ve službě HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* [cURL](https://curl.haxx.se/). Tento článek používá cURL k předvedení jak provádět volání rozhraní REST API pro cluster HDInsight Spark.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Odeslat úlohu služby batch Apache Livy Spark
Před odesláním úlohy služby batch, musíte nahrát soubor jar aplikace v úložišti clusteru přidružené ke clusteru. Můžete k tomu použít nástroj příkazového řádku [**AzCopy**](../../storage/common/storage-use-azcopy.md). Existují různé klienty, které vám umožní nahrát data. Můžete najít další informace o nich v [nahrávání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"

**Příklady**:

* Pokud je soubor .JAR v úložišti clusteru (WASB)
  
        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
* Pokud chcete předat název souboru jar a classname jako součást vstupní soubor (v tomto příkladu input.txt)
  
        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Získejte informace o dávky Livy Spark spuštěné v clusteru
    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Příklady**:

* Pokud chcete načíst všechny listy Livy Spark spuštěné v clusteru:
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
* Pokud chcete načíst konkrétní služby batch pomocí dané ID dávky
  
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="delete-a-livy-spark-batch-job"></a>Odstranit úlohu služby batch Livy Spark
    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Příklad**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-spark-and-high-availability"></a>Livy Spark a vysoké dostupnosti
Livy poskytuje vysokou dostupnost pro Spark úloh spuštěných v clusteru. Tady je několik příkladů.

* Pokud službu Livy přestane fungovat po odeslání úlohy vzdáleně ke clusteru Spark, úloha dál běžet na pozadí. Při zálohování Livy obnoví stav úlohy a sestavy ji zpět.
* Poznámkové bloky Jupyter pro HDInsight využívají Livy v back-endu. Pokud poznámkového bloku běží úloha Sparku a získá restartována služba Livy, zůstane spuštěný buňky kódu poznámkového bloku. 

## <a name="show-me-an-example"></a>Zobrazit příklad
V této části se podíváme na příklady se sparkem Livy k odeslání úlohy služby batch, sledovat průběh úlohy a pak ho odstraňte. Aplikace v tomto příkladu používáme je vyvinutý v tomto článku [vytvoření samostatné aplikace Scala a ke spuštění v clusteru HDInsight Spark](apache-spark-create-standalone-application.md). Tady postup předpokládá, že:

* Již zkopírovali jste přes jar aplikace k účtu úložiště přidruženého clusteru.
* Máte nainstalovaný na počítači, ve kterém chcete tyto kroky CuRL.

Proveďte následující kroky:

1. Dejte nám prosím nejdřív ověřte, že Livy Spark běží v clusteru. Jsme to tak, že načítá se seznam spuštění dávky. Pokud používáte úlohu pomocí Livy poprvé, výstup by měl vrátit nula.
   
        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
   
    Měl zobrazit výstup podobný následujícímu fragmentu kódu:
   
        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
   
    Všimněte si, jak uvádí, že poslední řádek ve výstupu **celkem: 0**, takže je možné žádná spuštění dávek.

2. Dejte nám odešlete úlohu služby batch. Následující fragment kódu používá vstupní soubor (input.txt) k předání názvu jar a názvu třídy jako parametry. Pokud používáte tyto kroky z počítače Windows, pomocí vstupního souboru je doporučený postup.
   
        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
   
    Parametry v souboru **input.txt** jsou definovány takto:
   
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
   
    Všimněte si, jak uvádí, že poslední řádek výstupu **stavu: spouští se**. Také říká, **id: 0**. Tady **0** je ID dávky.

3. Nyní můžete načíst stav této konkrétní služby batch pomocí ID dávky.
   
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
   
    Výstup teď zobrazují **stav: Úspěch**, takže je možné, že se úloha úspěšně dokončila.

4. Pokud chcete, můžete nyní odstranit služby batch.
   
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
   
    Poslední řádek výstupu ukazuje, že dávka byla úspěšně odstraněna. Odstraňuje se úloha, když je spuštěn, ukončuje také úlohu. Pokud odstraníte úlohu, která byla dokončena úspěšně, nebo v opačném případě odstraní informace o úloze úplně.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aktualizace konfigurace Livy od verze 3.5 pro HDInsight

HDInsight 3.5 clusterů a výše, ve výchozím nastavení, zakázat použití místní cesty souborů přístup ukázkových datových souborů nebo souborů JAR. Doporučujeme vám použít `wasb://` cesta místo pro přístup k kromě souborů JAR nebo ukázková data souborů z clusteru. Pokud chcete použít místní cestu, je nutné aktualizovat konfiguraci Ambari odpovídajícím způsobem. Postupujte následovně:

1. Přejděte na portál Ambari clusteru. Webové uživatelské rozhraní Ambari je k dispozici v clusteru HDInsight na https://**CLUSTERNAME**. azurehdidnsight.net, kde CLUSTERNAME představuje název vašeho clusteru.

2. V levém navigačním panelu klikněte na **Livy**a potom klikněte na tlačítko **Configs**.

3. V části **livy výchozí** přidejte název vlastnosti `livy.file.local-dir-whitelist` a nastavte její hodnotu na **"/"** Pokud budete chtít povolit úplný přístup k systému souborů. Pokud chcete povolit přístup jenom pro konkrétní adresář, zadejte cestu k tomuto adresáři jako hodnotu.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Odesílání úloh Livy pro cluster v rámci virtuální sítě Azure

Pokud se připojíte ke clusteru HDInsight Spark z v rámci virtuální sítě Azure, můžete přímo připojit k Livy v clusteru. V takovém případě je adresa URL pro koncový bod Livy `http://<IP address of the headnode>:8998/batches`. Tady **8998** je port, na kterém poběží Livy hlavního uzlu clusteru. Další informace o přístup ke službám na jiných veřejných portech najdete v tématu [portů používaných služeb Apache Hadoop v HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="troubleshooting"></a>Řešení potíží

Tady jsou některé problémy, které se můžete setkat při používání Livy pro odeslání vzdálené úlohy do clusterů Spark.

### <a name="using-an-external-jar-from-the-additional-storage-is-not-supported"></a>Použití externí jar z dodatečné úložiště se nepodporuje

**Problém:** Pokud vaše úloha Sparku Livy odkazuje na externí jar z účtu úložiště přidružené ke clusteru, úloha se nezdaří.

**Řešení:** Ujistěte se, že soubor jar, který chcete použít je k dispozici ve výchozím nastavení úložiště přidružené ke clusteru HDInsight.





## <a name="next-step"></a>Další krok

* [Dokumentace k rozhraní REST API Apache Livy](http://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

