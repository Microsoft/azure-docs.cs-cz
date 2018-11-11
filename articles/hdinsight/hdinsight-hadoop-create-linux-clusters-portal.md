---
title: Vytvořte clustery systému Apache Hadoop pomocí webového prohlížeče – Azure HDInsight
description: Zjistěte, jak vytvářet clustery Apache Hadoop, Apache HBase, Apache Storm nebo Apache Spark v Linuxu pro HDInsight pomocí webového prohlížeče a na portálu Azure preview.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 8d5e29b89d65bc8777feac0c496b3253bc2a6763
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51278986"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Vytvoření clusterů se systémem Linux v HDInsight pomocí webu Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Na webu Azure portal je nástroj pro správu webových služeb a prostředků, které jsou hostované v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvářet clustery HDInsight se systémem Linux pomocí portálu.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Moderní webový prohlížeč**. Na webu Azure portal používá HTML5 a Javascript a nemusí fungovat správně v starší webové prohlížeče.

## <a name="create-clusters"></a>Vytváření clusterů
Na webu Azure portal poskytuje většinu vlastností clusteru. Pomocí šablony Azure Resource Manageru, můžete skrýt mnoho podrobností. Další informace najdete v tématu [vytvořit systémem Linux Hadoop clusterů v HDInsight pomocí šablon Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Klikněte na tlačítko **+ vytvořit prostředek**, klikněte na tlačítko **Analytics**a potom klikněte na tlačítko **HDInsight**.
   
    ![Vytváří se nový cluster na webu Azure Portal](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster.png "vytvoření nového clusteru na portálu Azure portal")

3. V **HDInsight** okna, klikněte na tlačítko **vlastní (velikost, nastavení, aplikace)**, klikněte na tlačítko **Základy**a potom zadejte následující informace.

    ![Vytváří se nový cluster na webu Azure Portal](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-basics.png "vytvoření nového clusteru na portálu Azure portal")

    * Zadejte **Název clusteru**: Tento název musí být globálně jedinečný.

    * Z **předplatné** rozevíracího seznamu, vyberte předplatné Azure, který se používá pro cluster.

    * Klikněte na tlačítko **typ clusteru**a pak vyberte typ clusteru (Hadoopu, Sparku, atd.), kterou chcete vytvořit. Pro **operační systém**, klikněte na tlačítko **Linux** a potom vyberte verzi. Používejte výchozí verzi, pokud si nejste jisti, co k výběru. Další informace najdete v článku [Verze clusterů HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]
        > HDInsight clustery se dělí na celou řadu typů, které odpovídají úlohy nebo technologie, která clusteru je vyladěný pro. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je Storm a HBase na jednom clusteru. 
        > 
        > 
        
    * Pro **uživatelské jméno přihlášení clusteru** a **přihlašovací heslo clusteru**, zadejte uživatelské jméno a heslo pro uživatele s rolí správce.

    * Zadejte **uživatelské jméno SSH** a pokud chcete mít SSH heslo stejné jako heslo správce, který jste zadali dříve, vyberte **použít stejné heslo jako pro přihlášení ke clusteru** zaškrtávací políčko. Pokud ne, zadejte buď **heslo** nebo **veřejný klíč**, který se použije k ověřování uživatele SSH. Doporučujeme používat veřejný klíč. Klikněte v dolní části na tlačítko **Vybrat** a uložte konfiguraci přihlašovacích údajů.
   
    Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * V části **Skupina prostředků** určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující.

    * Zadejte datové centrum **umístění** ve kterém se cluster vytvoří.

    * Klikněte na **Další**.

4. V **zabezpečení a sítě**, clusteru můžete připojit k virtuální síti pomocí zadané rozevíracího seznamu. Vyberte virtuální síť Azure a podsíť, pokud chcete umístit clusteru do virtuální sítě. Informace o používání HDInsight s virtuální sítí, včetně požadavků na konkrétní konfiguraci virtuální sítě, naleznete v tématu [možnosti rozšíření HDInsight pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Pokud chcete použít **Enterprise Security Package**, budete také postupovat podle pokynů tady: [konfigurace clusteru HDInsight s balíčkem Enterprise Security Package pomocí Azure Active Directory Domain Services](https://docs.microsoft.com/en-us/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

5. Pro **úložiště**, zda má být Azure Storage (WASB) nebo Data Lake Storage jako výchozí úložiště. Podívejte se na tabulce pro další informace.

    ![Vytváří se nový cluster na webu Azure Portal](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-storage.png "vytvoření nového clusteru na portálu Azure portal")

    | Úložiště                                      | Popis |
    |----------------------------------------------|-------------|
    | **Azure Storage BLOB jako výchozí úložiště**   | <ul><li>Pro **typ primárního úložiště**vyberte **služby Azure Storage**. Potom pro **metodu výběru**, můžete zvolit **mé odběry** Pokud budete chtít zadat účet úložiště, který je součástí vašeho předplatného Azure a pak vyberte účet úložiště. V opačném případě klikněte na tlačítko **přístupový klíč** a zadejte informace o účtu úložiště, který chcete vybrat z mimo vašeho předplatného Azure.</li><li>Pro **výchozí kontejner**, můžete přejít pomocí výchozí název kontejneru navrhl na portálu nebo zadat vlastní.</li><li>Pokud používáte WASB jako výchozím úložištěm, (volitelně) kliknete na **další účty úložiště** k určení dalších účtů úložiště pro přidružení ke clusteru. Pro **klíčů k úložišti Azure**, klikněte na tlačítko **přidat klíč úložiště**, a potom zadáte účet úložiště z vašich předplatných Azure nebo z jiných předplatných (tím, že přístupový klíč účtu úložiště).</li><li>Pokud používáte WASB jako výchozím úložištěm, (volitelně) kliknete na **přístupu k Data Lake Store** určit úložiště Azure Data Lake jako další úložiště. Další informace najdete v tématu [rychlý start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | **Azure Data Lake Storage jako výchozí úložiště** | Pro **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen1** nebo **Azure Data Lake Storage Gen2 (Preview)** a pak se podívejte na článek [rychlý start : Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) pokyny. |
    | **Externí metaúložiště**                      | Volitelně můžete zadat SQL databázi k uložení metadat Hive a Oozie přidružené ke clusteru. Pro **vybrat databázi SQL pro podregistr** vybrat databázi SQL a pak zadejte uživatelské jméno a heslo pro databázi. Tento postup opakujte pro Oozie metadata.<br><br>Některé aspekty při používání Azure SQL database u metaúložiště. <ul><li>Azure SQL database používá pro metastore musí umožňovat připojení k jiným službám Azure, včetně Azure HDInsight. Azure SQL database řídicí panel, na pravé straně klikněte na název serveru. Jedná se o server, na kterém běží SQL database instance. Po otevření zobrazení serveru, klikněte na tlačítko **konfigurovat**a potom **Azure Services**, klikněte na tlačítko **Ano**a potom klikněte na tlačítko **Uložit**.</li><li>Při vytvoření metastoru, nepoužívejte název databáze, které obsahuje pomlčky nebo pomlčky, protože to může způsobit selhání procesu vytváření clusteru.</li></ul> |

    Klikněte na **Další**. 

    > [!WARNING]
    > Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

6. Případně můžete kliknout **aplikací** instalovat aplikace, které fungují s clustery HDInsight. Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými. Další informace najdete v tématu [aplikací HDInsight nainstalovat](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).


6. Klikněte na tlačítko **velikost clusteru** k zobrazení informací o uzlech, které se používají pro tento cluster. Nastavte počet pracovních uzlů, které potřebujete pro cluster. Také se zobrazí odhadované náklady na provoz clusteru.
   
    ![Cenovými úrovněmi uzlů](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-nodes.png "zadejte počet uzlů clusteru")
   
   > [!IMPORTANT]
   > Pokud máte v úmyslu na více než 32 uzly pracovního procesu v clusteru vytváření nebo škálování clusteru po jeho vytvoření, musíte vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.
   > 
   > Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   > 
   > 
   
   Klikněte na tlačítko **Další** uložte konfiguraci cen uzlů.

8. V **akcí skriptů**, si můžete přizpůsobit clusteru a nainstalujte vlastní komponenty.  Tuto možnost použijte, pokud chcete použít vlastní skript, který vlastní cluster, při vytváření clusteru. Další informace o akcí skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).
Klikněte na **Další**.

9. Pro **Souhrn**, zkontrolujte zadané informace, které jste zadali dříve a pak klikněte na tlačítko **vytvořit**.

    ![Cenovými úrovněmi uzlů](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-summary.png "zadejte počet uzlů clusteru")
    
    > [!NOTE]
    > Pro vytvoření, obvykle přibližně 15 minut clusteru nějakou dobu trvá. Použít na dlaždici na úvodním panelu, nebo **oznámení** položku na levé straně stránky můžete zkontrolovat na během procesu zřizování.
    > 
    > 
10. Po dokončení procesu vytváření kliknutím na dlaždici clusteru na úvodní panel. V okně clusteru obsahuje následující informace.
    
    ![Rozhraní clusteru](./media/hdinsight-hadoop-create-linux-cluster-portal/hdinsight-create-cluster-completed.png "vlastnosti clusteru")
    
    Použijte následující postup k pochopení ikony v horní části.
    
    * **Přehled** karta obsahuje všechny základní informace o clusteru, jako je například název, patří do skupiny prostředků, umístění, operační systém, adresa URL pro řídicí panel clusteru atd.
    * **Řídicí panel** vás přesměruje na portál Ambari přidružené ke clusteru.
    * **Secure Shell**: informace potřebné pro přístup ke clusteru pomocí SSH.
    * **Škálování clusteru** umožňuje zvýšit počet pracovních uzlů, které jsou přidružené ke clusteru.
    * **Odstranit**: Odstraní HDInsight cluster.
    

## <a name="customize-clusters"></a>Přizpůsobení clusterů
* Zobrazit [HDInsight přizpůsobení clusterů pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Zobrazit [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Odstranění clusteru
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Další postup
Teď, když úspěšně vytvoříte HDInsight cluster, použijte následující postup, jak pracovat s vaším clusterem:

### <a name="hadoop-clusters"></a>Clustery Hadoop
* [Použití Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Pigu se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clustery HBase
* [Začínáme s HBase ve službě HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací v Javě pro HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clustery Storm
* [Vývoj topologie Java pro Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent v Pythonu v Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií se Stormem v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clustery Spark
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](spark/apache-spark-livy-rest-interface.md)
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](spark/apache-spark-machine-learning-mllib-ipython.md)

