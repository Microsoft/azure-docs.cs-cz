---
title: Vytvořte clustery systému Apache Hadoop pomocí webového prohlížeče, Azure HDInsight
description: Zjistěte, jak vytvářet clustery Apache Hadoop, Apache HBase, Apache Storm nebo Apache Spark v Linuxu pro HDInsight pomocí webového prohlížeče a na portálu Azure preview.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/28/2018
ms.author: hrasheed
ms.openlocfilehash: 13d053c14b083390bfdd28fdad616caf13e7e4e0
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911553"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Vytvoření clusterů se systémem Linux v HDInsight pomocí webu Azure portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Na webu Azure portal je nástroj pro správu webových služeb a prostředků, které jsou hostované v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvářet clustery založené na Linuxu Azure HDInsight pomocí portálu.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Předplatné Azure**. Zobrazit [jak získat Azure bezplatnou zkušební verzi pro testování Hadoop v HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Moderní webový prohlížeč**. Na webu Azure portal používá HTML5 a JavaScript. Nemusí fungovat správně v starší webové prohlížeče.

## <a name="create-clusters"></a>Vytváření clusterů
Na webu Azure portal poskytuje většinu vlastností clusteru. Pomocí šablon Azure Resource Manageru můžete skrýt mnoho podrobností. Další informace najdete v tématu [vytvořit Apache Hadoop clusterů v HDInsight pomocí šablon Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **+ vytvořit prostředek**.

1.  V části **Azure Marketplace**vyberte **Analytics**.

1.  V části **doporučené**vyberte **HDInsight**.
   
    ![Vytvořit nový cluster na webu Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "vytvoření nového clusteru na portálu Azure portal")

1. Na **HDInsight** stránce **vlastní (velikost, nastavení, aplikace)**.

1. Vyberte **1 Základy**. Potom zadejte následující informace.

    ![Konfigurace základního nastavení](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "vytvoření nového clusteru na portálu Azure portal")

    * Zadejte **název clusteru**. Tento název musí být globálně jedinečný.

    * Z **předplatné** rozevíracího seznamu vyberte předplatné Azure, který se používá pro cluster.

    * Vyberte **typ clusteru**. Vyberte typ clusteru, který chcete vytvořit. Příklady jsou Hadoopu a Apache Sparku. **Operační systém** bude **Linux**. V dalším kroku vyberte verzi typu clusteru. Používejte výchozí verzi, pokud si nejste jisti, co k výběru. Další informace najdete v článku [Verze clusterů HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > HDInsight clustery se dělí na celou řadu typů. Odpovídají úlohy nebo technologie, která clusteru je vyladěný pro. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje několik typů. Příklady Storm a HBase na jednom clusteru.
        
    * Pro **uživatelské jméno přihlášení clusteru** a **přihlašovací heslo clusteru**, zadejte uživatelské jméno a heslo pro uživatele s rolí správce.

    * Zadejte **uživatelské jméno SSH**. Pokud chcete jako heslo správce, který jste zadali stejné heslo SSH, vyberte **použít stejné heslo jako pro přihlášení ke clusteru** zaškrtávací políčko. Pokud ne, zadejte buď **heslo** nebo **veřejný klíč** k ověřování uživatele SSH. Veřejný klíč je přístup, doporučujeme. Zvolte **vyberte** v dolní části uložíte přihlašovací údaje.
   
        Další informace najdete v tématu [připojení k HDInsight (Apache Hadoop) pomocí protokolu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * V části **Skupina prostředků** určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující.

    * Zadejte datové centrum **umístění** ve kterém se cluster vytvoří.

    * Vyberte **Další** přesunout na další stránku.

4. Z **2 zabezpečení a sítě**, clusteru můžete připojit k virtuální síti s použitím zadané rozevírací nabídky. Vyberte virtuální síť Azure a podsíť, pokud chcete umístit clusteru do virtuální sítě. Informace o používání HDInsight s virtuální sítí najdete v tématu [možnosti rozšíření HDInsight pomocí Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md). Tento článek obsahuje požadavky na konkrétní konfiguraci pro virtuální síť. 

    Pokud chcete použít **Enterprise Security Package**, postupujte podle těchto pokynů: [Konfigurace clusteru HDInsight pomocí Azure Active Directory Domain Services s balíčkem Enterprise Security Package](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Vyberte **Další** přesunout na další stránku.


5. Z **3 úložiště**, zda má být Azure Storage nebo Azure Data Lake Storage jako výchozí úložiště. Další informace najdete v tématu v následující tabulce.

     ![Nastavení úložiště](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "vytvoření nového clusteru na portálu Azure portal")

     | Storage                                      | Popis |
     |----------------------------------------------|-------------|
     | **Azure Storage BLOB jako výchozí úložiště**   | <ul><li>Pro **typ primárního úložiště**vyberte **služby Azure Storage**. Pro **metodu výběru**, zvolte **mé odběry** Pokud chcete zadat účet úložiště, který je součástí vašeho předplatného Azure. Pak vyberte účet úložiště. V opačném případě vyberte **přístupový klíč**. Zadejte informace pro účet úložiště, který chcete vybrat z mimo vašeho předplatného Azure.</li><li>Pro **výchozí kontejner**, zvolte výchozí název kontejneru navrhl na portálu nebo zadat vlastní.</li><li>Pokud Azure Blob storage je výchozí úložiště, můžete také vybrat **další účty úložiště** k určení dalších účtů úložiště pro přidružení ke clusteru. Pro **klíčů k úložišti Azure**vyberte **přidat klíč úložiště**. Můžete zadat účet úložiště z vašich předplatných Azure nebo z jiných předplatných. Zadejte přístupový klíč účtu úložiště.</li><li>Pokud úložiště objektů Blob je výchozí úložiště, můžete také vybrat **přístupu k Data Lake Storage** určit úložiště Azure Data Lake jako další úložiště. Další informace najdete v tématu [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage jako výchozí úložiště** | Pro **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen1** nebo **Azure Data Lake Storage Gen2**. Přečtěte si informace v článku [rychlý start: Nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) pokyny. |
     | **Externí metaúložiště**                      | Volitelně zadejte databázi SQL se uložit metadata Apache Hivu a Apache Oozie přidružené ke clusteru. Pro **vybrat databázi SQL pro podregistr**, vyberte databázi SQL. Pak zadejte uživatelské jméno a heslo pro databázi. Tento postup opakujte pro Oozie metadata.<br><br>Některé důležité informace o používání Azure SQL database u metaúložiště jsou následující: <ul><li>Azure SQL database, který se používá pro metaúložiště musí umožňovat připojení k jiným službám Azure, včetně Azure HDInsight. Na pravé straně řídicího panelu Azure SQL database vyberte název serveru. Tento server se spuštěnou v instanci databáze SQL. Když jste v zobrazení pro server, vyberte **konfigurovat**. Pak pro **Azure Services**vyberte **Ano**. Potom vyberte **Uložit**.</li><li>Při vytváření metastoru není název databáze s pomlčkami nebo spojovníky. Tyto znaky může způsobit selhání procesu vytváření clusteru.</li></ul> |

     > [!WARNING]  
     > Použití dalšího účtu úložiště v jiném umístění než HDInsight cluster se nepodporuje.

     Vyberte **Další** přesunout na další stránku.


6. Z **4 aplikace (volitelné)**, vyberte všechny aplikace, které chcete. Microsoft, nezávislí výrobci softwaru (ISV) nebo můžete vytvořit tyto aplikace. Další informace najdete v tématu [nainstalujte aplikace během vytváření clusteru](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Vyberte **Další** přesunout na další stránku.


6. **5 velikost clusteru** zobrazí informace o uzlech, které se používají pro tento cluster. Nastavte počet pracovních uzlů, které potřebujete pro cluster. Také se zobrazí odhadované náklady na provoz clusteru.
   
    ![Zadejte cenovými úrovněmi uzlů](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "zadejte počet uzlů clusteru")
   
   > [!IMPORTANT]  
   > Pokud máte v plánu na více než 32 uzlů pracovního procesu, vyberte velikost hlavního uzlu s minimálně 8 jádry a 14 GB paměti RAM. Uzly v clusteru vytváření nebo škálování clusteru po vytvoření plánu. 
   > 
   > Další informace o velikosti uzlů a související náklady, najdete v části [ceny za Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Vyberte **Další** přesunout na další stránku.

8. Z **6 akcí skriptů**, si můžete přizpůsobit clusteru a nainstalujte vlastní komponenty. Tato možnost funguje, pokud chcete použít vlastní skript, který vlastní cluster, při vytváření clusteru. Další informace o akcí skriptů najdete v tématu [HDInsight založených na Linuxu přizpůsobit clustery pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md).

   Vyberte **Další** přesunout na další stránku.

9. Z **7 Souhrn**, zkontrolujte zadané informace, které jste zadali dříve. Potom vyberte **Vytvořit**.

     ![Potvrzení konfigurací](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "zadejte počet uzlů clusteru")
    
    > [!NOTE]  
    > Vytvoření clusteru nějakou dobu trvá, obvykle přibližně 20 minut. Monitorování **oznámení** můžete zkontrolovat na během procesu zřizování.

10. Po dokončení procesu vytváření vyberte **přejít k prostředku** z **nasazení bylo úspěšné** oznámení. V okně clusteru obsahuje následující informace.
    
    ![Rozhraní clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "vlastnosti clusteru")
    
    Ikony v okně jsou vysvětleny následujícím způsobem:
    
    * **Přehled** karta obsahuje všechny základní informace o clusteru. Příklady jsou název, skupinu prostředků, do které patří do, umístění, operační systém a adresu URL pro řídicí panel clusteru.
    * **Řídicí panel** vás přesměruje na portál Ambari přidružené ke clusteru.
    * **Secure Shell** poskytuje informace potřebné pro přístup ke clusteru pomocí SSH.
    * S použitím **škálování clusteru**, můžete zvýšit počet pracovních uzlů, které jsou přidružené ke clusteru.
    * **Odstranit** odstraní HDInsight cluster.
    

## <a name="customize-clusters"></a>Přizpůsobení clusterů
* [Přizpůsobení clusterů HDInsight pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Odstranění clusteru
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Další postup
Úspěšně jste vytvořili HDInsight cluster. Teď zjistěte, jak pracovat s vaším clusterem.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusterů
* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](hadoop/hdinsight-use-pig.md)
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

