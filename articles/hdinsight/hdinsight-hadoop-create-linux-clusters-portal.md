---
title: Vytváření clusterů Apache Hadoop pomocí webového prohlížeče, Azure HDInsight
description: Naučte se vytvářet Apache Hadoop, Apache HBA, Apache Storm nebo Apache Spark clustery na platformě Linux pro HDInsight pomocí webového prohlížeče a Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215926"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Vytváření clusterů se systémem Linux v HDInsight pomocí Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure Portal je webový nástroj pro správu služeb a prostředků hostovaných v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvořit clustery Azure HDInsight se systémem Linux pomocí portálu.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Přečtěte si, [Jak získat bezplatnou zkušební verzi Azure pro testování Hadoop v HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Moderní webový prohlížeč**. Azure Portal používá HTML5 a JavaScript. Nemusí fungovat správně ve starších webových prohlížečích.

## <a name="create-clusters"></a>Vytváření clusterů

Azure Portal zpřístupňuje většinu vlastností clusteru. Pomocí šablon Azure Resource Manager můžete skrýt mnoho podrobností. Další informace najdete v tématu [Vytvoření clusterů Apache Hadoop ve službě HDInsight pomocí šablon Správce prostředků](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).

1. V nabídce vlevo přejděte na **+ vytvořit prostředek** >  **Analytics** > **Azure HDInsight**.

    ![Vytvoření nového clusteru v Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Vytvoření nového clusteru v Azure Portal")

1. Na stránce **vytvořit cluster HDInsight** vyberte **Přejít na klasické prostředí pro vytváření**.

    ![Přejít na klasické prostředí pro vytváření](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Na stránce **HDInsight** vyberte **vlastní (velikost, nastavení, aplikace)** .

1. Vyberte **1 Základy**. Pak zadejte následující informace:

    |Vlastnost |Popis |
    |---|---|
    |Název clusteru|Tento název musí být globálně jedinečný.|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Typ clusteru|Vyberte typ clusteru, který chcete vytvořit. Příklady jsou Hadoop a Apache Spark. **Operační systém** bude **Linux**. V dalším kroku vyberte verzi typu clusteru. Pokud si nejste jisti, co si můžete vybrat, použijte výchozí verzi. Další informace najdete v článku [Verze clusterů HDInsight](hdinsight-component-versioning.md).|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí nastavení je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo.|
    |Uživatelské jméno Secure Shell (SSH)|Výchozí hodnota je **sshuser**. Pokud chcete stejné heslo SSH jako heslo správce, které jste zadali dříve, zaškrtněte políčko **použít heslo přihlášení clusteru pro SSH** . Pokud ne, zadejte buď **heslo** , nebo **veřejný klíč** k ověření uživatele SSH. Veřejný klíč je přístup, který doporučujeme. Kliknutím na **tlačítko vybrat** v dolní části uložte konfiguraci přihlašovacích údajů.  Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Skupina prostředků|Určete, jestli chcete vytvořit novou skupinu prostředků, nebo použít existující.|
    |Umístění|Zadejte datacentrum, ve kterém se cluster vytvoří.|

    ![Základy vytváření clusterů HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Vytvoření nového clusteru v Azure Portal")

    > [!IMPORTANT]  
    > Clustery HDInsight přicházejí v nejrůznějších typech. Odpovídají zátěži nebo technologii, pro kterou je cluster laděn. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů. Příklady jsou zaplavení a HBA v jednom clusteru.

    Kliknutím na tlačítko **Další** přejdete na další stránku.

1. Ze **2 zabezpečení a sítě**můžete cluster připojit k virtuální síti pomocí zadané rozevírací nabídky. Vyberte virtuální síť Azure a podsíť, pokud chcete cluster umístit do virtuální sítě. Informace o používání služby HDInsight s virtuální sítí najdete v tématu [Plánování nasazení virtuální sítě pro clustery Azure HDInsight](hdinsight-plan-virtual-network-deployment.md). Článek zahrnuje konkrétní požadavky na konfiguraci pro virtuální síť.

    Pokud chcete použít **balíček zabezpečení podniku**, postupujte podle těchto pokynů: [Konfigurace clusteru HDInsight pomocí balíček zabezpečení podniku pomocí Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Kliknutím na tlačítko **Další** přejdete na další stránku.

1. V **Nastavení účtu úložiště**ze **3 úložiště**určete, jestli chcete jako výchozí úložiště použít Azure Storage nebo Azure Data Lake Storage. Další informace najdete v následující tabulce.

    | Typ primárního úložiště | Popis |
    |------------------|-------------|
    | Azure Storage   |  * Pokud chcete zadat účet úložiště, který je součástí vašeho předplatného **Azure, zvolte**možnost **Moje odběry** . Pak vyberte účet úložiště. V opačném případě vyberte **přístupová klávesa**. Pak zadejte informace o účtu úložiště, který chcete vybrat mimo předplatné Azure.</br></br> * Pro **výchozí kontejner**zvolte výchozí název kontejneru navržený portálem nebo zadejte vlastní.</br></br> * Pokud je úložiště objektů BLOB v Azure výchozí úložiště, můžete také vybrat **Další účty úložiště** a zadat další účty úložiště, které se mají přidružit ke clusteru. U **Azure Storage klíčů**vyberte **Přidat klíč úložiště**. Potom můžete poskytnout účet úložiště z předplatných Azure nebo z jiných předplatných. Zadejte přístupový klíč účtu úložiště.</br></br> * Pokud je úložiště BLOB ve výchozím úložišti, můžete také vybrat **Data Lake Storage přístup** a zadat Azure Data Lake Storage jako další úložiště. Další informace najdete v tématu [rychlý Start: nastavení clusterů v HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).</li></ul> |
    | Azure Data Lake Storage | Vyberte **Azure Data Lake Storage Gen1** nebo **Azure Data Lake Storage Gen2**. Pokyny najdete [v článku rychlý Start: nastavení clusterů ve službě HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) . |

    **Nastavení metastore (volitelné)**

    Jako možnost Určete databázi SQL pro ukládání Apache Hive a metadat Apache Oozie přidružených ke clusteru. Pro **možnost vybrat databázi SQL pro podregistr**vyberte databázi SQL. Pak zadejte uživatelské jméno a heslo pro databázi. Opakujte tyto kroky pro metadata Oozie.

    Mezi informace o používání služby Azure SQL Database pro metaúložiště patří následující:
    * Azure SQL Database, která se používá pro metastore, musí umožňovat připojení k ostatním službám Azure, včetně Azure HDInsight. Na pravé straně řídicího panelu Azure SQL Database vyberte název serveru. Tento server je ten, na kterém běží instance databáze SQL. Až budete v zobrazení serveru, vyberte **Konfigurovat**. Potom pro **služby Azure**vyberte **Ano**. Potom vyberte **Uložit**.
    * Při vytváření metastore Nejmenujte databázi s pomlčkami nebo pomlčkami. Tyto znaky můžou způsobit selhání procesu vytváření clusteru.

    ![Vytvoření úložiště clusteru HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Vytvoření nového clusteru v Azure Portal")

    > [!WARNING]  
    > Použití dalšího účtu úložiště v jiném umístění, než je cluster HDInsight, se nepodporuje.

    Kliknutím na tlačítko **Další** přejdete na další stránku.

1. Ze **4 aplikací (volitelné)** vyberte libovolné aplikace, které chcete. Společnost Microsoft, nezávislé výrobce softwaru (ISV) nebo tyto aplikace můžete vyvíjet. Další informace najdete v tématu [instalace aplikací během vytváření clusteru](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation).

    Kliknutím na tlačítko **Další** přejdete na další stránku.

1. **5 velikost clusteru** zobrazuje informace o uzlech, které se používají pro tento cluster. Nastavte počet pracovních uzlů, které pro cluster potřebujete. Zobrazí se také odhadované náklady na spuštění clusteru.

    ![Vytváření uzlů clusteru HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Zadat počet uzlů clusteru")

   > [!IMPORTANT]  
   > Pokud plánujete více než 32 pracovních uzlů, vyberte velikost hlavního uzlu s alespoň osmi jádry a 14 GB paměti RAM. Naplánujte uzly buď při vytváření clusteru, nebo pomocí škálování clusteru po jeho vytvoření.
   >
   > Další informace o velikostech uzlů a přidružených nákladech najdete v tématu [ceny služby Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

    Kliknutím na tlačítko **Další** přejdete na další stránku.

1. Z **6 akcí skriptu**můžete přizpůsobit cluster pro instalaci vlastních komponent. Tato možnost funguje, pokud chcete použít vlastní skript k přizpůsobení clusteru, protože se vytváří cluster. Další informace o akcích skriptu najdete v tématu [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

   Kliknutím na tlačítko **Další** přejdete na další stránku.

1. Z **7 souhrnu**ověřte informace, které jste zadali dříve. Potom vyberte **Vytvořit**.

    ![Shrnutí clusteru pro vytvoření HDInsight](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Zadat počet uzlů clusteru")

    > [!NOTE]  
    > Vytvoření clusteru nějakou dobu trvá, obvykle přibližně 20 minut. Sledujte **oznámení** pro kontrolu procesu zřizování.

1. Po dokončení procesu vytváření vyberte **Přejít k prostředku** z oznámení o **úspěšném nasazení** . V okně clusteru jsou uvedeny následující informace.

    ![HDI Azure Portal clusteru – Přehled](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Vlastnosti clusteru")

    Některé ikony v okně jsou vysvětleny takto:

    |Vlastnost | Popis |
    |---|---|
    |Přehled|Poskytuje všechny základní informace o clusteru. Příklady jsou název, skupina prostředků, do které patří, umístění, operační systém a adresa URL řídicího panelu clusteru.|
    |Řídicí panely clusteru|Vás směruje na portál Ambari přidružený ke clusteru.|
    |SSH + přihlášení k clusteru|Poskytuje informace potřebné pro přístup ke clusteru pomocí SSH.|
    |Odstranění|Odstraní cluster HDInsight.|

## <a name="customize-clusters"></a>Přizpůsobení clusterů

* [Přizpůsobení clusterů HDInsight pomocí Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="next-steps"></a>Další kroky

Úspěšně jste vytvořili cluster HDInsight. Nyní se naučíte pracovat s clusterem.

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
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
* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku ve službě HDInsight s nástroji BI.](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)
