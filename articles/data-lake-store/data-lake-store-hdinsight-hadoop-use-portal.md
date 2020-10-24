---
title: Vytváření clusterů Azure HDInsight pomocí Data Lake Storage Gen1 – portál
description: Použití Azure Portal k vytvoření a použití clusterů HDInsight s Azure Data Lake Storage Gen1
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 41ba9d9e66fa1d7f622550bde68951573af4bb96
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92484980"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Vytváření clusterů HDInsight pomocí Azure Data Lake Storage Gen1 pomocí Azure Portal

> [!div class="op_single_selector"]
> * [Použití webu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Použití PowerShellu (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Použití PowerShellu (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použít Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Naučte se, jak pomocí Azure Portal vytvořit cluster HDInsight s Azure Data Lake Storage Gen1 jako výchozí úložiště nebo další úložiště. I když je pro cluster HDInsight volitelné další úložiště, doporučuje se ukládat obchodní data do dalších účtů úložiště.

## <a name="prerequisites"></a>Předpoklady

Než začnete, ujistěte se, že jste splnili následující požadavky:

* **Předplatné Azure**. Přejít na [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md). Musíte také vytvořit kořenovou složku na účtu.  V tomto článku se používá kořenová složka s názvem __/Clusters__ .
* **Objekt Azure Active Directory služby**. Tato příručka poskytuje pokyny k vytvoření instančního objektu ve službě Azure Active Directory (Azure AD). Pokud ale chcete vytvořit instanční objekt, musíte být správce Azure AD. Pokud jste správce, můžete tento požadavek vynechat a pokračovat.

>[!NOTE]
>Instanční objekt můžete vytvořit jenom v případě, že jste správce Azure AD. Správce Azure AD musí vytvořit instanční objekt, aby bylo možné vytvořit cluster HDInsight s Data Lake Storage Gen1. Také je nutné vytvořit instanční objekt s certifikátem, jak je popsáno v tématu [Vytvoření instančního objektu s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

V této části vytvoříte cluster HDInsight s Data Lake Storage Gen1 jako výchozí nebo další úložiště. Tento článek se zaměřuje jenom na součást konfigurace Data Lake Storage Gen1. Obecné informace o vytváření a postupech clusteru najdete v tématu [vytváření clusterů Hadoop ve službě HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Vytvoření clusteru s Data Lake Storage Gen1 jako výchozí úložiště

Pokud chcete vytvořit cluster HDInsight s Data Lake Storage Gen1 jako výchozí účet úložiště:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Pro obecné informace o vytváření clusterů HDInsight postupujte podle pokynů v části [Vytvoření clusterů](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) .
3. V okně **úložiště** v části **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen1**a potom zadejte následující informace:

    ![Nastavení účtu úložiště HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png)

    * **Vyberte data Lake Store účet**: Vyberte existující účet Data Lake Storage Gen1. Je vyžadován existující účet Data Lake Storage Gen1.  Viz [Požadavky](#prerequisites).
    * **Kořenová cesta**: zadejte cestu, kam se mají ukládat soubory specifické pro daný cluster. Na snímku obrazovky je __/Clusters/myhdiadlcluster/__, ve kterém musí existovat složka __/Clusters__ , a portál vytvoří složku *myhdicluster* .  *Myhdicluster* je název clusteru.
    * **Data Lake Store přístup**: Nakonfigurujte přístup mezi účtem Data Lake Storage Gen1 a clusterem HDInsight. Pokyny najdete v tématu [Konfigurace přístupu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * **Další účty úložiště**: přidejte účty Azure Storage jako další účty úložiště pro cluster. Pokud chcete přidat další účty Data Lake Storage Gen1, zadáte oprávnění clusteru pro data v dalších Data Lake Storage Gen1 účtech při konfiguraci Data Lake Storage Gen1 účtu jako primárního typu úložiště. Viz [Konfigurace přístupu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

4. Na **Data Lake Store přístup**klikněte na **Vybrat**a potom pokračujte v vytváření clusteru, jak je popsáno v tématu [vytváření clusterů Hadoop ve službě HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Vytvoření clusteru s Data Lake Storage Gen1 jako další úložiště

Následující pokyny Vytvoří cluster HDInsight s účtem služby Azure Blob Storage jako výchozí úložiště a účtem úložiště, který Data Lake Storage Gen1 jako další úložiště.

Pokud chcete vytvořit cluster HDInsight s Data Lake Storage Gen1 jako další účet úložiště:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Pro obecné informace o vytváření clusterů HDInsight postupujte podle pokynů v části [Vytvoření clusterů](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) .
3. V okně **úložiště** v části **typ primárního úložiště**vyberte **Azure Storage**a potom zadejte následující informace:

    ![Nastavení účtu úložiště HDInsight další úložiště](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png)

    * **Metoda výběru** – Pokud chcete zadat účet úložiště, který je součástí vašeho předplatného Azure, vyberte **Moje předplatné**a pak vyberte účet úložiště. Pokud chcete zadat účet úložiště, který se nachází mimo předplatné Azure, vyberte **přístupový klíč**a potom zadejte informace pro externí účet úložiště.

    * **Výchozí kontejner** – použijte buď výchozí hodnotu, nebo zadejte vlastní název.
    * **Další účty úložiště** – jako dodatečné úložiště přidejte další účty úložiště Azure.
    * **Přístup k Data Lake Store** – nakonfigurujte přístup mezi účtem Data Lake Storage Gen1 a clusterem HDInsight. Pokyny najdete v tématu [Konfigurace přístupu Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurace přístupu Data Lake Storage Gen1

V této části nakonfigurujete Data Lake Storage Gen1 přístup z clusterů HDInsight pomocí instančního objektu služby Azure Active Directory.

### <a name="specify-a-service-principal"></a>Zadat instanční objekt

Z Azure Portal můžete buď použít existující instanční objekt, nebo vytvořit nový.

Vytvoření instančního objektu z Azure Portal:
1. Viz téma [Vytvoření instančního objektu a certifikátů](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) pomocí Azure Active Directory.

Použití existujícího instančního objektu z Azure Portal:

1. Instanční objekt by měl mít oprávnění vlastníka k účtu úložiště. Viz [Nastavení oprávnění pro instanční objekt jako vlastníka účtu úložiště](#configure-serviceprincipal-permissions).
1. Vyberte **přístup data Lake Store**.
1. V okně **přístup k Data Lake Storage Gen1** vyberte **použít existující**.
1. Vyberte **instanční objekt**a potom vyberte instanční objekt.
1. Nahrajte certifikát (soubor. pfx), který je přidružený k vybranému instančnímu objektu, a pak zadejte heslo certifikátu.

[Přidání instančního objektu do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png)

1. Vyberte **přístup** ke konfiguraci přístupu ke složce.  Viz téma [Konfigurace oprávnění k souborům](#configure-file-permissions).

### <a name="set-up-permissions-for-the-service-principal-to-be-owner-on-the-storage-account"></a><a name="configure-serviceprincipal-permissions"></a>Nastavte oprávnění pro instanční objekt jako vlastníka účtu úložiště.
1. V okně Access Control (IAM) účtu úložiště klikněte na přidat přiřazení role. 
2. V okně Přidat přiřazení role vyberte role jako vlastník a vyberte hlavní název služby (SPN) a klikněte na Uložit.

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Konfigurace oprávnění k souborům

Konfigurace se liší v závislosti na tom, jestli se účet používá jako výchozí úložiště nebo další účet úložiště:

* Používá se jako výchozí úložiště.

  * oprávnění na kořenové úrovni účtu Data Lake Storage Gen1
  * oprávnění na kořenové úrovni úložiště clusteru HDInsight. Například složka __/Clusters__ použitá dříve v tomto kurzu.

* Použít jako další úložiště

  * Oprávnění ke složkám, kde potřebujete přístup k souboru.

Chcete-li přiřadit oprávnění k účtu úložiště s Data Lake Storage Gen1 na kořenové úrovni:

1. V okně **přístup k Data Lake Storage Gen1** vyberte **přístup**. Otevře se okno **vybrat oprávnění souboru** . Obsahuje seznam všech účtů úložiště v rámci vašeho předplatného.
1. Najeďte myší (neklepejte) na název účtu s Data Lake Storage Gen1, aby bylo políčko viditelné, a zaškrtněte políčko.

    ![Vybrat oprávnění souboru](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png)

   Ve výchozím nastavení jsou všechny vybrané, __číst__, __zapisovat__a __spouštět__ .

1. V dolní části stránky klikněte na **Vybrat** .
1. Kliknutím na tlačítko **Spustit** přiřadíte oprávnění.
1. Vyberte **Hotovo**.

Přiřazení oprávnění na úrovni kořenového adresáře clusteru HDInsight:

1. V okně **přístup k Data Lake Storage Gen1** vyberte **přístup**. Otevře se okno **vybrat oprávnění souboru** . Obsahuje seznam všech účtů úložiště s Data Lake Storage Gen1 v předplatném.
1. V okně **vybrat oprávnění k souborům** vyberte účet úložiště s názvem Data Lake Storage Gen1, abyste zobrazili jeho obsah.
1. Výběrem zaškrtávacího políčka na levé straně složky vyberte kořenový adresář úložiště clusteru HDInsight. Podle výše uvedeného snímku obrazovky je kořen úložiště clusteru __/Clusters__ složka, kterou jste zadali při výběru možnosti Data Lake Storage Gen1 jako výchozí úložiště.
1. Nastavte oprávnění pro složku.  Ve výchozím nastavení jsou všechny vybrané, číst, zapisovat a spouštět.
1. V dolní části stránky klikněte na **Vybrat** .
1. Vyberte **Run** (Spustit).
1. Vyberte **Hotovo**.

Pokud používáte Data Lake Storage Gen1 jako další úložiště, je nutné přiřadit oprávnění pouze pro složky, ke kterým chcete získat přístup z clusteru HDInsight. Například na následujícím snímku obrazovky poskytnete přístup pouze ke složce **mynewfolder** v účtu úložiště s Data Lake Storage Gen1.

![Přiřazení instančních oprávnění k clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png)

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Ověřit instalaci clusteru

Po dokončení instalace clusteru ověřte v okně clusteru výsledky pomocí jednoho nebo obou následujících kroků:

* Pokud chcete ověřit, jestli je přidruženým úložištěm pro cluster účet s Data Lake Storage Gen1, kterou jste zadali, vyberte **účty úložiště** v levém podokně.

    ![Ověřit přidružené úložiště](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png)

* Pokud chcete ověřit, jestli je instanční objekt správně přidružený k clusteru HDInsight, vyberte v levém podokně **Data Lake Storage Gen1 přístup** .

    ![Ověřit instanční objekt](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png)

## <a name="examples"></a>Příklady

Po nastavení clusteru s Data Lake Storage Gen1 jako úložiště se podívejte na tyto příklady použití clusteru HDInsight k analýze dat uložených v Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-primary-storage"></a>Spuštění dotazu na podregistr pro data v Data Lake Storage Gen1 (jako primární úložiště)

Pokud chcete spustit dotaz na podregistr, použijte rozhraní zobrazení podregistru na portálu Ambari. Pokyny k používání zobrazení podregistru Ambari najdete v tématu [použití zobrazení podregistru se systémem Hadoop ve službě HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Při práci s daty v Data Lake Storage Gen1 existuje několik řetězců ke změně.

Pokud používáte například cluster, který jste vytvořili pomocí Data Lake Storage Gen1 jako primární úložiště, cesta k datům je: *adl://<data_lake_storage_gen1_account_name>/azuredatalakestore.NET/path/to/File*. Dotaz na podregistr pro vytvoření tabulky z ukázkových dat uložených v Data Lake Storage Gen1 vypadá jako v následujícím příkazu:

```console
CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'
```

Označení

* `adl://hdiadlsg1storage.azuredatalakestore.net/` je kořenem účtu s Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` je kořenový adresář dat clusteru, který jste zadali při vytváření clusteru.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` je umístění ukázkového souboru, který jste použili v dotazu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-as-additional-storage"></a>Spuštění dotazu na podregistr pro data v Data Lake Storage Gen1 (jako další úložiště)

Pokud vytvořený cluster používá jako výchozí úložiště BLOB Storage, Tato ukázková data nejsou obsažená v účtu úložiště s Data Lake Storage Gen1, která se používá jako další úložiště. V takovém případě nejdříve přeneste data z úložiště objektů blob do účtu úložiště pomocí Data Lake Storage Gen1 a potom spusťte dotazy, jak je znázorněno v předchozím příkladu.

Informace o tom, jak kopírovat data z úložiště objektů blob do účtu úložiště pomocí Data Lake Storage Gen1, najdete v následujících článcích:

* [Použití Distcp ke kopírování dat mezi službou Azure Blob Storage a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Použití AdlCopy ke kopírování dat z úložiště objektů BLOB v Azure do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Použití Data Lake Storage Gen1 s clusterem Spark

Cluster Spark můžete použít ke spouštění úloh Sparku s daty uloženými v Data Lake Storage Gen1. Další informace najdete v tématu [použití clusteru HDInsight Spark k analýze dat v Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Použití Data Lake Storage Gen1 v topologii zaplavení

Pomocí účtu úložiště s Data Lake Storage Gen1 můžete zapisovat data z topologie s více podmnožinami. Pokyny k tomu, jak dosáhnout tohoto scénáře, najdete v tématu [použití Azure Data Lake Storage Gen1 s Apache Storm se službou HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Viz také

* [Použití Data Lake Storage Gen1 s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen1.md)
* [PowerShell: Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: /windows-hardware/drivers/devtest/makecert
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
