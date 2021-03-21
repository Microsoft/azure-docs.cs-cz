---
title: Vytvoření Azure HDInsight – Azure Data Lake Storage Gen2 – portál
description: Naučte se používat Azure Data Lake Storage Gen2 s clustery Azure HDInsight pomocí portálu.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 09/17/2020
ms.openlocfilehash: d54c005ffb17571be172b5716723febb742253a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945398"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-the-azure-portal"></a>Vytvoření clusteru s Data Lake Storage Gen2 pomocí Azure Portal

Azure Portal je webový nástroj pro správu služeb a prostředků hostovaných v cloudu Microsoft Azure. V tomto článku se dozvíte, jak vytvořit clustery Azure HDInsight se systémem Linux pomocí portálu. Další podrobnosti jsou k dispozici v [vytváření clusterů HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Pokud chcete vytvořit cluster HDInsight, který používá Data Lake Storage Gen2 pro úložiště, postupujte podle těchto kroků a nakonfigurujte účet úložiště, který má hierarchický obor názvů.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte spravovanou identitu přiřazenou uživatelem, pokud ji ještě nemáte.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V levém horním rohu klikněte na **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **přiřazený uživatel** a klikněte na položku **spravovaná identita přiřazená uživateli**.
1. Klikněte na **Vytvořit**.
1. Zadejte název spravované identity, vyberte správné předplatné, skupinu prostředků a umístění.
1. Klikněte na **Vytvořit**.

Další informace o tom, jak spravované identity fungují ve službě Azure HDInsight, najdete v tématu [spravované identity ve službě Azure HDInsight](hdinsight-managed-identities.md).

![Vytvoření spravované identity přiřazené uživatelem](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

## <a name="create-a-storage-account-to-use-with-data-lake-storage-gen2"></a>Vytvoření účtu úložiště pro použití s Data Lake Storage Gen2

Vytvořte účet úložiště pro použití s Azure Data Lake Storage Gen2.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V levém horním rohu klikněte na **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **Storage** a klikněte na **účet úložiště**.
1. Klikněte na **Vytvořit**.
1. Na obrazovce **vytvořit účet úložiště** :
    1. Vyberte správné předplatné a skupinu prostředků.
    1. Do Data Lake Storage Gen2 zadejte název svého účtu úložiště.
    1. Klikněte na kartu **Upřesnit** .
    1. V části **Data Lake Storage Gen2** klikněte na **povoleno** vedle **hierarchického oboru názvů** .
    1. Klikněte na **Zkontrolovat a vytvořit**.
    1. Klikněte na **Vytvořit**.

Další informace o dalších možnostech při vytváření účtu úložiště najdete v tématu [rychlý Start: vytvoření účtu úložiště pro Azure Data Lake Storage Gen2](../storage/blobs/create-data-lake-storage-account.md).

![Snímek obrazovky, který ukazuje vytvoření účtu úložiště v Azure Portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

## <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2"></a>Nastavte oprávnění pro spravovanou identitu na Data Lake Storage Gen2

Přiřaďte spravovanou identitu k roli **vlastníka dat objektů BLOB úložiště** v účtu úložiště.

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.
1. Vyberte svůj účet úložiště a pak vyberte **řízení přístupu (IAM)** a zobrazte nastavení řízení přístupu pro tento účet. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.

    ![Snímek obrazovky s nastavením řízení přístupu k úložišti](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Kliknutím na tlačítko **+ Přidat přiřazení role** přidejte novou roli.
1. V okně **Přidat přiřazení role** vyberte roli **vlastníka dat objektu BLOB úložiště** . Pak vyberte předplatné, které má spravovanou identitu a účet úložiště. V dalším kroku vyhledejte uživatelem přiřazenou spravovanou identitu, kterou jste předtím vytvořili. Nakonec vyberte spravovanou identitu a zobrazí se v seznamu **Vybrané členy**.

    ![Snímek obrazovky, který ukazuje, jak přiřadit roli Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Vyberte **Uložit**. Uživatelem přiřazená identita, kterou jste vybrali, je teď uvedená v seznamu vybraná role.
1. Po dokončení tohoto počátečního nastavení můžete cluster vytvořit prostřednictvím portálu. Cluster musí být ve stejné oblasti Azure jako účet úložiště. Na kartě **úložiště** v nabídce vytvoření clusteru vyberte následující možnosti:

    * Jako **typ primárního úložiště** vyberte **Azure Data Lake Storage Gen2**.
    * V části **primární účet úložiště** vyhledejte a vyberte nově vytvořený účet úložiště s úložištěm Data Lake Storage Gen2.

    * V části **Identita** vyberte nově vytvořenou spravovanou identitu přiřazenou uživatelem.

        ![Nastavení úložiště pro použití Data Lake Storage Gen2 se službou Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Pokud chcete do Data Lake Storage Gen2 přidat sekundární účet úložiště, na úrovni účtu úložiště jednoduše přiřaďte spravovanou identitu vytvořenou dříve k novému Data Lake Storage Gen2, který chcete přidat. Doporučujeme přidat sekundární účet úložiště s Data Lake Storage Gen2 přes okno další účty úložiště ve službě HDInsight se nepodporuje.
    > * V účtu služby Azure Blob Storage, který používá HDInsight, můžete povolit RA-GRS nebo RA-ZRS. Vytvoření clusteru se sekundárním koncovým bodem RA-GRS nebo RA-ZRS se ale nepodporuje.

## <a name="delete-the-cluster"></a>Odstranění clusteru

Další informace najdete v tématu [odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

Úspěšně jste vytvořili cluster HDInsight. Nyní se naučíte pracovat s clusterem.

### <a name="apache-spark-clusters"></a>Clustery Apache Spark

* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md)
* [Vytvoření samostatné aplikace pomocí Scala](spark/apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](spark/apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBA

* [Začínáme s Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací Java pro Apache HBA v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)
