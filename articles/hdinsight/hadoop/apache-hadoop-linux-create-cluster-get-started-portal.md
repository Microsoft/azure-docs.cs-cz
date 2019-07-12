---
title: 'Rychlý start: Začínáme s Apache Hadoop a Apache Hive pomocí webu Azure portal – Azure HDInsight'
description: V tomto rychlém startu použijete Azure portal k vytvoření clusteru HDInsight Hadoop
keywords: začínáme používat hadoop, hadoop linux, hadoop rychlý start, hive začínáme, hive rychlý start
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: hrasheed
ms.openlocfilehash: f92cb247afb25562a96373c28534549a2f16d8c9
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805593"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý start: Vytvořit cluster Apache Hadoop v Azure HDInsight pomocí webu Azure portal

V tomto článku se dozvíte, jak vytvořit [Apache Hadoop](https://hadoop.apache.org/) clusterů v HDInsight pomocí webu Azure portal a potom spouštět úlohy Apache Hive v HDInsight. Většina úloh Hadoop jsou dávkové úlohy. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V tomto článku provedete všechny tři úlohy.

V tomto rychlém startu pomocí webu Azure Portal vytvoříte cluster HDInsight Hadoop. K vytvoření clusteru můžete použít také [šablonu Azure Resource Manageru](apache-hadoop-linux-tutorial-get-started.md).

Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](./apache-hadoop-introduction.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných součásti v HDInsight najdete v tématu [co je nového ve verzích clusterů systému Apache Hadoop poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)  

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvořit cluster Apache Hadoop

V této části vytvoříte cluster Hadoop v HDInsight pomocí webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Na webu Azure Portal, přejděte na **vytvořit prostředek** > **Analytics** > **HDInsight**.

    ![Databricks na webu Azure Portal](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight.png "Databricks na webu Azure Portal")

1. V části **HDInsight** > **rychlé vytvoření** > **Základy**, zadejte nebo vyberte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Název clusteru   | Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může mít až 59 znaků včetně písmena, číslice a pomlčky. První a poslední znak názvu nemůže být pomlčka. |
    |Subscription    |  Vyberte své předplatné Azure. |
    |Typ clusteru     | Tohle zatím přeskočte. Tuto hodnotu zadáte v dalším kroku postupu.|
    |Uživatelské jméno přihlášení clusteru a heslo    | Výchozí přihlašovací jméno je **admin** (správce). Heslo musí mít minimálně 10 znaků a musí obsahovat alespoň jedno číslo, jedno velké písmeno, jedno malé písmeno a jeden jiný než alfanumerický znak (kromě znaků ' " ` \). **Nezadávejte** běžné heslo, jako je „Pass@word1“.|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Heslo přihlášení clusteru pomocí SSH| Pokud toto políčko zaškrtnete, použije se stejné heslo pro uživatele SSH, jaké jste zadali pro přihlášení uživatele clusteru.|
    |Resource group     | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |Location    | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |

    ![HDInsight Linux začínáme zadání základních hodnot clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-basics.png "Zadání základních hodnot pro vytvoření clusteru HDInsight")

1. Vyberte **typ clusteru** otevřít **konfigurace clusteru** stránce a poté zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ clusteru     | Vyberte **Hadoop** |
    |Version     | Vyberte **Hadoop 2.7.3 (HDI 3.6)**|

    ![HDInsight Linux začínáme zadání základních hodnot clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-configuration-hadoop.png "Zadání základních hodnot pro vytvoření clusteru HDInsight")

    Vyberte **vyberte** a pak vyberte **Další** pro přechod na nastavení úložiště.

1. Z **úložiště** kartu, zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště    | Pro účely tohoto článku vyberte úložiště Azure pro použití jako výchozí účet úložiště Azure Storage Blob. Jako výchozí úložiště můžete vybrat také úložiště Azure Data Lake. |
    |Metoda výběru     |  Pro účely tohoto článku vyberte **Moje předplatné**, aby se použil účet úložiště z vašeho předplatného Azure. Pokud chcete používat účet úložiště z jiných předplatných, vyberte **Přístupový klíč** a pak zadejte přístupový klíč k účtu. |
    |Vyberte účet úložiště.   | Vyberte **vyberte účet úložiště** vyberte existující účet úložiště, nebo vyberte **vytvořit nový**. Pokud vytvoříte nový účet, název musí být dlouhý 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.|

    Přijměte všechny výchozí hodnoty a pak vyberte **Další** k přechodu na stránce souhrnu.

    ![HDInsight Linux začínáme zadání hodnot úložiště clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-storage.png "Zadání hodnot úložiště pro vytvoření clusteru HDInsight")

1. Z **Souhrn** kartu, ověřte hodnoty, které jste vybrali v předchozích krocích.

    ![HDInsight Linux začínáme souhrn clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-summary.png "Začínáme s HDInsight Linux a souhrn clusteru")

1. Vyberte **Vytvořit**. Vytvoření clusteru trvá přibližně 20 minut.

1. Po vytvoření clusteru se zobrazí stránka přehledu clusteru na webu Azure Portal.

    ![Počáteční nastavení clusteru HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-overview.png "Vlastnosti clusteru HDInsight")    

    Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. HDInsight cluster a jeho výchozí účet úložiště, musí být umístěny společně ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

    > [!NOTE]  
    > Další metody vytváření clusterů a principy vlastnosti používaných v rámci tohoto rychlého startu, najdete v tématu [clusterů HDInsight vytvořit](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Spustit dotazy Apache Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější součástí používanou v HDInsight. Existuje mnoho způsobů spouštění úloh Hive v HDInsight. V tomto rychlém startu použijete zobrazení Ambari Hive z portálu. Další metody pro odesílání úloh Hive najdete v části [Použití Hive v HDInsight](hdinsight-use-hive.md).

1. Pokud chcete otevřít Ambari, vyberte **Řídicí panel clusteru**, jak je znázorněno na předchozím snímku obrazovky.  Můžete také přejít na `https://ClusterName.azurehdinsight.net`, kde `ClusterName` je cluster, který jste vytvořili v předchozí části.

    ![Počáteční řídicí panel clusteru HDInsight Linux](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-get-started-open-cluster-dashboard.png "Počáteční řídicí panel clusteru HDInsight Linux")

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno **admin**.

3. Otevřete **Zobrazení Hive**, jak je znázorněno na následujícím snímku obrazovky:

    ![Výběr zobrazení Ambari](./media/apache-hadoop-linux-tutorial-get-started/selecthiveview.png "Nabídka zobrazení Hive služby HDInsight")

4. Na kartě **DOTAZ** vložte následující příkazy HiveQL do pracovního listu:

    ```sql
    SHOW TABLES;
    ```

    ![Zobrazení Hive služby HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview-1.png "Editor dotazů zobrazení Hive služby HDInsight")

5. Vyberte **Provést**. Karta **VÝSLEDKY** se zobrazí pod kartou **DOTAZ** a zobrazí informace o úloze. 

    Po dokončení dotazu **dotazu** karta zobrazuje výsledky operace. Zobrazí jedna tabulka s názvem **hivesampletable**. Tato vzorová tabulka Hive obsahuje všechny clustery HDInsight.

    ![Zobrazení Hive služby HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hiveview.png "Editor dotazů zobrazení Hive služby HDInsight")

6. Opakujte kroky 4 a 5 a spusťte následující dotaz:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Výsledky dotazu můžete také uložit. Vyberte tlačítko s nabídkou na pravé straně a určete, jestli chcete stáhnout výsledky jako soubor CSV nebo je uložit do účtu úložiště přidruženého ke clusteru.

    ![Uložení výsledku dotazu Hive](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-linux-hive-view-save-results.png "Uložení výsledku dotazu Hive")

Po dokončení úlohy Hive můžete [Exportovat výsledky do databáze Azure SQL nebo databáze systému SQL Server](apache-hadoop-use-sqoop-mac-linux.md), můžete také [zobrazit výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o používání Hive v HDInsight najdete v tématu [použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení tohoto rychlého startu, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

> [!NOTE]  
> Pokud jste *okamžitě* přistoupíte k dalším článku se dozvíte, jak k provádění operací ETL pomocí systému Hadoop v HDInsight, možná budete chtít zachovat clusteru se systémem. Toto je vzhledem k tomu, že v tomto kurzu, budete muset znovu vytvořit Hadoop cluster. Pokud nebudete prostřednictvím dalším článku hned, je však musí teď odstranit clusteru.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Postup odstranění clusteru a/nebo výchozího účtu úložiště

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Odstranění clusteru HDInsight](./media/apache-hadoop-linux-tutorial-get-started/hdinsight-delete-cluster.png "Odstranění clusteru HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Resource Manageru a jak provádět základní dotazy Hive. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načítání dat pomocí na HDInsight Interactive Query](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
