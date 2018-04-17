---
title: Použití nástroje Hadoop Sqoop se Curl v HDInsight - Azure | Microsoft Docs
description: Naučte se vzdáleně odesílání úloh Sqoop do HDInsight pomocí Curl.
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 39798321-78ca-428c-bcfe-322e49af4059
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/03/2018
ms.author: jgao
ms.openlocfilehash: 66c82cc22b9b460281928c4892dab8308ccf1098
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Spuštění úloh Sqoop se systémem Hadoop v prostředí HDInsight pomocí Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Další informace o použití Curl ke spuštění úloh Sqoop na cluster Hadoop v HDInsight.

Curl se používá k ukazují, jak můžete pracovat s HDInsight pomocí nezpracované požadavky HTTP na spouštět, monitorovat a načíst výsledky úlohy Sqoop. To funguje tak, že pomocí WebHCat REST API (dříve označované jako Templeton) poskytované clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky
Pokud chcete provést kroky v tomto článku, budete potřebovat následující:

* Dokončení [Sqoop použití s Hadoop v HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) nakonfigurovat prostředí se HDInsight cluster a Azure SQL database.
* [Curl](http://curl.haxx.se/). Curl je nástroj pro přenos dat z nebo na clusteru HDInsight.
* [jq](http://stedolan.github.io/jq/). Nástroj jq se používá ke zpracování dat JSON vrácená z požadavky REST.

## <a name="submit-sqoop-jobs-by-using-curl"></a>Odesílání úloh Sqoop pomocí Curl
> [!NOTE]
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Název clusteru také musíte použít jako součást identifikátoru URI (Uniform Resource Identifier) sloužícímu k odesílání požadavků na server.
> 
> Pro příkazy v této části nahraďte **UŽIVATELSKÉ JMÉNO** uživatelem pro ověření do clusteru a nahraďte **HESLO** heslem pro uživatelský účet. Nahraďte **CLUSTERNAME** názvem vašeho clusteru.
> 
> Rozhraní API REST je zabezpečeno pomocí [základního ověřování](http://en.wikipedia.org/wiki/Basic_access_authentication). Vždy doporučujeme provádět požadavky pomocí protokolu HTTPS (Secure HTTP) a pomoci tak zajistit, že přihlašovací údaje budou na server odeslány bezpečně.
> 
> 

1. Z příkazového řádku použijte následující příkaz k ověření, zda se můžete připojit ke clusteru HDInsight:

    ```bash   
    curl -u USERNAME:PASSWORD -G https://CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```

    Měla by se zobrazit odpověď podobná následujícímu:

    ```json   
    {"status":"ok","version":"v1"}
    ```
   
    Parametry použité v tomto příkazu jsou následující:
   
   * **-u** – uživatelské jméno a heslo použité pro ověření žádosti.
   * **-G** – označuje, že se jedná o požadavek GET.
     
     Na začátek adresu URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, je stejný pro všechny požadavky. Cesta **/status**, označuje, že požadavek je vrátit stav WebHCat (také známé jako Templeton) pro server. 
2. Použijte následující postupy se odeslat úlohu sqoop:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Parametry použité v tomto příkazu jsou následující:

    * **-d** – od `-G` se nepoužívá, použije se výchozí hodnota žádost na metodu POST. `-d` Určuje datových hodnot, které se odesílají s požadavkem.

        * **User.Name** -uživatel, který spouští příkaz.

        * **příkaz** -Sqoop příkaz k provedení.

        * **statusdir** -adresáře, který budou zapisovat do stavu pro tuto úlohu.

    Tento příkaz vrátí ID úlohy, který slouží ke kontrole stavu úlohy.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Chcete-li zkontrolovat stav úlohy, použijte následující příkaz. Nahraďte **JOBID** se hodnota vrácená v předchozím kroku. Například, pokud byl návratovou hodnotu `{"id":"job_1415651640909_0026"}`, pak **JOBID** by `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Pokud se úloha dokončí, bude stav **úspěšné**.
   
   > [!NOTE]
   > Tento požadavek Curl vrátí dokument JavaScript Object Notation (JSON) s informacemi o úloze; jq se používá k načtení jenom hodnotu stavu.
   > 
   > 
4. Jakmile se stav úlohy se změnila na **úspěšné**, můžete načíst výsledky úlohy z Azure Blob storage. `statusdir` Parametr předaný s dotaz obsahuje umístění výstupního souboru; v tomto případě **wasb: / / / Příklad/data/sqoop/curl**. Tato adresa ukládá výstup úlohy v **příklad/data/sqoop/curl** adresář na výchozí kontejner úložiště používané clusteru HDInsight.
   
    Na portálu Azure můžete použít pro přístup k výstupu stderr a stdout objekty BLOB.  Microsoft SQL Server Management Studio můžete použít také ke kontrole data, která se nahraje log4jlogs tabulky.

## <a name="limitations"></a>Omezení
* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor umožňuje exportovat data do systému Microsoft SQL Server nebo Azure SQL Database v současné době nepodporuje hromadné vložení.
* Dávkování - s HDInsight se systémem Linux, při použití `-batch` přepnout při vložení, Sqoop provede několik vloží místo dávkování operace insert.

## <a name="summary"></a>Souhrn
Jak je ukázáno v tomto dokumentu, můžete spouštět, monitorovat a zobrazit výsledky Sqoop úloh na clusteru HDInsight nezpracovaná požadavek HTTP.

Další informace o rozhraní REST používané v tomto článku najdete v tématu <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API průvodce</a>.

## <a name="next-steps"></a>Další postup
Obecné informace o Hive s HDInsight:

* [Použití nástroje Sqoop se systémem Hadoop v HDInsight](hdinsight-use-sqoop.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pro ostatní HDInsight curl souvisejících článcích:
 
* [Vytváření clusterů systému Hadoop pomocí REST API služby Azure](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Spouštění dotazů Hive se systémem Hadoop v HDInsight pomocí REST](apache-hadoop-use-hive-curl.md)
* [Spuštění úloh MapReduce s Hadoop v HDInsight pomocí REST](apache-hadoop-use-mapreduce-curl.md)
* [Spuštění úlohy Pig s Hadoop v HDInsight pomocí cURL](apache-hadoop-use-pig-curl.md)



