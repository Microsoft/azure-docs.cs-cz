---
title: 'Úvodní příručka: Apache Hadoop, Apache Hive & portál Azure HDInsight'
description: V tomto rychlém startu použijete portál Azure k vytvoření clusteru HDInsight Hadoop.
keywords: začínáme používat hadoop, hadoop linux, hadoop rychlý start, hive začínáme, hive rychlý start
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/24/2020
ms.openlocfilehash: 8939d9b342094342d576c00cf02e622286c8fc0f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80130574"
---
# <a name="quickstart-create-apache-hadoop-cluster-in-azure-hdinsight-using-azure-portal"></a>Úvodní příručka: Vytvoření clusteru Apache Hadoop v Azure HDInsight pomocí portálu Azure

V tomto článku se dozvíte, jak vytvořit clustery Apache Hadoop v HDInsight pomocí portálu Azure a pak spustit úlohy Apache Hive v HDInsight. Většina úloh Hadoop jsou dávkové úlohy. Vytvoříte cluster, spustíte některé úlohy a pak cluster odstraníte. V tomto článku provedete všechny tři úlohy. Podrobné vysvětlení dostupných konfigurací naleznete v tématu [Nastavení clusterů v hdinsightu](../hdinsight-hadoop-provision-linux-clusters.md). Další informace týkající se použití portálu k vytvoření clusterů naleznete [v tématu Vytváření clusterů na portálu](../hdinsight-hadoop-create-linux-clusters-portal.md).

V tomto rychlém startu pomocí webu Azure Portal vytvoříte cluster HDInsight Hadoop. K vytvoření clusteru můžete použít také [šablonu Azure Resource Manageru](apache-hadoop-linux-tutorial-get-started.md).

