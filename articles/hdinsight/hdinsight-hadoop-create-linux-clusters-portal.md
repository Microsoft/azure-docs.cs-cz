---
title: Vytvořte clustery systému Apache Hadoop pomocí webového prohlížeče – Azure HDInsight
description: Zjistěte, jak vytvářet clustery Apache Hadoop, Apache HBase, Apache Storm nebo Apache Spark v Linuxu pro HDInsight pomocí webového prohlížeče a na portálu Azure preview.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: ca8b398a796284e34ce4e0b577c335552ca75a98
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974263"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-the-azure-portal"></a>Vytvoření clusterů se systémem Linux v HDInsight pomocí webu Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Na webu Azure portal je nástroj pro správu webových služeb a prostředků, které jsou hostované v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvářet clustery HDInsight se systémem Linux pomocí portálu.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Moderní webový prohlížeč**. Na webu Azure portal používá HTML5 a Javascript a nemusí fungovat správně v starší webové prohlížeče.

## <a name="create-clusters"></a>Vytváření clusterů
Na webu Azure portal poskytuje většinu vlastností clusteru. Pomocí šablon Azure Resource Manageru, můžete skrýt mnoho podrobností. Další informace najdete v tématu [clustery založené na Linuxu se vytvořit Apache Hadoop v HDInsight pomocí šablon Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **+ vytvořit prostředek**.

1.  V části **Azure Marketplace**vyberte **Analytics**.

1.  V části **doporučené**vyberte **HDInsight**.
   
    ![Vytváří se nový cluster na webu Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "vytvoření nového clusteru na portálu Azure portal")

1. Z **HDInsight** stránce **vlastní (velikost, nastavení, aplikace)**.

1. Vyberte **1 Základy**a potom zadejte následující informace:

    ![Vytváří se nový cluster na webu Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "vytvoření nového clusteru na portálu Azure portal")

    * Zadejte **název clusteru**: Tento název musí být globálně jedinečný.

    * Z **předplatné** rozevíracího seznamu, vyberte předplatné Azure, který se používá pro cluster.

    * Vyberte **typ clusteru**a pak vyberte typ clusteru (Hadoopu, Sparku, atd.), kterou chcete vytvořit. **Operační systém** bude **Linux**. Vyberte verzi typu clusteru. Používejte výchozí verzi, pokud si nejste jisti, co k výběru. Další informace najdete v článku [Verze clusterů HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > HDInsight clustery se dělí na celou řadu typů, které odpovídají úlohy nebo technologie, která clusteru je vyladěný pro. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je Storm a HBase na jednom clusteru.
        
    * Pro **uživatelské jméno přihlášení clusteru** a **přihlašovací heslo clusteru**, zadejte uživatelské jméno a heslo pro uživatele s rolí správce.

    * Zadejte **uživatelské jméno SSH** a pokud chcete mít SSH heslo stejné jako heslo správce, který jste zadali dříve, vyberte **použít stejné heslo jako pro přihlášení ke clusteru** zaškrtávací políčko. Pokud ne, zadejte buď **heslo** nebo **veřejný klíč**, který se použije k ověřování uživatele SSH. Doporučujeme používat veřejný klíč. Klikněte v dolní části na tlačítko **Vybrat** a uložte konfiguraci přihlašovacích údajů.
   
        Další informace najdete v tématu [Použití SSH se službou HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    * V části **Skupina prostředků** určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující.

    * Zadejte datové centrum **umístění** ve kterém se cluster vytvoří.

    * Vyberte **Další** přesunout na další stránku.

4. Z **2 zabezpečení a sítě**, clusteru můžete připojit k virtuální síti pomocí zadané rozevíracího seznamu. Vyberte virtuální síť Azure a podsíť, pokud chcete umístit clusteru do virtuální sítě. Informace o používání HDInsight s virtuální sítí, včetně požadavků na konkrétní konfiguraci virtuální sítě, naleznete v tématu [možnosti rozšíření HDInsight pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Pokud chcete použít **Enterprise Security Package**, budete také postupovat podle pokynů tady: [Konfigurace clusteru HDInsight pomocí Azure Active Directory Domain Services s balíčkem Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Vyberte **Další** přesunout na další stránku.


5. Z **3 úložiště**, zda má být Azure Storage (WASB) nebo Data Lake Storage jako výchozí úložiště. Podívejte se na tabulce pro další informace.

     ![Vytváří se nový cluster na webu Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "vytvoření nového clusteru na portálu Azure portal")

     | Storage                                      | Popis |
     |----------------------------------------------|-------------|
     | **Azure Storage BLOB jako výchozí úložiště**   | <ul><li>Pro **typ primárního úložiště**vyberte **služby Azure Storage**. Potom pro **metodu výběru**, můžete zvolit **mé odběry** Pokud budete chtít zadat účet úložiště, který je součástí vašeho předplatného Azure a pak vyberte účet úložiště. V opačném případě klikněte na tlačítko **přístupový klíč** a zadejte informace o účtu úložiště, který chcete vybrat z mimo vašeho předplatného Azure.</li><li>Pro **výchozí kontejner**, můžete přejít pomocí výchozí název kontejneru navrhl na portálu nebo zadat vlastní.</li><li>Pokud používáte WASB jako výchozím úložištěm, (volitelně) kliknete na **další účty úložiště** k určení dalších účtů úložiště pro přidružení ke clusteru. Pro **klíčů k úložišti Azure**, klikněte na tlačítko **přidat klíč úložiště**, a potom zadáte účet úložiště z vašich předplatných Azure nebo z jiných předplatných (tím, že přístupový klíč účtu úložiště).</li><li>Pokud používáte WASB jako výchozím úložištěm, (volitelně) kliknete na **přístupu k Data Lake Storage** určit úložiště Azure Data Lake jako další úložiště. Další informace najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage jako výchozí úložiště** | Pro **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen1** nebo **Azure Data Lake Storage Gen2** a pak se podívejte na článek [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) pokyny. |
     | **Externí metaúložiště**                      | Volitelně můžete zadat SQL databázi k uložení metadat Hive a Oozie přidružené ke clusteru. Pro **vybrat databázi SQL pro podregistr** vybrat databázi SQL a pak zadejte uživatelské jméno a heslo pro databázi. Tento postup opakujte pro Oozie metadata.<br><br>Některé aspekty při používání Azure SQL database u metaúložiště. <ul><li>Azure SQL database používá pro metastore musí umožňovat připojení k jiným službám Azure, včetně Azure HDInsight. Azure SQL database řídicí panel, na pravé straně klikněte na název serveru. Jedná se o server, na kterém běží SQL database instance. Po otevření zobrazení serveru, klikněte na tlačítko **konfigurovat**a potom **Azure Services**, klikněte na tlačítko **Ano**a potom klikněte na tlačítko **Uložit**.</li><li>Při vytvoření metastoru, nepoužívejte název databáze, které obsahuje pomlčky nebo pomlčky, protože to může způsobit selhání procesu vytváření clusteru.</li></ul> |

     > [!WARNING]  
     > Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, není podporováno.

     Vyberte **Další** přesunout na další stránku.


6. Z **4 aplikace (volitelné)**, vyberte všechny požadované aplikace.  Tyto aplikace mohou být vytvořeny společností Microsoft, nezávislými dodavateli softwaru (ISV) nebo vámi samotnými. Další informace najdete v tématu [aplikací HDInsight nainstalovat](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Vyberte **Další** přesunout na další stránku.


6. **5 velikost clusteru** zobrazí informace o uzlech, které se používají pro tento cluster. Nastavte počet pracovních uzlů, které potřebujete pro cluster. Také se zobrazí odhadované náklady na provoz clusteru.
   
    ![Cenovými úrovněmi uzlů](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "zadejte počet uzlů clusteru")
   
   > [!IMPORTANT]  
   > Pokud máte v úmyslu na více než 32 uzly pracovního procesu v clusteru vytváření nebo škálování clusteru po jeho vytvoření, musíte vybrat velikost hlavního uzlu s alespoň s 8 jádry a 14 GB paměti RAM.
   > 
   > Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Vyberte **Další** přesunout na další stránku.

8. Z **6 akcí skriptů**, si můžete přizpůsobit clusteru a nainstalujte vlastní komponenty.  Tuto možnost použijte, pokud chcete použít vlastní skript, který vlastní cluster, při vytváření clusteru. Další informace o akcí skriptů najdete v tématu [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

   Vyberte **Další** přesunout na další stránku.

9. Z **7 Souhrn**, zkontrolujte zadané informace, které jste zadali dříve a pak vyberte **vytvořit**.

     ![Cenovými úrovněmi uzlů](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "zadejte počet uzlů clusteru")
    
    > [!NOTE]  
    > Vytvoření clusteru nějakou dobu trvá, obvykle přibližně 20 minut. Monitorování **oznámení** můžete zkontrolovat na během procesu zřizování.

10. Po dokončení procesu vytváření vyberte **přejít k prostředku** z **nasazení bylo úspěšné** oznámení. V okně clusteru obsahuje následující informace.
    
    ![Rozhraní clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "vlastnosti clusteru")
    
    Použijte následující postup k pochopení ikony v horní části.
    
    * **Přehled** karta obsahuje všechny základní informace o clusteru, jako je například název, patří do skupiny prostředků, umístění, operační systém, adresa URL pro řídicí panel clusteru atd.
    * **Řídicí panel** vás přesměruje na portál Ambari přidružené ke clusteru.
    * **Secure Shell**: Informace potřebné pro přístup ke clusteru pomocí SSH.
    * **Škálování clusteru** umožňuje zvýšit počet pracovních uzlů, které jsou přidružené ke clusteru.
      * **Odstranit**: Odstraní HDInsight cluster.
    

## <a name="customize-clusters"></a>Přizpůsobení clusterů
* Zobrazit [HDInsight přizpůsobení clusterů pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Zobrazit [HDInsight přizpůsobit clustery pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Odstranění clusteru
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Další postup
Teď, když úspěšně vytvoříte HDInsight cluster, použijte následující postup, jak pracovat s vaším clusterem:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusterů
* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [UseApache Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBase
* [Začínáme s Apache HBase v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikace Java pro Apache HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clustery Apache Storm
* [Vývoj topologie Java pro Apache Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent v Pythonu v Apache Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií pomocí Apache Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clustery Apache Spark
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark pomocí Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Použití Sparku v HDInsight k předpovědím výsledků kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)

