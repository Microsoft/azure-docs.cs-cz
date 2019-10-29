---
title: 'Rychlý Start: Apache Hadoop, Apache Hive & portálu Azure HDInsight'
description: V tomto rychlém startu použijete Azure Portal k vytvoření clusteru HDInsight Hadoop.
keywords: začínáme používat hadoop, hadoop linux, hadoop rychlý start, hive začínáme, hive rychlý start
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: cab46c4fe2ff0bd1f86c6d98a3b37efb3547a3ce
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044573"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý Start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí Azure Portal

V tomto článku se naučíte, jak vytvářet clustery [Apache Hadoop](https://hadoop.apache.org/) v hdinsight pomocí Azure Portal a pak spouštět Apache Hive úlohy v HDInsight. Většina úloh Hadoop jsou dávkové úlohy. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V tomto článku provedete všechny tři úlohy.

V tomto rychlém startu pomocí webu Azure Portal vytvoříte cluster HDInsight Hadoop. K vytvoření clusteru můžete použít také [šablonu Azure Resource Manageru](apache-hadoop-linux-tutorial-get-started.md).

Aktuálně se HDInsight dodává se [sedmi různými typy clusteru](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných komponent ve službě HDInsight najdete v tématu [co je nového v Apache Hadoop verzích clusterů poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)  

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

V této části vytvoříte cluster Hadoop v HDInsight pomocí webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Z Azure Portal můžete přejít k části **vytvoření prostředku** > **Analytics** > **HDInsight**.

    ![Vytvoření clusteru HDInsight prostředku](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Vytvoření clusteru HDInsight prostředku")

1. V části **základy**zadejte nebo vyberte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné    |  Vyberte své předplatné Azure. |
    |Skupina prostředků     | Vytvořte skupinu prostředků nebo vyberte existující.  Skupina prostředků je kontejner komponent Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a závislý účet služby Azure Storage. |
    |Název clusteru   | Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery ve službě HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může obsahovat až 59 znaků včetně písmen, číslic a spojovníků. První a poslední znak názvu nemůže být pomlčka. |
    |Umístění    | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |
    |Typ clusteru| Vyberte **možnost vybrat typ clusteru**. Pak jako typ clusteru vyberte **Hadoop** .|
    |Version|Bude zadána výchozí verze typu clusteru. V rozevíracím seznamu vyberte, pokud chcete zadat jinou verzi.|
    |Uživatelské jméno a heslo přihlášení clusteru    | Výchozí přihlašovací jméno je **admin**. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků ' "' ' \). Ujistěte se, že **nezadáváte** běžné heslo, jako je například Pass@word1.|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser** (uživatelssh).  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použít heslo přihlášení clusteru pro SSH| Toto políčko zaškrtněte, pokud chcete pro uživatele SSH použít stejné heslo jako ten, který jste zadali pro uživatele přihlášení clusteru.|

    ![Začínáme s HDInsight Linux poskytují základní hodnoty clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "Zadejte základní hodnoty pro vytvoření clusteru HDInsight.")

    Vyberte **Další: > úložiště >** , abyste mohli přejít na nastavení úložiště.

1. Na kartě **úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **vybrat ze seznamu**.|
    |Účet primárního úložiště|Pomocí rozevíracího seznamu vyberte existující účet úložiště, nebo vyberte **vytvořit novou**. Pokud vytvoříte nový účet, musí mít název délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.|
    |Kontejner|Použijte automaticky vyplněnou hodnotu.|

    ![Začínáme s HDInsight Linux poskytují hodnoty úložiště clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage-blank.png "Zadejte hodnoty úložiště pro vytvoření clusteru HDInsight.")

    Vyberte kartu **Revize + vytvořit** .

1. Na kartě **Revize + vytvořit** ověřte hodnoty, které jste vybrali v předchozích krocích.

    ![Souhrn clusteru HDInsight Linux Začínáme](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Souhrn clusteru HDInsight Linux Začínáme")

1. Vyberte **Create** (Vytvořit). Vytvoření clusteru trvá přibližně 20 minut.

Po vytvoření clusteru se zobrazí stránka přehledu clusteru na webu Azure Portal.

![Nastavení clusteru HDInsight Linux Začínáme](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Vlastnosti clusteru HDInsight")

Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

> [!NOTE]  
> Další metody vytváření clusterů a porozumění vlastnostem používaným v tomto rychlém startu najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Spuštění dotazů Apache Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější součástí používanou v HDInsight. Existuje mnoho způsobů spouštění úloh Hive v HDInsight. V tomto rychlém startu použijete zobrazení podregistru Ambari z portálu. Další metody pro odesílání úloh Hive najdete v části [Použití Hive v HDInsight](hdinsight-use-hive.md).

1. Pokud chcete otevřít Ambari, vyberte **Řídicí panel clusteru**, jak je znázorněno na předchozím snímku obrazovky.  Můžete také přejít na `https://ClusterName.azurehdinsight.net`, kde `ClusterName` je cluster, který jste vytvořili v předchozí části.

    ![Řídicí panel clusteru HDInsight Linux Začínáme](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Řídicí panel clusteru HDInsight Linux Začínáme")

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno **admin**.

3. Otevřete **Zobrazení Hive**, jak je znázorněno na následujícím snímku obrazovky:

    ![Výběr zobrazení podregistru z Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Nabídka prohlížeče podregistru HDInsight")

4. Na kartě **DOTAZ** vložte následující příkazy HiveQL do pracovního listu:

    ```sql
    SHOW TABLES;
    ```

    ![Editor dotazů zobrazení podregistru HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Editor dotazů zobrazení podregistru HDInsight")

5. Vyberte **Provést**. Karta **VÝSLEDKY** se zobrazí pod kartou **DOTAZ** a zobrazí informace o úloze. 

    Po dokončení dotazu se na kartě **dotaz** zobrazí výsledky operace. Zobrazí jedna tabulka s názvem **hivesampletable**. Tato vzorová tabulka Hive obsahuje všechny clustery HDInsight.

    ![Výsledky zobrazení Apache Hive HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Výsledky zobrazení Apache Hive HDInsight")

6. Opakujte kroky 4 a 5 a spusťte následující dotaz:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Výsledky dotazu můžete také uložit. Vyberte tlačítko s nabídkou na pravé straně a určete, jestli chcete stáhnout výsledky jako soubor CSV nebo je uložit do účtu úložiště přidruženého ke clusteru.

    ![Uložit výsledek Apache Hive dotazu](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Uložit výsledek Apache Hive dotazu")

Po dokončení úlohy Hive můžete [Exportovat výsledky do databáze Azure SQL nebo databáze systému SQL Server](apache-hadoop-use-sqoop-mac-linux.md), můžete také [zobrazit výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o použití podregistru v HDInsight najdete v tématu [použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

> [!NOTE]  
> Pokud *hned* pokračujete na další článek a naučíte se, jak spouštět operace ETL pomocí Hadoop ve službě HDInsight, můžete chtít udržovat cluster spuštěný. Důvodem je to, že v tomto kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale neprojdete dalším článkem hned, musíte ho teď odstranit.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Postup odstranění clusteru a/nebo výchozího účtu úložiště

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Cluster pro odstranění Azure HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Odstranit cluster Azure HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Správce prostředků a jak provádět základní dotazy na podregistr. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
> [Extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
