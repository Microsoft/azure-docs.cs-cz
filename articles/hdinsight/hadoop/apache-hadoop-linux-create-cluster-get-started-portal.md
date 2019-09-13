---
title: 'Rychlý start: Apache Hadoop a Apache Hive v HDInsight pomocí Azure Portal'
description: V tomto rychlém startu použijete Azure Portal k vytvoření clusteru HDInsight Hadoop.
keywords: začínáme používat hadoop, hadoop linux, hadoop rychlý start, hive začínáme, hive rychlý start
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 07/02/2019
ms.author: hrasheed
ms.openlocfilehash: be7ff67f07ed8eaeb3f04a15c6185191bf107cf2
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70918399"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí Azure Portal

V tomto článku se naučíte, jak vytvářet clustery [Apache Hadoop](https://hadoop.apache.org/) v hdinsight pomocí Azure Portal a pak spouštět Apache Hive úlohy v HDInsight. Většina úloh Hadoop jsou dávkové úlohy. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V tomto článku provedete všechny tři úlohy.

V tomto rychlém startu pomocí webu Azure Portal vytvoříte cluster HDInsight Hadoop. K vytvoření clusteru můžete použít také [šablonu Azure Resource Manageru](apache-hadoop-linux-tutorial-get-started.md).

Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných komponent ve službě HDInsight najdete v tématu [co je nového v Apache Hadoop verzích clusterů poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)  

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

V této části vytvoříte cluster Hadoop v HDInsight pomocí webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Z Azure Portal můžete přejít k části **Vytvoření zdroje** > **analýzy** > prostředků**HDInsight**.

    ![Databricks na webu Azure Portal](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Databricks na webu Azure Portal")

1. V > části**rychlé vytváření** > **základů**HDInsight zadejte nebo vyberte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Název clusteru   | Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může obsahovat až 59 znaků včetně písmen, číslic a spojovníků. První a poslední znak názvu nemůže být pomlčka. |
    |Subscription    |  Vyberte své předplatné Azure. |
    |Typ clusteru     | Tohle zatím přeskočte. Tuto hodnotu zadáte v dalším kroku postupu.|
    |Uživatelské jméno a heslo přihlášení clusteru    | Výchozí přihlašovací jméno je **admin** (správce). Heslo musí mít minimálně 10 znaků a musí obsahovat alespoň jedno číslo, jedno velké písmeno, jedno malé písmeno a jeden jiný než alfanumerický znak (kromě znaků ' " ` \). **Nezadávejte** běžné heslo, jako je „Pass@word1“.|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použít heslo přihlášení clusteru pro SSH| Toto políčko zaškrtněte, pokud chcete pro uživatele SSH použít stejné heslo jako ten, který jste zadali pro uživatele přihlášení clusteru.|
    |Resource group     | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |Location    | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |

    ![HDInsight Linux začínáme zadání základních hodnot clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-basics1.png "Zadání základních hodnot pro vytvoření clusteru HDInsight")

1. Vyberte **typ clusteru** . otevře se stránka **Konfigurace clusteru** a pak zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ clusteru     | Vyberte **Hadoop** |
    |Version     | Vyberte **Hadoop 2.7.3 (HDI 3.6)**|

    ![Konfigurace clusteru HDInsight pro Linux – začátek](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-configuration-hadoop.png "Konfigurace clusteru HDInsight pro Linux – začátek")

    Vyberte **Vybrat** a potom vyberte **Další** a přejděte k nastavení úložiště.

1. Na kartě **úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště    | V tomto článku vyberte úložiště Azure, které chcete použít Azure Storage Blob jako výchozí účet úložiště. Jako výchozí úložiště můžete vybrat také úložiště Azure Data Lake. |
    |Metoda výběru     |  Pro účely tohoto článku vyberte **Moje předplatné**, aby se použil účet úložiště z vašeho předplatného Azure. Pokud chcete používat účet úložiště z jiných předplatných, vyberte **Přístupový klíč** a pak zadejte přístupový klíč k účtu. |
    |Vybrat účet úložiště   | Vyberte **možnost vybrat účet úložiště** a vyberte existující účet úložiště, nebo vyberte **vytvořit novou**. Pokud vytvoříte nový účet, název musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.|

    Přijměte všechny ostatní výchozí hodnoty a pak klikněte na tlačítko **Další** a přejděte na stránku Souhrn.

    ![HDInsight Linux začínáme zadání hodnot úložiště clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-storage.png "Zadání hodnot úložiště pro vytvoření clusteru HDInsight")

1. Na kartě **Souhrn** ověřte hodnoty, které jste vybrali v předchozích krocích.

    ![HDInsight Linux začínáme souhrn clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/quick-create-summary.png "Začínáme s HDInsight Linux a souhrn clusteru")

1. Vyberte **Vytvořit**. Vytvoření clusteru trvá přibližně 20 minut.

1. Po vytvoření clusteru se zobrazí stránka přehledu clusteru na webu Azure Portal.

    ![Počáteční nastavení clusteru HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Vlastnosti clusteru HDInsight")    

    Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

    > [!NOTE]  
    > Další metody vytváření clusterů a porozumění vlastnostem používaným v tomto rychlém startu najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Spuštění dotazů Apache Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější součástí používanou v HDInsight. Existuje mnoho způsobů spouštění úloh Hive v HDInsight. V tomto rychlém startu použijete zobrazení podregistru Ambari z portálu. Další metody pro odesílání úloh Hive najdete v části [Použití Hive v HDInsight](hdinsight-use-hive.md).

1. Pokud chcete otevřít Ambari, vyberte **Řídicí panel clusteru**, jak je znázorněno na předchozím snímku obrazovky.  Můžete také přejít na `https://ClusterName.azurehdinsight.net`, kde `ClusterName` je cluster, který jste vytvořili v předchozí části.

    ![Počáteční řídicí panel clusteru HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Počáteční řídicí panel clusteru HDInsight Linux")

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno **admin**.

3. Otevřete **Zobrazení Hive**, jak je znázorněno na následujícím snímku obrazovky:

    ![Výběr zobrazení Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Nabídka zobrazení Hive služby HDInsight")

4. Na kartě **DOTAZ** vložte následující příkazy HiveQL do pracovního listu:

    ```sql
    SHOW TABLES;
    ```

    ![Zobrazení Hive služby HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Editor dotazů zobrazení Hive služby HDInsight")

5. Vyberte **Provést**. Karta **VÝSLEDKY** se zobrazí pod kartou **DOTAZ** a zobrazí informace o úloze. 

    Po dokončení dotazu se na kartě **dotaz** zobrazí výsledky operace. Zobrazí jedna tabulka s názvem **hivesampletable**. Tato vzorová tabulka Hive obsahuje všechny clustery HDInsight.

    ![Výsledky zobrazení podregistru HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Výsledky zobrazení podregistru HDInsight")

6. Opakujte kroky 4 a 5 a spusťte následující dotaz:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Výsledky dotazu můžete také uložit. Vyberte tlačítko s nabídkou na pravé straně a určete, jestli chcete stáhnout výsledky jako soubor CSV nebo je uložit do účtu úložiště přidruženého ke clusteru.

    ![Uložení výsledku dotazu Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Uložení výsledku dotazu Hive")

Po dokončení úlohy Hive můžete [Exportovat výsledky do databáze Azure SQL nebo databáze systému SQL Server](apache-hadoop-use-sqoop-mac-linux.md), můžete také [zobrazit výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o použití podregistru v HDInsight najdete v tématu [použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

> [!NOTE]  
> Pokud *hned* pokračujete na další článek a naučíte se, jak spouštět operace ETL pomocí Hadoop ve službě HDInsight, můžete chtít udržovat cluster spuštěný. Důvodem je to, že v tomto kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale neprojdete dalším článkem hned, musíte ho teď odstranit.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Postup odstranění clusteru a/nebo výchozího účtu úložiště

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Odstranění clusteru HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Odstranění clusteru HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Správce prostředků a jak provádět základní dotazy na podregistr. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
>[Extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