V současné době je hdinsight dodáván se [sedmi různými typy clusterů](../hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru podporuje odlišnou sadu komponent. Všechny typy clusteru podporují Hive. Seznam podporovaných součástí ve službě HDInsight najdete v tématu [Co je nového ve verzích clusteru Apache Hadoop poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)  

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-an-apache-hadoop-cluster"></a>Vytvoření clusteru Apache Hadoop

V této části vytvoříte cluster Hadoop v HDInsight pomocí webu Azure Portal.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. V horní nabídce vyberte **+ Vytvořit prostředek**.

    ![Vytvoření clusteru HDInsight o prostředku](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-create-resource.png "Vytvoření clusteru HDInsight o prostředku")

1. Vyberte **Analytics** > **Azure HDInsight** a přejděte na stránku **vytvořit cluster HDInsight.**

1. Na kartě **Základy** zadejte následující informace:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Předplatné    |  V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster. |
    |Skupina prostředků     | V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**.|
    |Název clusteru   | Zadejte globálně jedinečný název. Název se může skládat až z 59 znaků včetně písmen, čísel a spojovníků. První a poslední znak názvu nemůže být spojovníky. |
    |Region (Oblast)    | V rozevíracím seznamu vyberte oblast, ve které je cluster vytvořen.  Pro dosažení lepšího výkonu zvolte co nejbližší umístění. |
    |Typ clusteru| Vyberte **vybrat typ clusteru**. Pak vyberte **Hadoop** jako typ clusteru.|
    |Version|V rozevíracím seznamu vyberte **verzi**. Pokud nevíte, co si vybrat, použijte výchozí verzi.|
    |Přihlašovací uživatelské jméno a heslo clusteru    | Výchozí přihlašovací jméno je **admin**. Heslo musí mít délku alespoň 10 znaků a musí obsahovat alespoň jednu číslici, jedno velké a jedno malé \)písmeno, jeden nealfanumerický znak (kromě znaků " " . Ujistěte se, že **nezadáváte** běžné heslo, jako je například Pass@word1.|
    |Uživatelské jméno Secure Shell (SSH) | Výchozí uživatelské jméno je **sshuser**.  Pro uživatelské jméno SSH můžete zadat jiný název. |
    |Použití přihlašovacího hesla clusteru pro SSH| Zaškrtnutím tohoto políčka použijete stejné heslo pro uživatele SSH jako to, které jste zadali pro uživatele přihlášení k clusteru.|

    ![HDInsight Linux začíná poskytovat základní hodnoty clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-basics.png "Poskytnutí základních hodnot pro vytvoření clusteru HDInsight")

    Vyberte **další: Úložiště >>** pro přejděte do nastavení úložiště.

1. Na kartě **Úložiště** zadejte následující hodnoty:

    |Vlastnost  |Popis  |
    |---------|---------|
    |Typ primárního úložiště|Použijte výchozí hodnotu **Azure Storage**.|
    |Metoda výběru|Použijte výchozí hodnotu **Vybrat ze seznamu**.|
    |Účet primárního úložiště|V rozevíracím seznamu vyberte existující účet úložiště nebo vyberte **Vytvořit nový**. Pokud vytvoříte nový účet, musí být název o délce 3 až 24 znaků a může obsahovat pouze čísla a malá písmena.|
    |Kontejner|Použijte automaticky vyplněnou hodnotu.|

    ![HDInsight Linux začíná poskytovat hodnoty úložiště clusteru](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-storage.png "Poskytnutí hodnot úložiště pro vytvoření clusteru HDInsight")

    Každý cluster obsahuje závislost [účtu Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo [účtu Azure Data Lake](../hdinsight-hadoop-use-data-lake-store.md). Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěny ve stejné oblasti Azure. Odstraněním clusterů účet úložiště neodstraníte.

    Vyberte kartu **Revize + vytvoření.**

1. Na kartě **Revize + vytvoření** ověřte hodnoty vybrané v předchozích krocích.

    ![Souhrn clusteru začíná hdinsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/azure-portal-cluster-review-create-hadoop.png "Souhrn clusteru začíná hdinsight Linux")

1. Vyberte **Vytvořit**. Vytvoření clusteru trvá přibližně 20 minut.

    Po vytvoření clusteru se zobrazí stránka přehledu clusteru na webu Azure Portal.

    ![Počáteční nastavení clusteru HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/cluster-settings-overview.png "Vlastnosti clusteru HDInsight")

## <a name="run-apache-hive-queries"></a>Spuštění dotazů Apache Hive

[Apache Hive](hdinsight-use-hive.md) je nejoblíbenější součástí používanou v HDInsight. Existuje mnoho způsobů spouštění úloh Hive v HDInsight. V tomto rychlém startu použijete zobrazení Ambari Hive z portálu. Další metody pro odesílání úloh Hive naleznete v části [Použití Hive v HDInsight](hdinsight-use-hive.md).

> [!NOTE]
> Apache Hive View není v HDInsight 4.0 k dispozici.

1. Pokud chcete otevřít Ambari, vyberte **Řídicí panel clusteru**, jak je znázorněno na předchozím snímku obrazovky.  Můžete také přejít na místo, `https://ClusterName.azurehdinsight.net` kde `ClusterName` je cluster, který jste vytvořili v předchozí části.

    ![Řídicí panel clusteru ZAČÍNÁ HDInsight Linux](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-get-started-open-cluster-dashboard.png "Řídicí panel clusteru ZAČÍNÁ HDInsight Linux")

2. Zadejte uživatelské jméno a heslo Hadoop, které jste zadali při vytváření clusteru. Výchozí uživatelské jméno **admin**.

3. Otevřete **Zobrazení Hive**, jak je znázorněno na následujícím snímku obrazovky:

    ![Výběr zobrazení Hive z Ambari](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-select-hive-view.png "Nabídka HDInsight Hive Viewer")

4. Na kartě **DOTAZ** vložte následující příkazy HiveQL do pracovního listu:

    ```sql
    SHOW TABLES;
    ```

    ![Editor dotazů zobrazení hive hdinsight](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdi-apache-hive-view1.png "Editor dotazů zobrazení hive hdinsight")

5. Vyberte **Provést**. Karta **VÝSLEDKY** se zobrazí pod kartou **DOTAZ** a zobrazí informace o úloze. 

    Po dokončení dotazu se na kartě **DOTAZ** zobrazí výsledky operace. Zobrazí jedna tabulka s názvem **hivesampletable**. Tato vzorová tabulka Hive obsahuje všechny clustery HDInsight.

    ![Výsledky zobrazení HDInsight Apache Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-hive-views.png "Výsledky zobrazení HDInsight Apache Hive")

6. Opakujte kroky 4 a 5 a spusťte následující dotaz:

    ```sql
    SELECT * FROM hivesampletable;
    ```

7. Výsledky dotazu můžete také uložit. Vyberte tlačítko s nabídkou na pravé straně a určete, jestli chcete stáhnout výsledky jako soubor CSV nebo je uložit do účtu úložiště přidruženého ke clusteru.

    ![Uložit výsledek dotazu Apache Hive](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-linux-hive-view-save-results.png "Uložit výsledek dotazu Apache Hive")

Po dokončení úlohy Hive můžete [exportovat výsledky do databáze Azure SQL Database nebo SQL Server](apache-hadoop-use-sqoop-mac-linux.md), můžete také [vizualizovat výsledky pomocí aplikace Excel](apache-hadoop-connect-excel-power-query.md). Další informace o používání Hive v HDInsight, najdete [v tématu Použití Apache Hive a HiveQL s Apache Hadoop v HDInsight analyzovat ukázkový soubor Apache log4j](hdinsight-use-hive.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

> [!NOTE]  
> Pokud *jste okamžitě* přejdete k dalšímu článku se dozvíte, jak spustit operace ETL pomocí Hadoop na HDInsight, můžete chtít zachovat clusteru spuštěný. Je to proto, že v kurzu budete muset znovu vytvořit cluster Hadoop. Pokud však neprocházíte dalším článkem hned, musíte nyní cluster odstranit.

### <a name="to-delete-the-cluster-andor-the-default-storage-account"></a>Postup odstranění clusteru a/nebo výchozího účtu úložiště

1. Vraťte se na kartu prohlížeče s webem Azure Portal. Měli byste být na stránce s přehledem clusteru. Pokud chcete odstranit jenom cluster, ale zachovat výchozí účet úložiště, vyberte **Odstranit**.

    ![Azure HDInsight odstranit cluster](./media/apache-hadoop-linux-create-cluster-get-started-portal/hdinsight-delete-cluster.png "Odstranění clusteru Azure HDInsight")

2. Pokud chcete odstranit cluster i výchozí účet úložiště, vyberte název skupiny prostředků (zvýrazněný na předchozím snímku obrazovky) a otevřete stránku skupiny prostředků.

3. Vyberte **Odstranit skupinu prostředků** a odstraňte skupinu prostředků obsahující cluster a výchozí účet úložiště. Upozorňujeme, že odstraněním skupiny prostředků odstraníte účet úložiště. Pokud chcete zachovat účet úložiště, zvolte odstranění samotného clusteru.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit cluster HDInsight založený na Linuxu pomocí šablony Správce prostředků a jak provádět základní dotazy Hive. V dalším článku se dozvíte, jak pomocí Hadoopu ve službě HDInsight provést operaci ETL (extrakce, transformace a načítání).

> [!div class="nextstepaction"]
> [Extrahování, transformace a načítání dat pomocí interaktivního dotazu na hdinsightu](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
