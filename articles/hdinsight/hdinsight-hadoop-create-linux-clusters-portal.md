---
title: Vytváření clusterů Apache Hadoop pomocí webového prohlížeče, Azure HDInsight
description: Naučte se vytvářet Apache Hadoop, Apache HBA, Apache Storm nebo Apache Spark clustery na platformě Linux pro HDInsight pomocí webového prohlížeče a portálu Azure Preview.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: hrasheed
ms.openlocfilehash: d4593cd3602c5324e9cffd5eff8564c09aceaf46
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406274"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Vytváření clusterů se systémem Linux v HDInsight pomocí Azure Portal
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal je webový nástroj pro správu služeb a prostředků hostovaných v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvořit clustery Azure HDInsight se systémem Linux pomocí portálu.

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Předplatné Azure**. Přečtěte si, [Jak získat bezplatnou zkušební verzi Azure pro testování Hadoop v HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Moderní webový prohlížeč**. Azure Portal používá HTML5 a JavaScript. Nemusí fungovat správně ve starších webových prohlížečích.

## <a name="create-clusters"></a>Vytváření clusterů
Azure Portal zpřístupňuje většinu vlastností clusteru. Pomocí šablon Azure Resource Manager můžete skrýt mnoho podrobností. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop ve službě HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. V nabídce vlevo vyberte **+ vytvořit prostředek**.

1.  V části **Azure Marketplace**vyberte **Analytics**.

1.  V části **Doporučené**vyberte **HDInsight**.
   
    ![Vytvoření nového clusteru v Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Vytvoření nového clusteru v Azure Portal")

1. Na stránce **HDInsight** vyberte **vlastní (velikost, nastavení, aplikace)** .

1. Vyberte **1 Základy**. Pak zadejte následující informace.

    ![Konfigurace základního nastavení](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Vytvoření nového clusteru v Azure Portal")

    * Zadejte **název clusteru**. Tento název musí být globálně jedinečný.

    * V rozevíracím seznamu **předplatné** vyberte předplatné Azure, které se používá pro cluster.

    * Vyberte **typ clusteru**. Pak vyberte typ clusteru, který chcete vytvořit. Příklady jsou Hadoop a Apache Spark. **Operační systém** bude **Linux**. V dalším kroku vyberte verzi typu clusteru. Pokud si nejste jisti, co si můžete vybrat, použijte výchozí verzi. Další informace najdete v článku [Verze clusterů HDInsight](hdinsight-component-versioning.md).
     
        > [!IMPORTANT]  
        > Clustery HDInsight přicházejí v nejrůznějších typech. Odpovídají zátěži nebo technologii, pro kterou je cluster laděn. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů. Příklady jsou zaplavení a HBA v jednom clusteru.
        
    * Pro uživatelské **jméno přihlášení clusteru** a **heslo přihlášení clusteru**zadejte uživatelské jméno a heslo pro uživatele s oprávněními správce.

    * Zadejte **uživatelské jméno SSH**. Pokud chcete stejné heslo SSH jako heslo správce, které jste zadali dříve, zaškrtněte políčko **použít stejné heslo jako přihlášení ke clusteru** . Pokud ne, zadejte buď **heslo** , nebo **veřejný klíč** k ověření uživatele SSH. Veřejný klíč je přístup, který doporučujeme. Kliknutím na **tlačítko vybrat** v dolní části uložte konfiguraci přihlašovacích údajů.
   
        Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * V části **Skupina prostředků** určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující.

    * Zadejte **umístění** datového centra, ve kterém se cluster vytvoří.

    * Kliknutím na tlačítko **Další** přejdete na další stránku.

4. Ze **2 zabezpečení a sítě**můžete cluster připojit k virtuální síti pomocí zadané rozevírací nabídky. Vyberte virtuální síť Azure a podsíť, pokud chcete cluster umístit do virtuální sítě. Informace o používání služby HDInsight s virtuální sítí najdete v tématu [Plánování nasazení virtuální sítě pro clustery Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Článek zahrnuje konkrétní požadavky na konfiguraci pro virtuální síť. 

    Pokud chcete použít **balíček zabezpečení podniku**, postupujte podle těchto pokynů: [Nakonfigurujte cluster HDInsight s balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Kliknutím na tlačítko **Další** přejdete na další stránku.


5. Ze **3 úložiště**určete, jestli chcete jako výchozí úložiště použít Azure Storage nebo Azure Data Lake Storage. Další informace najdete v následující tabulce.

     Nastavení ![úložiště](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Vytvoření nového clusteru v Azure Portal")

     | Storage                                      | Popis |
     |----------------------------------------------|-------------|
     | **Azure Storage objekty BLOB jako výchozí úložiště**   | <ul><li>Jako **typ primárního úložiště**vyberte **Azure Storage**. Pokud chcete zadat účet úložiště, který je součástí vašeho předplatného Azure, zvolte pro **metodu výběru**možnost **Moje odběry** . Pak vyberte účet úložiště. V opačném případě vyberte **přístupová klávesa**. Pak zadejte informace o účtu úložiště, který chcete vybrat mimo předplatné Azure.</li><li>V části **výchozí kontejner**zvolte výchozí název kontejneru navržený portálem nebo zadejte vlastní.</li><li>Pokud je úložiště objektů BLOB v Azure výchozí úložiště, můžete také vybrat **Další účty úložiště** a zadat další účty úložiště, které se mají přidružit ke clusteru. U **Azure Storage klíčů**vyberte **Přidat klíč úložiště**. Potom můžete poskytnout účet úložiště z předplatných Azure nebo z jiných předplatných. Zadejte přístupový klíč účtu úložiště.</li><li>Pokud je úložiště BLOB ve výchozím úložišti, můžete také vybrat **Data Lake Storage přístup** a zadat Azure Data Lake Storage jako další úložiště. Další informace najdete v tématu [rychlý Start: Nastavte clustery v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
     | **Azure Data Lake Storage jako výchozí úložiště** | Jako **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen1** nebo **Azure Data Lake Storage Gen2**. Pak se podívejte na článek [rychlý Start: Pokyny najdete v tématu instalace](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) clusterů ve službě HDInsight. |
     | **Externí metaúložiště**                      | Jako možnost Určete databázi SQL pro ukládání Apache Hive a metadat Apache Oozie přidružených ke clusteru. Pro **možnost vybrat databázi SQL pro podregistr**vyberte databázi SQL. Pak zadejte uživatelské jméno a heslo pro databázi. Opakujte tyto kroky pro metadata Oozie.<br><br>Mezi informace o používání služby Azure SQL Database pro metaúložiště patří následující: <ul><li>Azure SQL Database, která se používá pro metastore, musí umožňovat připojení k ostatním službám Azure, včetně Azure HDInsight. Na pravé straně řídicího panelu Azure SQL Database vyberte název serveru. Tento server je ten, na kterém běží instance databáze SQL. Až budete v zobrazení serveru, vyberte **Konfigurovat**. Potom pro **služby Azure**vyberte **Ano**. Potom vyberte **Uložit**.</li><li>Při vytváření metastore Nejmenujte databázi s pomlčkami nebo pomlčkami. Tyto znaky můžou způsobit selhání procesu vytváření clusteru.</li></ul> |

     > [!WARNING]  
     > Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, se nepodporuje.

     Kliknutím na tlačítko **Další** přejdete na další stránku.


6. Ze **4 aplikací (volitelné)** vyberte libovolné aplikace, které chcete. Společnost Microsoft, nezávislé výrobce softwaru (ISV) nebo tyto aplikace můžete vyvíjet. Další informace najdete v tématu [instalace aplikací během vytváření clusteru](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Kliknutím na tlačítko **Další** přejdete na další stránku.


6. **5 velikost clusteru** zobrazuje informace o uzlech, které se používají pro tento cluster. Nastavte počet pracovních uzlů, které pro cluster potřebujete. Zobrazí se také odhadované náklady na spuštění clusteru.
   
    ![Zadat cenové úrovně uzlu](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Zadat počet uzlů clusteru")
   
   > [!IMPORTANT]  
   > Pokud plánujete více než 32 pracovních uzlů, vyberte velikost hlavního uzlu s alespoň osmi jádry a 14 GB paměti RAM. Naplánujte uzly buď při vytváření clusteru, nebo pomocí škálování clusteru po jeho vytvoření. 
   > 
   > Další informace o velikostech uzlů a přidružených nákladech najdete v tématu [ceny služby Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
   
    Kliknutím na tlačítko **Další** přejdete na další stránku.

8. Z **6 akcí skriptu**můžete přizpůsobit cluster pro instalaci vlastních komponent. Tato možnost funguje, pokud chcete použít vlastní skript k přizpůsobení clusteru, protože se vytváří cluster. Další informace o akcích skriptu najdete v tématu [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

   Kliknutím na tlačítko **Další** přejdete na další stránku.

9. Z **7 souhrnu**ověřte informace, které jste zadali dříve. Potom vyberte **Vytvořit**.

     ![Potvrdit konfigurace](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Zadat počet uzlů clusteru")
    
    > [!NOTE]  
    > Vytvoření clusteru nějakou dobu trvá, obvykle přibližně 20 minut. Sledujte **oznámení** pro kontrolu procesu zřizování.

10. Po dokončení procesu vytváření vyberte **Přejít k prostředku** z oznámení o **úspěšném nasazení** . V okně clusteru jsou uvedeny následující informace.
    
    ![Rozhraní clusteru](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Vlastnosti clusteru")
    
    Ikony v okně jsou vysvětleny takto:
    
    * Karta **Přehled** poskytuje všechny základní informace o clusteru. Příklady jsou název, skupina prostředků, do které patří, umístění, operační systém a adresa URL řídicího panelu clusteru.
    * **Řídicí panel** vás přesměruje na portál Ambari přidružený ke clusteru.
    * **Secure Shell** poskytuje informace potřebné pro přístup ke clusteru pomocí SSH.
    * Pomocí **clusteru škálování**můžete zvýšit počet pracovních uzlů přidružených ke clusteru.
    * **Odstranění** odstraní cluster HDInsight.
    

## <a name="customize-clusters"></a>Přizpůsobení clusterů
* [Přizpůsobení clusterů HDInsight pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Odstranění clusteru
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Další postup
Úspěšně jste vytvořili cluster HDInsight. Nyní se naučíte pracovat s clusterem.

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop
* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBA
* [Začínáme s Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací Java pro Apache HBA v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clustery Apache Storm
* [Vývoj topologií v jazyce Java pro Apache Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití součástí Pythonu v Apache Storm ve službě HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií pomocí Apache Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Clustery Apache Spark
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na Apache Sparkm clusteru pomocí Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Sparku ve službě HDInsight pomocí nástrojů BI](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: Předpověď výsledků kontroly potravin pomocí Sparku v HDInsight](spark/apache-spark-machine-learning-mllib-ipython.md)

