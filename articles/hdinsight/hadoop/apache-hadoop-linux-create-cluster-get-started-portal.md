---
title: 'Rychlý Start: Apache Hadoop a Apache Hive v HDInsight pomocí Azure Portal'
description: V tomto rychlém startu použijete Azure Portal k vytvoření clusteru HDInsight Hadoop.
keywords: Začínáme se systémem Hadoop, Hadoop Linux, Hadoop pro rychlé zprovoznění, Začínáme s podregistry, rychlý Start podregistru
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.topic: quickstart
ms.date: 09/25/2019
ms.openlocfilehash: 5d87cc7fdcd9c8065c2a9886b970b406df0d8fc8
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71677917"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý Start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí Azure Portal

V tomto článku se naučíte, jak vytvářet clustery [Apache Hadoop](https://hadoop.apache.org/) v hdinsight pomocí Azure Portal a pak spouštět Apache Hive úlohy v HDInsight. Většina úloh systému Hadoop je dávková úloha. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V tomto článku provedete všechny tři úkoly.

V tomto rychlém startu použijete Azure Portal k vytvoření clusteru HDInsight Hadoop. Cluster můžete také vytvořit pomocí [šablony Azure Resource Manager](apache-hadoop-linux-tutorial-get-started.md).

Aktuálně se HDInsight dodává se [sedmi různými typy clusterů](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje jinou sadu komponent. Všechny typy clusterů podporují podregistr. Seznam podporovaných komponent ve službě HDInsight najdete v tématu [co je nového v Apache Hadoop verzích clusterů poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)  

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

V této části vytvoříte cluster Hadoop v HDInsight pomocí Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Z Azure Portal můžete přejít k části **vytvoření prostředku** > **Analytics** > **HDInsight**.

    ![Vytvoření clusteru HDInsight s prostředkem](./media/apache-hadoop-linux-create-cluster-get-started-portal/create-hdinsight-cluster.png "Vytvoření clusteru HDInsight")

1. V části **základy**zadejte nebo vyberte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |formě    |  Vyberte své předplatné Azure. |
    |Skupina prostředků     | Vytvořte skupinu prostředků nebo vyberte existující skupinu prostředků.  Skupina prostředků je kontejner součástí Azure.  V tomto případě skupina prostředků obsahuje cluster HDInsight a účet závislého Azure Storage. |
    |Název clusteru   | Zadejte název clusteru Hadoop. Vzhledem k tomu, že všechny clustery v HDInsight sdílejí stejný obor názvů DNS, musí být tento název jedinečný. Název může obsahovat až 59 znaků včetně písmen, číslic a spojovníků. První a poslední znak jména nesmí být spojovníky. |
    |Umístění    | Vyberte umístění Azure, ve kterém chcete cluster vytvořit.  Pro lepší výkon vyberte umístění blíž. |
    |Typ clusteru| Vyberte **možnost vybrat typ clusteru**. Pak jako typ clusteru vyberte **Hadoop** .|
    |Version|Bude zadána výchozí verze typu clusteru. V rozevíracím seznamu vyberte, pokud chcete zadat jinou verzi.|
    |Uživatelské jméno a heslo přihlášení clusteru    | Výchozí přihlašovací jméno je **admin**. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků ' "' ' \). Ujistěte se, že **neposkytujete** společná hesla, jako je například "Pass@word1".|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser**.  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použít heslo přihlášení clusteru pro SSH| Toto políčko zaškrtněte, pokud chcete pro uživatele SSH použít stejné heslo jako ten, který jste zadali pro uživatele přihlášení clusteru.|

    ![Začínáme se systémem HDInsight Linux zadáním základních hodnot clusteru]získáte(./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics-blank.png "základní hodnoty pro vytvoření clusteru HDInsight") .

    Vyberte **Další: > úložiště >** , abyste mohli přejít na nastavení úložiště.

1. Na kartě **úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **vybrat ze seznamu**.|
    |Primární účet úložiště|Pomocí rozevíracího seznamu vyberte existující účet úložiště, nebo vyberte **vytvořit novou**. Pokud vytvoříte nový účet, musí mít název délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.|
    |Vnitřního|Použijte automaticky vyplněnou hodnotu.|

    ![Začínáme s HDInsight Linux poskytují hodnoty úložiště clusteru], které(./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage-blank.png "poskytují hodnoty úložiště pro vytvoření clusteru HDInsight") .

    Vyberte kartu **Revize + vytvořit** .

1. Na kartě **Revize + vytvořit** ověřte hodnoty, které jste vybrali v předchozích krocích.

    Shrnutí ![clusteru HDInsight Linux]Začínáme souhrn clusteru(./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "HDInsight Linux") Začínáme

1. Vyberte **vytvořit**. Vytvoření clusteru trvá přibližně 20 minut.

Po vytvoření clusteru se na Azure Portal zobrazí stránka s přehledem clusteru.

![Začátek nastavení clusteru HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Vlastnosti clusteru HDInsight")

Každý cluster má [účet Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo závislost [Azure Data Lakeho účtu](../hdinsight-hadoop-use-data-lake-store.md) . Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

> [!NOTE]  
> Další metody vytváření clusterů a porozumění vlastnostem používaným v tomto rychlém startu najdete v tématu [Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="run-apache-hive-queries"></a>Spuštění dotazů Apache Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější komponenta používaná v HDInsight. Existuje mnoho způsobů, jak spouštět úlohy podregistru v HDInsight. V tomto rychlém startu použijete zobrazení podregistru Ambari z portálu. Další metody pro odesílání úloh podregistru najdete v tématu [použití podregistru v HDInsight](hdinsight-use-hive.md).

1. Pokud chcete otevřít Ambari, na předchozím snímku obrazovky vyberte **řídicí panel clusteru**.  Můžete také přejít na `https://ClusterName.azurehdinsight.net`, kde `ClusterName` je cluster, který jste vytvořili v předchozí části.

    Řídicí panel clusteru HDInsight ![Linux]Začínáme v clusteru(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "HDInsight Linux Začínáme")

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno je **admin**.

3. Otevřete **zobrazení podregistru** , jak je znázorněno na následujícím snímku obrazovky:

    ![Výběr zobrazení podregistru z](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "nabídky prohlížeč podregistru Ambari HDInsight")

4. Na kartě **dotaz** vložte následující příkazy HiveQL do listu:

    ```sql
    SHOW TABLES;
    ```

    Editor dotazů zobrazení(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "podregistru HDInsight") ![zobrazení]podregistru HDInsight

5. Vyberte **provést**. Karta **výsledky** se zobrazí pod kartou **dotaz** a zobrazí informace o úloze. 

    Po dokončení dotazu se na kartě **dotaz** zobrazí výsledky operace. Zobrazí se jedna tabulka s názvem **hivesampletable**. Tato ukázková tabulka podregistru obsahuje všechny clustery HDInsight.

    ![Hdinsight Apache Hive zobrazení výsledků]v(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "HDInsight Apache Hive zobrazení výsledků")

6. Opakujte krok 4 a krok 5 a spusťte následující dotaz:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Výsledky dotazu můžete také uložit. Na pravé straně vyberte tlačítko nabídky a určete, jestli chcete výsledky stáhnout jako soubor CSV, nebo ho uložit do účtu úložiště přidruženého ke clusteru.

    ![Uložit výsledek Apache Hive dotazu](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "uložit výsledek dotazu Apache Hive")

Po dokončení úlohy podregistru můžete [Exportovat výsledky do služby Azure SQL Database nebo databáze SQL Server](apache-hadoop-use-sqoop-mac-linux.md), můžete také [vizualizovat výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o použití podregistru v HDInsight najdete v tématu [použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

> [!NOTE]  
> Pokud *hned* pokračujete na další článek a naučíte se, jak spouštět operace ETL pomocí Hadoop ve službě HDInsight, můžete chtít udržovat cluster spuštěný. Důvodem je to, že v tomto kurzu musíte cluster Hadoop vytvořit znovu. Pokud ale neprojdete dalším článkem hned, musíte ho teď odstranit.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Postup odstranění clusteru a/nebo výchozího účtu úložiště

1. Vraťte se na kartu prohlížeče, kde máte Azure Portal. Je třeba na stránce Přehled clusteru. Pokud chcete jenom odstranit cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Azure HDInsight odstranit]cluster(./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Odstranit cluster Azure HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků, která obsahuje cluster a výchozí účet úložiště. Poznámka: odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, vyberte možnost jenom odstranit cluster.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster HDInsight se systémem Linux pomocí šablony Správce prostředků a jak provádět základní dotazy na podregistr. V dalším článku se dozvíte, jak provádět operace extrakce, transformace a načítání (ETL) pomocí Hadoop ve službě HDInsight.

> [!div class="nextstepaction"]
> [Extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
