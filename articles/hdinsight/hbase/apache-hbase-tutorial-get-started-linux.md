---
title: Kurz – použití Apache HBA v Azure HDInsight
description: Postupujte podle tohoto kurzu Apache HBA a začněte používat Hadoop ve službě HDInsight. Vytvářejte tabulky z prostředí HBase a dotazujte je pomocí Hive.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/22/2021
ms.openlocfilehash: 5de98f5bf57626a408dd5bec8575856074f434c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101705666"
---
# <a name="tutorial-use-apache-hbase-in-azure-hdinsight"></a>Kurz: použití Apache HBA v Azure HDInsight

V tomto kurzu se dozvíte, jak vytvořit cluster Apache HBA v Azure HDInsight, vytvořit tabulky HBA a dotazovat tabulky pomocí Apache Hive.  Obecné informace o HBase najdete v tématu [Přehled HBase ve službě HDInsight](./apache-hbase-overview.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření clusteru Apache HBA
> * Vytváření tabulek HBA a vkládání dat
> * Použití Apache Hive k dotazování Apache HBA
> * Použití rozhraní REST API HBase pomocí Curl
> * Kontrola stavu clusteru

## <a name="prerequisites"></a>Předpoklady

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Bash. Příklady v tomto článku používají pro příkazy oblé prostředí bash ve Windows 10. Pokyny k instalaci najdete v tématu [Instalační příručka k systému Windows pro Linux pro systém Windows 10](/windows/wsl/install-win10) .  Budou fungovat i další [prostředí UNIX](https://www.gnu.org/software/bash/) .  Příklady složených s některými drobnými úpravami mohou pracovat na příkazovém řádku systému Windows.  Nebo můžete použít rutinu Windows PowerShellu [Invoke-RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod).

## <a name="create-apache-hbase-cluster"></a>Vytvoření clusteru Apache HBA

Následující postup používá šablonu Azure Resource Manager k vytvoření clusteru HBA. Šablona také vytvoří závislý výchozí účet Azure Storage. Pro lepší pochopení parametrů použitých v postupu a dalších metod vytvoření clusteru si projděte téma [Vytvoření Hadoop clusterů se systémem Linux v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

1. Výběrem následujícího obrázku otevřete šablonu v Azure Portal. Šablona se nachází v [šablonách rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux%2Fazuredeploy.json" target="_blank"><img src="./media/apache-hbase-tutorial-get-started-linux/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. V dialogovém okně **vlastní nasazení** zadejte následující hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|Vyberte předplatné Azure, které se použije k vytvoření clusteru.|
    |Skupina prostředků|Vytvořte skupinu správy prostředků Azure nebo použijte existující.|
    |Umístění|Zadejte umístění skupiny prostředků. |
    |Název clusteru|Zadejte název clusteru HBA.|
    |Přihlašovací jméno a heslo clusteru|Výchozí přihlašovací jméno je **admin**.|
    |Uživatelské jméno a heslo SSH|Výchozí uživatelské jméno je **sshuser**.|

    Další parametry jsou volitelné.  

    Každý cluster obsahuje závislost účtu Azure Storage. Po odstranění clusteru zůstanou tato data v účtu úložiště. Výchozí název účtu úložiště clusteru je název clusteru s připojenou příponou „úložiště“. Je pevně zakódované v části proměnné šablony.

3. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře** a pak vyberte **koupit**. Vytvoření clusteru trvá přibližně 20 minut.

Po odstranění clusteru služby HBase můžete vytvořit jiný cluster HBase pomocí stejného výchozího kontejneru blob. Nový cluster převezme tabulky HBase, které jste vytvořili v původním clusteru. Aby se zabránilo nekonzistencím, doporučujeme zakázat tabulky HBase před odstraněním clusteru.

## <a name="create-tables-and-insert-data"></a>Vytváření tabulek a vkládání dat

Pomocí SSH se můžete připojit k clusterům HBA a pak pomocí [prostředí Apache HBA](https://hbase.apache.org/0.94/book/shell.html) vytvořit tabulky HBA, vkládat data a dotazovat data.

Pro většinu osob se data zobrazí v tabulkovém formátu:

![Tabulková data HDInsight Apache HBA](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-tabular.png)

V rámci adaptérů HBA (implementace [cloudu BigTable](https://cloud.google.com/bigtable/)) vypadají stejná data jako:

![BigTable data o službě HDInsight Apache HBA](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-contacts-bigtable.png)

**Použití prostředí HBase**

1. Pomocí `ssh` příkazu se připojte ke clusteru HBA. Níže uvedený příkaz upravte nahrazením `CLUSTERNAME` názvem vašeho clusteru a zadáním příkazu:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Pomocí `hbase shell` příkazu spusťte interaktivní prostředí pro adaptéry HBA. Do připojení SSH zadejte následující příkaz:

    ```bash
    hbase shell
    ```

1. Pomocí `create` příkazu vytvořte tabulku HBA se dvěma skupinami sloupců. V názvech tabulek a sloupců se rozlišují velká a malá písmena. Zadejte následující příkaz:

    ```hbaseshell
    create 'Contacts', 'Personal', 'Office'
    ```

1. `list`K vypsání všech tabulek v adaptérech HBA použijte příkaz. Zadejte následující příkaz:

    ```hbase
    list
    ```

1. Použijte `put` příkaz pro vložení hodnot do zadaného sloupce v zadaném řádku v konkrétní tabulce. Zadejte následující příkazy:

    ```hbaseshell
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    ```

1. Pomocí `scan` příkazu naskenujte a vraťte `Contacts` data tabulky. Zadejte následující příkaz:

    ```hbase
    scan 'Contacts'
    ```

    ![Prostředí HDInsight Apache Hadoop HBA](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-shell.png)

1. `get`K načtení obsahu řádku použijte příkaz. Zadejte následující příkaz:

    ```hbaseshell
    get 'Contacts', '1000'
    ```

    Podobné výsledky se zobrazí při použití `scan` příkazu, protože existuje pouze jeden řádek.

    Další informace o schématu tabulky HBA najdete v tématu [Úvod do návrhu schématu Apache HBA](http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf). Další příkazy HBase najdete v tématu [Referenční příručka Apache HBase](https://hbase.apache.org/book.html#quickstart).

1. Pomocí `exit` příkazu zastavte prostředí HBA interaktivní prostředí. Zadejte následující příkaz:

    ```hbaseshell
    exit
    ```

**Hromadné načítání dat do tabulky kontaktů HBase**

HBase obsahuje několik metod načítání dat do tabulek.  Další informace naleznete v tématu [Hromadné načítání](https://hbase.apache.org/book.html#arch.bulk.load).

Ukázkový datový soubor najdete ve veřejném kontejneru objektů blob: `wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt`.  Obsah datového souboru je:

`8396    Calvin Raji      230-555-0191    230-555-0191    5415 San Gabriel Dr.`

`16600   Karen Wu         646-555-0113    230-555-0192    9265 La Paz`

`4324    Karl Xie         508-555-0163    230-555-0193    4912 La Vuelta`

`16891   Jonn Jackson     674-555-0110    230-555-0194    40 Ellis St.`

`3273    Miguel Miller    397-555-0155    230-555-0195    6696 Anchor Drive`

`3588    Osa Agbonile     592-555-0152    230-555-0196    1873 Lion Circle`

`10272   Julia Lee        870-555-0110    230-555-0197    3148 Rose Street`

`4868    Jose Hayes       599-555-0171    230-555-0198    793 Crawford Street`

`4761    Caleb Alexander  670-555-0141    230-555-0199    4775 Kentucky Dr.`

`16443   Terry Chander    998-555-0171    230-555-0200    771 Northridge Drive`

Volitelně můžete vytvořit textový soubor a nahrát ho do vlastního účtu úložiště. Pokyny najdete v tématu [nahrání dat pro úlohy Apache Hadoop v HDInsight](../hdinsight-upload-data.md).

Tento postup používá `Contacts` tabulku HBA, kterou jste vytvořili v posledním postupu.

1. V otevřeném připojení SSH spusťte následující příkaz, který převede datový soubor na StoreFiles a uloží se do relativní cesty určené pomocí `Dimporttsv.bulk.output` .

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name,Personal:Phone,Office:Phone,Office:Address" -Dimporttsv.bulk.output="/example/data/storeDataFileOutput" Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt
    ```

2. Spuštěním následujícího příkazu nahrajte data z `/example/data/storeDataFileOutput` do tabulky HBA:

    ```bash
    hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /example/data/storeDataFileOutput Contacts
    ```

3. Můžete otevřít prostředí HBA a použít `scan` příkaz k vypsání obsahu tabulky.

## <a name="use-apache-hive-to-query-apache-hbase"></a>Použití Apache Hive k dotazování Apache HBA

Pomocí [Apache Hive](https://hive.apache.org/)můžete zadávat dotazy na data v adaptérech HBA. V této části vytvoříte tabulku Hive, která se namapuje na tabulku HBase, a použijete ji k dotazování dat v tabulce HBase.

1. V otevřeném připojení SSH spusťte Beeline pomocí následujícího příkazu:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
    ```

    Další informace o Beeline najdete v tématu [Použití Hivu s Hadoopem ve službě HDInsight s Beeline](../hadoop/apache-hadoop-use-hive-beeline.md).

1. Spuštěním následujícího skriptu [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual) vytvořte tabulku podregistru, která se mapuje na tabulku HBA. Než spustíte tento příkaz, ujistěte se, že jste vytvořili ukázkovou tabulku, kterou jste si poznamenali dříve v tomto článku, pomocí prostředí HBA.

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

1. K ukončení Beeline použijte `!exit` .

1. K ukončení připojení SSH použijte `exit` .

### <a name="separate-hive-and-hbase-clusters"></a>Samostatné clustery podregistru a HBA

Dotaz na podregistr pro přístup k datům HBA nemusíte spouštět z clusteru HBA. Každý cluster, který je součástí podregistru (včetně Sparku, Hadoop, HBA nebo interaktivního dotazu), se dá použít k dotazování na data HBA za předpokladu, že jsou splněné následující kroky:

1. Oba clustery musí být připojeny ke stejnému Virtual Network a podsíti
2. Kopírování `/usr/hdp/$(hdp-select --version)/hbase/conf/hbase-site.xml` z clusteru HBA hlavních do podregistru clusteru hlavních

### <a name="secure-clusters"></a>Zabezpečené clustery

Data HBA je také možné dotazovat z podregistru pomocí adaptérů HBA s povoleným protokolem ESP: 

1. Pokud budete postupovat podle vzoru více clusterů, musí být oba clustery povolené ESP. 
2. Pokud chcete, aby se v podregistru mohly dotazovat data HBA, ujistěte se, že `hive` uživatel má udělená oprávnění pro přístup k datům HBA přes modul plug-in HBA Ranger.
3. Při použití samostatných clusterů s povoleným protokolem ESP se `/etc/hosts` musí obsah z clusteru HBA hlavních připojit k `/etc/hosts` podregistru clusteru hlavních. 
> [!NOTE]
> Po škálování obou clusterů se `/etc/hosts` musí znovu připojit.

## <a name="use-hbase-rest-apis-using-curl"></a>Použití rozhraní REST API HBase pomocí Curl

Rozhraní API REST je zabezpečeno pomocí [základního ověřování](https://en.wikipedia.org/wiki/Basic_access_authentication). Požadavky byste vždy měli provádět pomocí protokolu HTTPS (Secure HTTP), čímž pomůžete zajistit, že se přihlašovací údaje budou na server odesílat bezpečně.

1. Pokud chcete povolit rozhraní REST API v clusteru HDInsight, přidejte do oddílu **akce skriptu** následující vlastní spouštěcí skript. Spouštěcí skript můžete přidat po vytvoření clusteru nebo po jeho vytvoření. V poli **typ uzlu** vyberte **servery oblastí** , aby se zajistilo, že se skript spustí jenom v adaptérech HBA na serverech oblasti.


    ```bash
    #! /bin/bash

    THIS_MACHINE=`hostname`

    if [[ $THIS_MACHINE != wn* ]]
    then
        printf 'Script to be executed only on worker nodes'
        exit 0
    fi

    RESULT=`pgrep -f RESTServer`
    if [[ -z $RESULT ]]
    then
        echo "Applying mitigation; starting REST Server"
        sudo python /usr/lib/python2.7/dist-packages/hdinsight_hbrest/HbaseRestAgent.py
    else
        echo "Rest server already running"
        exit 0
    fi
    ```

1. Nastavte proměnnou prostředí pro snadné použití. Níže uvedené příkazy upravte tak, že nahradíte `MYPASSWORD` heslo pro přihlášení ke clusteru. Nahraďte `MYCLUSTERNAME` názvem vašeho clusteru HBA. Pak zadejte příkazy.

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

    Kódování Base64 hodnoty zadané v přepínači-d V tomto příkladu:

   * MTAwMA==: 1000
   * UGVyc29uYWw6TmFtZQ = =: osobní: název
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
> `curl -u <UserName>:<Password> \`
>
> `-G https://<ClusterName>.azurehdinsight.net/templeton/v1/status`
>
> Měla by se zobrazit odpověď podobná následující odpovědi:
>
> `{"status":"ok","version":"v1"}`

## <a name="check-cluster-status"></a>Kontrola stavu clusteru

HBase v HDInsight se dodává s webovým uživatelským rozhraním pro sledování clusterů. Pomocí webového uživatelského rozhraní, můžete žádat o statistické údaje nebo informace o oblastech.

**Přístup k hlavnímu uživatelskému rozhraní HBase**

1. Přihlaste se k webovému uživatelskému rozhraní Ambari, `https://CLUSTERNAME.azurehdinsight.net` kde `CLUSTERNAME` je název vašeho clusteru HBA.

1. V nabídce vlevo vyberte **HBA** .

1. V horní části stránky vyberte **Rychlé odkazy** , přejděte na aktivní odkaz na uzel Zookeeper a pak vyberte **HBase Master uživatelské rozhraní**.  Uživatelské rozhraní se otevře na nové kartě prohlížeče:

   ![HMaster uživatelské rozhraní HDInsight Apache HBA](./media/apache-hbase-tutorial-get-started-linux/hdinsight-hbase-hmaster-ui.png)

   Hlavní uživatelské rozhraní HBase obsahuje tyto části:

   - oblastní servery
   - zálohování hlavních serverů
   - V tabulkách
   - úlohy
   - atributy softwaru

## <a name="cluster-recreation"></a>Rekreace clusteru

Po odstranění clusteru služby HBase můžete vytvořit jiný cluster HBase pomocí stejného výchozího kontejneru blob. Nový cluster převezme tabulky HBase, které jste vytvořili v původním clusteru. Aby ale nedocházelo k nekonzistencím, doporučujeme před odstraněním clusteru zakázat tabulky HBA. 

Můžete použít příkaz HBA `disable 'Contacts'` . 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte cluster HBA, který jste vytvořili, pomocí následujícího postupu:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Do **vyhledávacího** pole v horní části zadejte **HDInsight**.
1. V části **služby** vyberte **clustery HDInsight** .
1. V seznamu clusterů HDInsight, které se zobrazí, klikněte na **...** vedle clusteru, který jste vytvořili pro účely tohoto kurzu.
1. Klikněte na **Odstranit**. Klikněte na **Ano**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit cluster Apache HBA. A vytváření tabulek a zobrazení dat v těchto tabulkách z prostředí HBA. Zjistili jste také, jak použít dotaz na podregistr na data v rámci tabulek HBA. A použití rozhraní REST API pro adaptéry C# k vytvoření tabulky HBA a načtení dat z tabulky. Další informace najdete v následujících tématech:

> [!div class="nextstepaction"]
> [Přehled HBA v HDInsight](./apache-hbase-overview.md)
