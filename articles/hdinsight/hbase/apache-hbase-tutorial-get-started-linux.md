---
title: Kurz – použití Apache HBase v Azure HDInsight
description: Postupujte podle tohoto kurzu Apache HBase a začněte používat hadoop na HDInsight. Vytvářejte tabulky z prostředí HBase a dotazujte je pomocí Hive.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/14/2020
ms.openlocfilehash: a601d54ebda074a25a988ac2a115f6418dd5c7ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390262"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Kurz: Použití Apache HBase v Azure HDInsight

Tento kurz ukazuje, jak vytvořit cluster Apache HBase v Azure HDInsight, vytvořit tabulky HBase a tabulky dotazů pomocí Apache Hive.  Obecné informace o HBase najdete v tématu [Přehled HBase ve službě HDInsight](./apache-hbase-overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření clusteru Apache HBase
> * Vytvoření tabulek HBase a vložení dat
> * Použití Apache Hive k dotazování Apache HBase
> * Použití rozhraní REST API HBase pomocí Curl
> * Kontrola stavu clusteru

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Příklady v tomto článku použít prostředí Bash na Windows 10 pro příkazy curl. Postup instalace najdete v [příručce k instalaci podsystému Windows pro Linux pro Windows 10.](https://docs.microsoft.com/windows/wsl/install-win10)  Ostatní [unixové granáty](https://www.gnu.org/software/bash/) budou fungovat také.  Příklady curl, s některými drobnými úpravami, mohou fungovat na příkazovém řádku systému Windows.  Nebo můžete použít rutinu prostředí Windows PowerShell [Invoke-RestMethod](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Vytvoření clusteru Apache HBase

Následující postup používá šablonu Azure Resource Manager k vytvoření clusteru HBase. Šablona také vytvoří závislý výchozí účet Azure Storage. Pro lepší pochopení parametrů použitých v postupu a dalších metod vytvoření clusteru si projděte téma [Vytvoření Hadoop clusterů se systémem Linux v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Kliknutím na následující obrázek otevřete šablonu na webu Azure Portal. Šablona se nachází v [šablonách azure quickstart](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. V dialogovém okně **Vlastní nasazení** zadejte následující hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|Vyberte předplatné Azure, které se používá k vytvoření clusteru.|
    |Skupina prostředků|Vytvořte skupinu pro správu prostředků Azure nebo použijte existující.|
    |Umístění|Zadejte umístění skupiny prostředků. |
    |Název clusteru|Zadejte název clusteru HBase.|
    |Přihlašovací jméno a heslo clusteru|Výchozí přihlašovací jméno je **admin**.|
    |Uživatelské jméno a heslo SSH|Výchozí uživatelské jméno je **sshuser**.|

    Další parametry jsou volitelné.  

    Každý cluster obsahuje závislost účtu Azure Storage. Po odstranění clusteru zůstanou data v účtu úložiště. Výchozí název účtu úložiště clusteru je název clusteru s připojenou příponou „úložiště“. Je pevně zakódován v části proměnné šablony.

3. Vyberte **Možnost Souhlasím s výše uvedenými podmínkami**a pak vyberte možnost **Nákup**. Vytvoření clusteru trvá přibližně 20 minut.

Po odstranění clusteru služby HBase můžete vytvořit jiný cluster HBase pomocí stejného výchozího kontejneru blob. Nový cluster převezme tabulky HBase, které jste vytvořili v původním clusteru. Aby se zabránilo nekonzistencím, doporučujeme zakázat tabulky HBase před odstraněním clusteru.

## <a name="create-tables-and-insert-data"></a>Vytváření tabulek a vkládání dat

Pomocí SSH se můžete připojit ke clusterům HBase a potom pomocí [prostředí Apache HBase](https://hbase.apache.org/0.94/book/shell.html) vytvořit tabulky HBase, vložit data a data dotazů.

Pro většinu osob se data zobrazí v tabulkovém formátu:

![Tabulková data HDInsight Apache HBase](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

V HBase (implementace [Cloud BigTable](https://cloud.google.com/bigtable/)) vypadají stejná data takto:

![HDInsight Apache HBase BigTable data](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Použití prostředí HBase**

1. Pomocí `ssh` příkazu se můžete připojit ke clusteru HBase. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pomocí `hbase shell` příkazu spusťte interaktivní prostředí HBase. V připojení SSH zadejte následující příkaz:

    ```bash
    hbase shell
    ```

1. Příkaz `create` slouží k vytvoření tabulky HBase s rodinami se dvěma sloupci. Názvy tabulek a sloupců rozlišují malá a velká písmena. Zadejte následující příkaz:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. Příkaz `list` slouží k zobrazení seznamu všech tabulek v HBase. Zadejte následující příkaz:

    ```hbase
    list
    ```

1. Příkaz `put` slouží k vložení hodnot do zadaného sloupce v zadaném řádku v určité tabulce. Zadejte následující příkazy:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Pomocí `scan` příkazu můžete skenovat a vracet data `Contacts` tabulky. Zadejte následující příkaz:

    ```hbase
    scan 'Contacts'
    ```

    ![HDInsight Apache Hadoop HBase shell](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. Pomocí `get` příkazu načíst obsah řádku. Zadejte následující příkaz:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Zobrazí se podobné výsledky `scan` jako pomocí příkazu, protože existuje pouze jeden řádek.

    Další informace o schématu tabulky HBase naleznete v [tématu Úvod do návrhu schématu Apache HBase](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Další příkazy HBase najdete v tématu [Referenční příručka Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Pomocí `exit` příkazu zastavte interaktivní prostředí HBase. Zadejte následující příkaz:

    ```hbaseshell
    exit
    ```

**Hromadné načítání dat do tabulky kontaktů HBase**

HBase obsahuje několik metod načítání dat do tabulek.  Další informace naleznete v tématu [Hromadné načítání](https://hbase.apache.org/book.html#arch.bulk.load).

Ukázkový datový soubor najdete ve veřejném kontejneru objektů blob: `wasb://hbasecontacts\@hditutorialdata.blob.core.windows.net/contacts.txt`.  Obsah datového souboru je:

    8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.
    16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz
    4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta
    16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.
    3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive
    3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle
    10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street
    4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street
    4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.
    16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive

Volitelně můžete vytvořit textový soubor a nahrát ho do vlastního účtu úložiště. Pokyny najdete v [tématu Nahrávání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

Tento postup `Contacts` používá tabulku HBase, kterou jste vytvořili v posledním postupu.

1. Z otevřeného připojení ssh spusťte následující příkaz, který transformuje datový `Dimporttsv.bulk.output`soubor na Soubory StoreFiles a uchovávejte na relativní cestě určené společností .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Spusťte následující příkaz pro `/example/data/storeDataFileOutput` nahrání dat do tabulky HBase:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Můžete otevřít prostředí HBase a `scan` pomocí příkazu zobrazit obsah tabulky.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Použití Apache Hive k dotazování Apache HBase

Data v tabulkách HBase můžete dotazovat pomocí [Apache Hive](https://hive.apache.org/). V této části vytvoříte tabulku Hive, která se namapuje na tabulku HBase, a použijete ji k dotazování dat v tabulce HBase.

1. Z otevřeného připojení ssh spusťte beeline pomocí následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Další informace o Beeline najdete v tématu [Použití Hivu s Hadoopem ve službě HDInsight s Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Spusťte následující skript [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) a vytvořte tabulku Hive, která se mapuje na tabulku HBase. Ujistěte se, že jste vytvořili ukázkovou tabulku odkazuje dříve v tomto článku pomocí prostředí HBase před spuštěním tohoto příkazu.

    ```hiveql
    CREATE EXTERNAL TABLE hbasecontacts(rowkey STRING, name STRING, homephone STRING, officephone STRING, officeaddress STRING)
    STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
    WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,Personal:Name,Personal:Phone,Office:Phone,Office:Address')
    TBLPROPERTIES ('hbase.table.name' = 'Contacts');
    ```

1. Spusťte následující skript HiveQL pro dotaz na data v tabulce HBase:

    ```hiveql
    SELECT count(rowkey) AS rk_count FROM hbasecontacts;
    ```

1. Chcete-li ukončit `!exit`beeline, použijte .

1. Chcete-li ukončit připojení `exit`ssh, použijte .

## <a name="use-hbase-rest-apis-using-curl"></a>Použití rozhraní REST API HBase pomocí Curl

Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Požadavky byste vždy měli provádět pomocí protokolu HTTPS (Secure HTTP), čímž pomůžete zajistit, že se přihlašovací údaje budou na server odesílat bezpečně.

1. Nastavte proměnnou prostředí pro snadné použití. Upravte níže uvedené příkazy nahrazením `MYPASSWORD` přihlašovacím heslem clusteru. Nahraďte `MYCLUSTERNAME` název clusteru HBase. Pak zadejte příkazy.

    ```bash
    export password='MYPASSWORD'
    export clustername=MYCLUSTERNAME
    ```

1. Pomocí následujícího příkazu můžete zobrazit seznam existujících tabulek HBase:

    ```bash
    curl -u admin:$password \
    -G https://$clustername.azurehdinsight.net/hbaserest/
    ```

1. Pokud chcete vytvořit novou tabulku HBase se dvěma skupinami sloupců, použijte následující příkaz:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/schema" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"@name\":\"Contact1\",\"ColumnSchema\":[{\"name\":\"Personal\"},{\"name\":\"Office\"}]}" \
    -v
    ```

    Schéma je k dispozici ve formátu JSon.
1. Chcete-li vložit nějaká data použijte následující příkaz:

    ```bash
    curl -u admin:$password \
    -X PUT "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/false-row-key" \
    -H "Accept: application/json" \
    -H "Content-Type: application/json" \
    -d "{\"Row\":[{\"key\":\"MTAwMA==\",\"Cell\": [{\"column\":\"UGVyc29uYWw6TmFtZQ==\", \"$\":\"Sm9obiBEb2xl\"}]}]}" \
    -v
    ```

    Base64 kóduje hodnoty zadané v přepínači -d. V tomto příkladu:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ==: Osobní: Jméno
   * Sm9obiBEb2xl: John Dole

     [false-row-key](https://hbase.apache.org/apidocs/org/apache/hadoop/hbase/rest/package-summary.html#operation_cell_store_single) umožňuje vložit více (dávkových) hodnot.

1. Pro získání řádku použijte následující příkaz:

    ```bash
    curl -u admin:$password \
    GET "https://$clustername.azurehdinsight.net/hbaserest/Contacts1/1000" \
    -H "Accept: application/json" \
    -v
    ```

Další informace o HBase Rest naleznete v tématu [Referenční příručka Apache HBase](https://hbase.apache.org/book.html#_rest).

> [!NOTE]  
> Thrift není podporovaný HBase v HDInsight.
>
> Pokud používáte Curl nebo jinou komunikaci REST s WebHCat, je třeba ověřit žádosti zadáním uživatelského jména a hesla pro správce clusteru HDInsight. Název clusteru také musíte použít jako součást identifikátoru URI (Uniform Resource Identifier) sloužícího k odesílání požadavků na server:
> 
>   
>        curl -u <UserName>:<Password> \
>        -G https://<ClusterName>.azurehdinsight.net/templeton/v1/status
>   
>    Měla by se zobrazit odpověď podobná následující odpovědi:
>   
>        {"status":"ok","version":"v1"}

## <a name="check-cluster-status"></a>Kontrola stavu clusteru

HBase v HDInsight se dodává s webovým uživatelským rozhraním pro sledování clusterů. Pomocí webového uživatelského rozhraní, můžete žádat o statistické údaje nebo informace o oblastech.

**Přístup k hlavnímu uživatelskému rozhraní HBase**

1. Přihlaste se k webovému `https://CLUSTERNAME.azurehdinsight.net` uživatelskému uživatelskému uživatelskému panelu Ambari, kde `CLUSTERNAME` je název clusteru HBase.

1. V levé nabídce vyberte **HBase.**

1. V horní části stránky vyberte **Rychlé odkazy,** přejděte na aktivní odkaz uzlu Zookeeper a pak vyberte **hlavní uhlavní ui HBase**.  Uživatelské rozhraní se otevře na nové kartě prohlížeče:

   ![HDInsight Apache HBase HMaster UI](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Hlavní uživatelské rozhraní HBase obsahuje tyto části:

   - oblastní servery
   - zálohování hlavních serverů
   - tabulky
   - úlohy
   - atributy softwaru

## <a name="clean-up-resources"></a>Vyčištění prostředků

Aby se zabránilo nekonzistencím, doporučujeme zakázat tabulky HBase před odstraněním clusteru. Můžete použít příkaz `disable 'Contacts'`HBase . Pokud nebudete nadále používat tuto aplikaci, odstraňte cluster HBase, který jste vytvořili pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
1. Do pole **Hledat** v horní části zadejte **HDInsight**.
1. V části **Služby** **vyberte clustery HDInsight** .
1. V seznamu clusterů HDInsight, který se zobrazí, klikněte na **...** vedle clusteru, který jste vytvořili pro tento kurz.
1. Klepněte na **tlačítko Odstranit**. Klepněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit cluster Apache HBase. A jak vytvořit tabulky a zobrazit data v těchto tabulkách z prostředí HBase. Také jste se naučili, jak používat dotaz Hive na data v tabulkách HBase. A jak použít HBase C# REST API k vytvoření tabulky HBase a načtení dat z tabulky. Další informace naleznete v tématu:

> [!div class="nextstepaction"]
> [Přehled hdinsight hbážíb](./apache-hbase-overview.md)