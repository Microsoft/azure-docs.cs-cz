---
title: Vytváření clusterů Azure HDInsight pomocí portálu Data Lake Storage Gen1 –
description: Použití portálu Azure k vytváření a používání clusterů HDInsight s Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265568"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Vytváření clusterů HDInsight pomocí Azure Data Lake Storage Gen1 pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Použití portálu Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použít Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Zjistěte, jak pomocí portálu Azure portal vytvořit cluster HDInsight s účtem Azure Data Lake Storage Gen1 jako výchozího úložiště nebo dalšího úložiště. I když je další úložiště volitelné pro cluster HDInsight, doporučujeme ukládat obchodní data do dalších účtů úložiště.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste splnili následující požadavky:

* **Předplatné Azure**. Přejděte na [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Gen1 úložiště datového jezera**. Postupujte podle pokynů z [Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](data-lake-store-get-started-portal.md). Je také nutné vytvořit kořenovou složku v účtu.  V tomto článku se používá kořenová složka s názvem __/clusters.__
* **Zaregistrovaný objekt služby Azure Active Directory**. Tento návod obsahuje pokyny, jak vytvořit instanční objekt ve službě Azure Active Directory (Azure AD). Chcete-li však vytvořit instanční objekt, musíte být správcem služby Azure AD. Pokud jste správce, můžete tento předpoklad přeskočit a pokračovat.

>[!NOTE]
>Instanční objekt můžete vytvořit pouze v případě, že jste správce Azure AD. Váš správce Služby Azure AD musí vytvořit instanční objekt, než budete moct vytvořit cluster HDInsight s Date Lake Storage Gen1. Instanční objekt musí být také vytvořen s certifikátem, jak je popsáno v [na vytvořit instanční objekt s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

V této části vytvoříte cluster HDInsight s účty Data Lake Storage Gen1 jako výchozí nebo další úložiště. Tento článek se zaměřuje pouze na část konfigurace účty Data Lake Storage Gen1. Obecné informace a postupy pro vytváření clusteru naleznete [v tématu Vytvoření clusterů Hadoop v hdinsightu](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Vytvoření clusteru s úložištěm Datového jezera Gen1 jako výchozím úložištěm

Vytvoření clusteru HDInsight s účtem Data Lake Storage Gen1 jako výchozího účtu úložiště:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Obecné informace o vytváření [clusterů](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) HDInsight navazují na tlačítko Vytvořit clustery.
3. V okně **Storage** v části **Typ primárního úložiště**vyberte Azure Data Lake Storage **Gen1**a zadejte následující informace:

    ![Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Přidání instančního objektu do clusteru HDInsight")

    * **Vyberte účet Úložiště datového jezera**: Vyberte existující účet Data Lake Storage Gen1. Je vyžadován existující účet Data Lake Storage Gen1.  Viz [Požadavky](#prerequisites).
    * **Kořenová cesta**: Zadejte cestu, do které mají být uloženy soubory specifické pro cluster. Na snímku obrazovky je __/clusters/myhdiadlcluster/__, ve kterém musí existovat složka __/clusters__ a portál vytvoří složku *myhdicluster.*  *Myhdicluster* je název clusteru.
    * **Přístup k úložišti datových jezer:** Konfigurace přístupu mezi účtem Data Lake Storage Gen1 a clusterem HDInsight. Pokyny naleznete v [tématu Konfigurace přístupu k úložišti datového jezera Gen1](#configure-data-lake-storage-gen1-access).
    * **Další účty úložiště**: Přidejte účty úložiště Azure jako další účty úložiště pro cluster. Chcete-li přidat další účty Data Lake Storage Gen1, můžete je udělit oprávnění clusteru k datům ve více účtech Data Lake Storage Gen1 při konfiguraci účtu Data Lake Storage Gen1 jako primárního typu úložiště. Viz [Konfigurace přístupu data lake storage gen1](#configure-data-lake-storage-gen1-access).

4. V **aplikaci Data Lake Store access**klikněte na **Select**a the continue with cluster creation as described in [Create Hadoop clusters in HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Vytvoření clusteru s datovým úložištěm jezera Gen1 jako dalším úložištěm

Následující pokyny vytvořit hdinsight cluster s účtem úložiště Azure jako výchozí úložiště a data Lake Storage Gen1 účet jako další úložiště.

Vytvoření clusteru HDInsight s účtem Data Lake Storage Gen1 jako dalšího účtu úložiště:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Obecné informace o vytváření [clusterů](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) HDInsight navazují na tlačítko Vytvořit clustery.
3. V okně **Úložiště** v části **Typ primárního úložiště**vyberte Azure **Storage**a zadejte následující informace:

    ![Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Přidání instančního objektu do clusteru HDInsight")

    * **Metoda výběru** – Chcete-li zadat účet úložiště, který je součástí vašeho předplatného Azure, vyberte **Moje předplatná**a pak vyberte účet úložiště. Pokud chcete zadat účet úložiště, který je mimo vaše předplatné Azure, vyberte **přístupový klíč**a zadejte informace pro účet vnějšího úložiště.

    * **Výchozí kontejner** – Použijte výchozí hodnotu nebo zadejte svůj vlastní název.
    * **Další účty úložiště** – přidejte další účty úložiště Azure jako další úložiště.
    * **Přístup k úložišti datových jezer** – konfigurace přístupu mezi účtem Data Lake Storage Gen1 a clusterem HDInsight. Pokyny naleznete v [tématu Konfigurace přístupu k úložišti datového jezera Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurace přístupu k úložišti datového jezera Gen1

V této části nakonfigurujete přístup Data Lake Storage Gen1 z clusterů HDInsight pomocí hlavního objektu služby Azure Active Directory.

### <a name="specify-a-service-principal"></a>Určení instančního objektu

Na webu Azure Portal můžete buď použít existující instanční objekt nebo vytvořit nový.

Vytvoření instančního objektu z webu Azure Portal:

1. V yberte **Přístup k úložišti datového jezera** z okna Úložiště.
1. V **okně přístupu Data Lake Storage Gen1** vyberte **Vytvořit nový**.
1. Vyberte **Instanční objekt**a podle pokynů vytvořte instanční objekt.
1. Stáhněte si certifikát, pokud se rozhodnete jej v budoucnu znovu použít. Stažení certifikátu je užitečné, pokud chcete použít stejný instanční objekt při vytváření dalších clusterů HDInsight.

    ![Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Přidání instančního objektu do clusteru HDInsight")

1. Chcete-li nakonfigurovat přístup ke složce, vyberte **možnost Přístup.**  Viz [Konfigurace oprávnění k souborům](#configure-file-permissions).

Použití existujícího instančního objektu z webu Azure:

1. Vyberte **přístup k úložišti datového jezera**.
1. V **okně přístupu Data Lake Storage Gen1** vyberte **Použít existující**.
1. Vyberte **Instanční objekt**a pak vyberte instanční objekt.
1. Nahrajte certifikát (soubor.pfx), který je přidružen k vybranému instančnímu objektu služby, a zadejte heslo certifikátu.

    ![Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Přidání instančního objektu do clusteru HDInsight")

1. Chcete-li nakonfigurovat přístup ke složce, vyberte **možnost Přístup.**  Viz [Konfigurace oprávnění k souborům](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurace oprávnění k souborům

Konfigurace se liší v závislosti na tom, zda se účet používá jako výchozí úložiště nebo další účet úložiště:

* Používá se jako výchozí úložiště

  * oprávnění na kořenové úrovni účtu Data Lake Storage Gen1
  * oprávnění na kořenové úrovni úložiště clusteru HDInsight. Například složka __/clusters__ použitá dříve v kurzu.

* Použití jako další úložiště

  * Oprávnění ke složkám, ve kterých potřebujete přístup k souborům.

Přiřazení oprávnění na kořenové úrovni účtu Data Lake Storage Gen1:

1. V **okně přístupu Data Lake Storage Gen1** vyberte **Access**. Otevře se okno **Vybrat oprávnění k souboru.** Obsahuje seznam všech účtů Data Lake Storage Gen1 ve vašem předplatném.
1. Najeďte myší (neklikejte) myší na název účtu Data Lake Storage Gen1, aby bylo zaškrtávací políčko viditelné, a zaškrtněte políčko.

    ![Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Přidání instančního objektu do clusteru HDInsight")

   Ve výchozím nastavení jsou vybrány všechny __možnosti ČTENÍ__, __ZÁPIS__A __SPUŠTĚNÍ.__

1. V dolní části stránky klikněte na **Vybrat.**
1. Chcete-li přiřadit oprávnění, vyberte **spustit.**
1. Vyberte **Done** (Hotovo).

Postup přiřazení oprávnění na úrovni kořenového adresáře clusteru HDInsight:

1. V **okně přístupu Data Lake Storage Gen1** vyberte **Access**. Otevře se okno **Vybrat oprávnění k souboru.** Obsahuje seznam všech účtů Data Lake Storage Gen1 ve vašem předplatném.
1. V okně **Vybrat oprávnění k souboru** vyberte název účtu Data Lake Storage Gen1, chcete-li zobrazit jeho obsah.
1. Vyberte kořen úložiště clusteru HDInsight zaškrtnutím políčka nalevo od složky. Podle předchozího snímku obrazovky je kořenúložiště clusteru __/clusters__ složka, kterou jste zadali při výběru Data Lake Storage Gen1 jako výchozí úložiště.
1. Nastavte oprávnění pro složku.  Ve výchozím nastavení jsou vybrány všechny čtení, zápis a spuštění.
1. V dolní části stránky klikněte na **Vybrat.**
1. Vyberte **Run** (Spustit).
1. Vyberte **Done** (Hotovo).

Pokud používáte Data Lake Storage Gen1 jako další úložiště, musíte přiřadit oprávnění pouze pro složky, které chcete získat přístup z clusteru HDInsight. Například na následujícím snímku obrazovky poskytujete přístup pouze ke složce **mynewfolder** v účtu Data Lake Storage Gen1.

![Přiřazení oprávnění hlavního povinného servisu ke clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Přiřazení oprávnění hlavního povinného servisu ke clusteru HDInsight")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Ověření nastavení clusteru

Po dokončení instalace clusteru ověřte výsledky v okně clusteru jedním nebo oběma následujícími kroky:

* Chcete-li ověřit, zda je přidruženým úložištěm pro cluster, účet Gene1 úložiště datového jezera, který jste zadali, vyberte v levém podokně **účty úložiště.**

    ![Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Přidání instančního objektu do clusteru HDInsight")

* Chcete-li ověřit, zda je instanční objekt správně přidružen k clusteru HDInsight, vlevém v levém podokně vyberte **přístup K Úložišti datových jezer Gen1.**

    ![Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Přidání instančního objektu do clusteru HDInsight")

## <a name="examples"></a>Příklady

Po nastavení clusteru s Data Lake Storage Gen1 jako úložiště, podívejte se na tyto příklady, jak pomocí clusteru HDInsight analyzovat data uložená v Datové hojedě Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Spuštění dotazu Hive proti datům v účtu Data Lake Storage Gen1 (jako primární úložiště)

Chcete-li spustit dotaz Hive, použijte rozhraní zobrazení Hive na portálu Ambari. Pokyny k používání zobrazení Ambari Hive najdete [v tématu Použití zobrazení Hive s hadoopem v hdinsightu](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Při práci s daty v účtu Gen1 úložiště datového jezera, existuje několik řetězců změnit.

Pokud používáte například cluster, který jste vytvořili s Data Lake Storage Gen1 jako primární úložiště, cesta k datům je: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.net/path/to/file*. Dotaz Hive pro vytvoření tabulky z ukázkových dat, která jsou uložená v účtu Data Lake Storage Gen1, vypadá jako následující příkaz:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Popisy:

* `adl://hdiadlsg1storage.azuredatalakestore.net/`je kořenový adresář účtu Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster`je kořenová složka dat clusteru, která jste zadali při vytváření clusteru.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/`je umístění ukázkového souboru, který jste použili v dotazu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Spuštění dotazu Hive proti datům v účtu Data Lake Storage Gen1 (jako další úložiště)

Pokud cluster, který jste vytvořili, používá úložiště objektů Blob jako výchozí úložiště, ukázková data nejsou obsažena v účtu Data Lake Storage Gen1, který se používá jako další úložiště. V takovém případě nejprve přenést data z úložiště objektů blob do účtu Data Lake Storage Gen1 a potom spusťte dotazy, jak je znázorněno v předchozím příkladu.

Informace o tom, jak zkopírovat data z úložiště objektů Blob do účtu Data Lake Storage Gen1, najdete v následujících článcích:

* [Kopírování dat mezi objekty BLOB úložiště Azure a úložištěm datových jezer Gen1 pomocí distcp](data-lake-store-copy-data-wasb-distcp.md)
* [Kopírování dat z objektů BLOB úložiště Azure do gen1 úložiště datových jezer pomocí AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Použití data lake storage gen1 s clusterem Spark

Pomocí clusteru Spark můžete spouštět úlohy Spark na datech, která jsou uložená v účtu Data Lake Storage Gen1. Další informace najdete [v tématu Použití clusteru HDInsight Spark k analýze dat v aplikaci Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Použití data lake storage gen1 v topologii Storm

Účet Data Lake Storage Gen1 můžete použít k zápisu dat z topologie Storm. Pokyny k dosažení tohoto scénáře najdete [v tématu Použití Azure Data Lake Storage Gen1 s Apache Storm s HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Viz také

* [Použití data lake storage gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: Vytvoření clusteru HDInsight pro použití data lake storage gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
