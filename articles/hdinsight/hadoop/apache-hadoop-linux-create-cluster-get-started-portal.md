---
title: 'Rychlý Start: Apache Hadoop, Apache Hive & portálu Azure HDInsight'
description: V tomto rychlém startu použijete Azure Portal k vytvoření clusteru HDInsight Hadoop.
keywords: začínáme používat hadoop, hadoop linux, hadoop rychlý start, hive začínáme, hive rychlý start
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 02990ce287aa423481c1bfe8ec5e2a8a4cfdb555
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858485"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Rychlý Start: Vytvoření clusteru Apache Hadoop ve službě Azure HDInsight pomocí Azure Portal

V tomto článku se naučíte, jak vytvářet clustery Apache Hadoop v HDInsight pomocí Azure Portal a pak spouštět Apache Hive úlohy v HDInsight. Většina úloh Hadoop jsou dávkové úlohy. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V tomto článku provedete všechny tři úlohy. Podrobné vysvětlení dostupných konfigurací najdete v tématu [Nastavení clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Další informace o použití portálu k vytváření clusterů najdete v tématu [Vytvoření clusterů na portálu](../hdinsight-hadoop-create-linux-clusters-portal.md).

V tomto rychlém startu pomocí webu Azure Portal vytvoříte cluster HDInsight Hadoop. K vytvoření clusteru můžete použít také [šablonu Azure Resource Manageru](apache-hadoop-linux-tutorial-get-started.md).

V současné době se HDInsight dodává se [sedmi různými typy clusterů](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných komponent ve službě HDInsight najdete v tématu [co je nového v Apache Hadoop verzích clusterů poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)  

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

V této části vytvoříte cluster Hadoop v HDInsight pomocí webu Azure Portal.

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. V horní nabídce vyberte **+ vytvořit prostředek**.

    ![Vytvoření clusteru HDInsight prostředku](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Vytvoření clusteru HDInsight prostředku")

1. Vyberte **Analytics**  >  **Azure HDInsight** a přejdete na stránku **vytvořit cluster HDInsight** .

1. Na kartě **základy** zadejte následující informace:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné    |  V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster. |
    |Skupina prostředků     | V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**.|
    |Název clusteru   | Zadejte globálně jedinečný název. Název může obsahovat až 59 znaků včetně písmen, číslic a spojovníků. První a poslední znak jména nesmí být spojovníky. |
    |Oblast    | V rozevíracím seznamu vyberte oblast, ve které se cluster vytvoří.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |
    |Typ clusteru| Vyberte **možnost vybrat typ clusteru**. Pak jako typ clusteru vyberte **Hadoop** .|
    |Verze|V rozevíracím seznamu vyberte **verzi**. Pokud si nejste jisti, co si můžete vybrat, použijte výchozí verzi.|
    |Přihlašovací uživatelské jméno a heslo clusteru    | Výchozí přihlašovací jméno je **admin**. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden jiný než alfanumerický znak (kromě znaků) \) . Ujistěte se, že **nezadáváte** běžné heslo, jako je například Pass@word1.|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser**.  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použít heslo přihlášení clusteru pro SSH| Toto políčko zaškrtněte, pokud chcete pro uživatele SSH použít stejné heslo jako ten, který jste zadali pro uživatele přihlášení clusteru.|

    ![Začínáme s HDInsight Linux poskytují základní hodnoty clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "Zadejte základní hodnoty pro vytvoření clusteru HDInsight.")

    Vyberte **Další: >>úložiště ** pro přechod k nastavení úložiště.

1. Na kartě **úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **vybrat ze seznamu**.|
    |Účet primárního úložiště|Pomocí rozevíracího seznamu vyberte existující účet úložiště, nebo vyberte **vytvořit novou**. Pokud vytvoříte nový účet, musí mít název délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.|
    |Kontejner|Použijte automaticky vyplněnou hodnotu.|

    ![Začínáme s HDInsight Linux poskytují hodnoty úložiště clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Zadejte hodnoty úložiště pro vytvoření clusteru HDInsight.")

    Každý cluster má [účet Azure Storage](../hdinsight-hadoop-use-blob-storage.md), [Azure Data Lake Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)nebo [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  závislost. Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

    Vyberte kartu **Revize + vytvořit** .

1. Na kartě **Revize + vytvořit** ověřte hodnoty, které jste vybrali v předchozích krocích.

    ![Souhrn clusteru HDInsight Linux Začínáme](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Souhrn clusteru HDInsight Linux Začínáme")

1. Vyberte **Vytvořit**. Vytvoření clusteru trvá přibližně 20 minut.

    Po vytvoření clusteru se zobrazí stránka přehledu clusteru na webu Azure Portal.

    ![Počáteční nastavení clusteru HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Vlastnosti clusteru HDInsight")

## <a name="run-apache-hive-queries"></a>Spuštění dotazů Apache Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější součástí používanou v HDInsight. Existuje mnoho způsobů spouštění úloh Hive v HDInsight. V tomto rychlém startu použijete zobrazení podregistru Ambari z portálu. Další metody pro odesílání úloh Hive naleznete v části [Použití Hive v HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Zobrazení Apache Hive není v HDInsight 4,0 k dispozici.

1. Pokud chcete otevřít Ambari, vyberte **Řídicí panel clusteru**, jak je znázorněno na předchozím snímku obrazovky.  Můžete také přejít na  `https://ClusterName.azurehdinsight.net` místo, kde `ClusterName` je cluster, který jste vytvořili v předchozí části.

    ![Řídicí panel clusteru HDInsight Linux Začínáme](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Řídicí panel clusteru HDInsight Linux Začínáme")

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno **admin**.

3. Otevřete **Zobrazení Hive**, jak je znázorněno na následujícím snímku obrazovky:

    ![Výběr zobrazení podregistru z Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Nabídka prohlížeče podregistru HDInsight")

4. Na kartě **DOTAZ** vložte následující příkazy HiveQL do pracovního listu:

    ```sql
    SHOW TABLES;
    ```

    ![Editor dotazů zobrazení podregistru HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Editor dotazů zobrazení podregistru HDInsight")

5. Vyberte **Execute** (Provést). Karta **VÝSLEDKY** se zobrazí pod kartou **DOTAZ** a zobrazí informace o úloze. 

    Po dokončení dotazu se na kartě **dotaz** zobrazí výsledky operace. Zobrazí jedna tabulka s názvem **hivesampletable**. Tato vzorová tabulka Hive obsahuje všechny clustery HDInsight.

    ![Výsledky zobrazení Apache Hive HDInsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Výsledky zobrazení Apache Hive HDInsight")

6. Opakujte kroky 4 a 5 a spusťte následující dotaz:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Výsledky dotazu můžete také uložit. Vyberte tlačítko s nabídkou na pravé straně a určete, jestli chcete stáhnout výsledky jako soubor CSV nebo je uložit do účtu úložiště přidruženého ke clusteru.

    ![Uložit výsledek Apache Hive dotazu](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Uložit výsledek Apache Hive dotazu")

Po dokončení úlohy podregistru můžete [Exportovat výsledky do Azure SQL Database nebo SQL Server databáze](apache-hadoop-use-sqoop-mac-linux.md), můžete také [vizualizovat výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o použití podregistru v HDInsight najdete v tématu [použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

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
