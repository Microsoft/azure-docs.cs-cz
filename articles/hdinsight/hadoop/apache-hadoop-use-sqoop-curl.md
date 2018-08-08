---
title: Použití nástroje Hadoop Sqoop se Curl v HDInsight – Azure
description: Zjistěte, jak vzdálené odeslání úloh Sqoop k HDInsight pomocí příkazu Curl.
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jasonh
ms.openlocfilehash: 2c6376772aedbe097d737d97c673447adb12bed3
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39598982"
---
# <a name="run-sqoop-jobs-with-hadoop-in-hdinsight-with-curl"></a>Spouštění úloh Sqoop se systémem Hadoop v HDInsight pomocí Curl
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Zjistěte, jak používáme nástroj Curl k spouštění Sqoop úloh na clusteru Hadoop v HDInsight.

Curl slouží k předvedení toho, jak můžete pracovat s HDInsight pomocí nezpracované požadavky HTTP na spuštění, monitorování a načtení výsledků úlohy Sqoop. Tento postup funguje s použitím rozhraní WebHCat REST API (dříve známé jako Templeton) k dispozici ve vašem clusteru HDInsight.

## <a name="prerequisites"></a>Požadavky
K dokončení kroků v tomto článku, budete potřebovat následující:

* Kompletní [pomocí Sqoop se systémem Hadoop v HDInsight](hdinsight-use-sqoop.md#create-cluster-and-sql-database) ke konfiguraci prostředí s clusterem HDInsight a Azure SQL database.
* [Curl](http://curl.haxx.se/). Curl je nástroj pro přenos dat z nebo do clusteru HDInsight.
* [jq](http://stedolan.github.io/jq/). Nástroj jq se používá ke zpracování JSON data vrácená z požadavky REST.

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
     
     Počáteční adresa URL, **https://CLUSTERNAME.azurehdinsight.net/templeton/v1**, je stejný pro všechny požadavky. Cesta, **/status**, signalizuje požadavek vrátit stav WebHCat (také známé jako Templeton) pro server. 
2. Použijte následující postup k odeslání sqoop úlohy:

    ```bash
    curl -u USERNAME:PASSWORD -d user.name=USERNAME -d command="export --connect jdbc:sqlserver://SQLDATABASESERVERNAME.database.windows.net;user=USERNAME@SQLDATABASESERVERNAME;password=PASSWORD;database=SQLDATABASENAME --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://CLUSTERNAME.azurehdinsight.net/templeton/v1/sqoop
    ```

    Parametry použité v tomto příkazu jsou následující:

    * **-d** – od `-G` se nepoužívá výchozí nastavení požadavku pro metodu POST. `-d` Určuje hodnoty dat, které se odesílají s požadavkem.

        * **User.Name** – uživatel, který spouští příkaz.

        * **příkaz** – The Sqoop příkazu ke spuštění.

        * **statusdir** – adresář, který stavu pro tuto úlohu se zapíšou do.

    Tento příkaz vrátí ID úlohy, který slouží ke kontrole stavu úlohy.

        ```json
        {"id":"job_1415651640909_0026"}
        ```

3. Pokud chcete zkontrolovat stav úlohy, použijte následující příkaz. Nahraďte **JOBID** pomocí hodnoty vrácené v předchozím kroku. Například, pokud se návratová hodnota `{"id":"job_1415651640909_0026"}`, pak **JOBID** by `job_1415651640909_0026`.

    ```bash
    curl -G -u USERNAME:PASSWORD -d user.name=USERNAME https://CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/JOBID | jq .status.state
    ```

    Pokud úloha dokončí, bude mít stav **SUCCEEDED**.
   
   > [!NOTE]
   > Tento požadavek Curl vrátí dokument JavaScript Object Notation (JSON) se informace o úloze; jq slouží k načtení jenom hodnoty stavu.
   > 
   > 
4. Jakmile se stav úlohy se změnila na **SUCCEEDED**, můžete načíst výsledky úlohy z úložiště objektů Blob v Azure. `statusdir` Parametr předaný s dotazem obsahuje umístění výstupního souboru; v takovém případě **wasb: / / / Příklad/data/sqoop/curl**. Tuto adresu se ukládá výstup úlohy **příklad/data/sqoop/curl** adresář na výchozí kontejner úložiště používá HDInsight cluster.
   
    Na webu Azure portal můžete použít pro přístup k objektům BLOB stderr a stdout.  Microsoft SQL Server Management Studio můžete také použít ke kontrole dat, který se nahraje do tabulky log4jlogs.

## <a name="limitations"></a>Omezení
* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor používaný k exportu dat Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje operace hromadného vložení.
* Dávkování – s Linuxovým systémem HDInsight při použití `-batch` přepnout při provádění operace vložení, Sqoop provede několik vloží místo dávkování operace vložení.

## <a name="summary"></a>Souhrn
Jak je ukázáno v tomto dokumentu, můžete spouštět, monitorovat a zobrazit výsledky Sqoop úloh ve vašem clusteru HDInsight nezpracovaná požadavku HTTP.

Další informace o rozhraní REST použité v tomto článku najdete v článku <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">pokyny k rozhraní API REST Sqoop</a>.

## <a name="next-steps"></a>Další postup
Obecné informace o Hive s HDInsight:

* [Pomocí Sqoop se systémem Hadoop v HDInsight](hdinsight-use-sqoop.md)

Další informace o dalších způsobech můžete pracovat s Hadoop v HDInsight:

* [Použití Hivu s Hadoopem v HDInsight](hdinsight-use-hive.md)
* [Použití Pigu se systémem Hadoop v HDInsight](hdinsight-use-pig.md)
* [Použití MapReduce se systémem Hadoop v HDInsight](hdinsight-use-mapreduce.md)

Pro další HDInsight články zahrnující curl:
 
* [Vytvoření clusterů Hadoop pomocí rozhraní Azure REST API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Spouštění dotazů Hive se systémem Hadoop v HDInsight pomocí rozhraní REST](apache-hadoop-use-hive-curl.md)
* [Spuštění úloh MapReduce s Hadoop v HDInsight pomocí rozhraní REST](apache-hadoop-use-mapreduce-curl.md)
* [Spuštění úlohy Pig s Hadoop v HDInsight pomocí cURL](apache-hadoop-use-pig-curl.md)



