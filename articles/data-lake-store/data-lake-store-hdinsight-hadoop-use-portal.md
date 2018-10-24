---
title: Pomocí webu Azure portal k vytvoření clusterů Azure HDInsight s Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Pomocí webu Azure portal k vytváření a používání clusterů HDInsight s Azure Data Lake Storage Gen1
services: data-lake-store,hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: a8c45a83-a8e3-4227-8b02-1bc1e1de6767
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: b81916de0940aa56eda97df94527fa6adc208285
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955826"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Vytvoření clusterů HDInsight s Azure Data Lake Storage Gen1 pomocí webu Azure portal
> [!div class="op_single_selector"]
> * [Použití portálu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Pomocí prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Pomocí prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Použití Resource Manageru](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Zjistěte, jak pomocí webu Azure portal k vytvoření clusteru HDInsight pomocí účtu Azure Data Lake Storage Gen1 jako výchozím úložištěm nebo další úložiště. I když dodatečné úložiště je nepovinné pro HDInsight cluster, se doporučuje ukládat podniková data v účtech úložiště.

## <a name="prerequisites"></a>Požadavky
Než začnete tento kurz, ujistěte se, že splnění následujících požadavků:

* **Předplatné Azure**. Přejděte na [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1**. Postupujte podle pokynů v [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md). Také musíte vytvořit kořenové složce v účtu.  V tomto kurzu kořenovou složku s názvem __/clusterů__ se používá.
* **Instanční objekt Azure Active Directory**. Tento kurz obsahuje pokyny o tom, jak vytvořit instanční objekt v Azure Active Directory (Azure AD). K vytvoření instančního objektu, ale musí být správce Azure AD. Pokud jste správce, můžete tuto požadovanou součást přeskočit a pokračovat v tomto kurzu.

    >[!NOTE]
    >Služby můžete vytvořit instanční objekt, pouze v případě, že jste správce Azure AD. Správce Azure AD musíte vytvořit instanční objekt před vytvořením clusteru služby HDInsight s Data Lake Storage Gen1. Navíc musí být instanční objekt vytvořený s certifikátem, jak je popsáno v [vytvoření instančního objektu s certifikátem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

V této části vytvoříte HDInsight cluster s účty Data Lake Storage Gen1 jako výchozí nebo další úložiště. Tento článek se zaměřuje jenom na část konfigurace účtů Data Lake Storage Gen1.  Informace o vytvoření clusteru obecné a postupy najdete v tématu [vytváření clusterů Hadoop v HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Vytvoření clusteru s Data Lake Storage Gen1 jako výchozí úložiště

**K vytvoření clusteru HDInsight s účtem Data Lake Storage Gen1 jako výchozí účet úložiště**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Postupujte podle [vytvářet clustery](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) obecné informace o vytváření clusterů HDInsight.
3. Na **úložiště** okně v části **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen1**a potom zadejte následující informace:

    ![Přidání instančního objektu pro HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "přidat instančního objektu pro HDInsight cluster")

    - **Vyberte Data Lake Store účtu**: Vyberte existující účet Data Lake Storage Gen1. Existující účet Data Lake Storage Gen1 je povinný.  Viz [Požadavky](#prerequisites).
    - **Kořenová cesta**: Zadejte cestu, kam se mají ukládat soubory specifických pro cluster. Na snímku obrazovky, je __/clusterů/myhdiadlcluster/__, ve kterém __/clusterů__ složka musí existovat a portál vytvoří *myhdicluster* složky.  *Myhdicluster* je název clusteru.
    - **Přístup k data Lake Store**: nakonfigurovat přístup mezi účtem Data Lake Storage Gen1 a HDInsight cluster. Pokyny najdete v tématu [přístup konfigurovat Data Lake Storage Gen1](#configure-data-lake-store-access).
    - **Další účty úložiště**: účty úložiště Azure přidat jako další účty úložiště pro cluster. Chcete-li přidat další účty Data Lake Storage Gen1 se provádí tak, že udělíte oprávnění clusteru na datech z další účty Data Lake Storage Gen1 při konfiguraci účtu Data Lake Storage Gen1 jako typ primárního úložiště. Zobrazit [přístup konfigurovat Data Lake Storage Gen1](#configure-data-lake-store-access).

4. Na **přístupu k Data Lake Store**, klikněte na tlačítko **vyberte**a poté pokračovat ve vytváření clusteru, jak je popsáno v [vytváření clusterů Hadoop v HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Vytvoření clusteru s Data Lake Storage Gen1 jako další úložiště

Postupujte podle následujících pokynů vytvořte HDInsight cluster se účet úložiště Azure jako výchozího úložiště a účet Data Lake Storage Gen1 jako další úložiště.

**K vytvoření clusteru HDInsight s účtem Data Lake Storage Gen1 jako dalšího účtu úložiště**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Postupujte podle [vytvářet clustery](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) obecné informace o vytváření clusterů HDInsight.
3. Na **úložiště** okně v části **typ primárního úložiště**vyberte **služby Azure Storage**a potom zadejte následující informace:

    ![Přidání instančního objektu pro HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "přidat instančního objektu pro HDInsight cluster")

    - **Metoda výběru**: použijte jednu z následujících možností:

        * Chcete-li zadat účet úložiště, který je součástí vašeho předplatného Azure, vyberte **mé odběry**a pak vyberte účet úložiště.
        * Zadejte účet úložiště, který se nachází mimo vašeho předplatného Azure, vyberte **přístupový klíč**a potom zadejte informace pro externí účet úložiště.

    - **Výchozí kontejner**: použijte výchozí hodnotu nebo zadejte vlastní název.

    - Další účty úložiště: přidat účty Azure storage jako další úložiště.
    - Přístup k data Lake Store: nakonfigurovat přístup mezi účtem Data Lake Storage Gen1 a HDInsight cluster. Pokyny najdete v tématu [přístup konfigurovat Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Konfigurace přístupu v Data Lake Storage Gen1 

V této části nakonfigurujete Data Lake Storage Gen1 přístup z clusterů HDInsight pomocí instančního objektu služby Azure Active Directory. 

### <a name="specify-a-service-principal"></a>Specifikace instančního objektu

Na webu Azure Portal můžete použít existující instanční objekt služby nebo vytvořte novou.

**K vytvoření hlavního názvu služby z webu Azure portal**

1. Klikněte na tlačítko **přístupu k Data Lake Store** z okna úložiště.
2. Na **přístupu k Data Lake Storage Gen1** okna, klikněte na tlačítko **vytvořit nový**.
3. Klikněte na tlačítko **instanční objekt služby**a pak postupujte podle pokynů k vytvoření instančního objektu.
4. Stáhněte si certifikát, pokud se rozhodnete použít později znovu. Stáhnout certifikát je užitečné, pokud chcete použít stejný objekt zabezpečení služby, když vytvoříte další clustery HDInsight.

    ![Přidání instančního objektu pro HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "přidat instančního objektu pro HDInsight cluster")

4. Klikněte na tlačítko **přístup** nakonfigurovat přístup ke složce.  Zobrazit [nakonfigurovat oprávnění k souboru](#configure-file-permissions).


**Chcete-li použít existující instanční objekt služby z webu Azure portal**

1. Klikněte na tlačítko **přístupu k Data Lake Store**.
1. Na **přístupu k Data Lake Storage Gen1** okna, klikněte na tlačítko **použít existující**.
2. Klikněte na tlačítko **instanční objekt služby**a potom vyberte objekt služby. 
3. Nahrát na server certifikát (soubor .pfx), který je spojen s instančního objektu vybrané služby a pak zadejte heslo certifikátu.

    ![Přidání instančního objektu pro HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "přidat instančního objektu pro HDInsight cluster")

4. Klikněte na tlačítko **přístup** nakonfigurovat přístup ke složce.  Zobrazit [nakonfigurovat oprávnění k souboru](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Konfigurace oprávnění k souboru

Se nakonfiguruje se liší v závislosti na tom, jestli účet se používá jako výchozí úložiště nebo dalšího účtu úložiště:

- Slouží jako výchozí úložiště

    - oprávnění na úrovni kořenového účtu Data Lake Storage Gen1
    - oprávnění na úrovni kořenového adresáře úložiště clusteru HDInsight. Například __/clusterů__ složky dříve v tomto kurzu použili.
- Použít jako další úložiště

    - Oprávnění na složky, kde potřebují přístup k souborům.

**Můžete přiřadit oprávnění na úrovni kořenového účtu Data Lake Storage Gen1**

1. Na **přístupu k Data Lake Storage Gen1** okna, klikněte na tlačítko **přístup**. **Vybrat oprávnění k souborům** otevře se okno. Zobrazí všechny účty Data Lake Storage Gen1 ve vašem předplatném.
2. Při najetí myší (neklikejte) myši nad název účtu Data Lake Storage Gen1 chcete zviditelnit zaškrtávací políčko, potom zaškrtněte políčko.

    ![Přidání instančního objektu pro HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "přidat instančního objektu pro HDInsight cluster")

  Ve výchozím nastavení __čtení__, __zápisu__, a __EXECUTE__ jsou vybrané všechny.

3. Klikněte na tlačítko **vyberte** v dolní části stránky.
4. Klikněte na tlačítko **spustit** můžete přiřadit oprávnění.
5. Klikněte na **Done** (Hotovo).

**Můžete přiřadit oprávnění na úrovni kořenového clusteru HDInsight**

1. Na **přístupu k Data Lake Storage Gen1** okna, klikněte na tlačítko **přístup**. **Vybrat oprávnění k souborům** otevře se okno. Zobrazí všechny účty Data Lake Storage Gen1 ve vašem předplatném.
1. Z **vybrat oprávnění k souborům** okna, klikněte na název účtu Data Lake Storage Gen1 zobrazíte jeho obsah.
2. Zaškrtnutím políčka nalevo od složce vyberte ke kořenu úložiště clusteru HDInsight. Podle na snímku obrazovky výše, ke kořenu úložiště clusteru je __/clusterů__ složky, kterou jste zadali při výběru jako výchozího úložiště Data Lake Storage Gen1.
3. Nastavte oprávnění u složky.  Ve výchozím nastavení, čtení, zápis a provedení jsou vybrané všechny.
4. Klikněte na tlačítko **vyberte** v dolní části stránky.
5. Klikněte na **Run** (Spustit).
6. Klikněte na **Done** (Hotovo).

Pokud používáte Gen1 úložiště Data Lake jako další úložiště, je nutné přiřadit oprávnění jenom pro složky, které chcete získat přístup z clusteru HDInsight. Například na snímku obrazovky níže, můžete poskytnout přístup pouze **mynewfolder** složky v účtu Data Lake Storage Gen1.

![Přiřadit oprávnění instančního objektu služby do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "přiřadit oprávnění instančního objektu služby do clusteru HDInsight")


## <a name="verify-cluster-set-up"></a>Ověřte nastavení clusteru

Po dokončení instalace clusteru v okně clusteru ověřte výsledky provedením jedné nebo obou z následujících kroků:

* Ověřte, zda je přidružené úložiště pro cluster, který jste zadali účet Data Lake Storage Gen1, klikněte na tlačítko **účty úložiště** v levém podokně.

    ![Přidání instančního objektu pro HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "přidat instančního objektu pro HDInsight cluster")

* Ověřte, zda je správně přidružené ke clusteru HDInsight instanční objekt služby, klikněte na tlačítko **přístupu k Data Lake Storage Gen1** v levém podokně.

    ![Přidání instančního objektu pro HDInsight cluster](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "přidat instančního objektu pro HDInsight cluster")


## <a name="examples"></a>Příklady

Jakmile nastavíte cluster s Data Lake Storage Gen1 jako úložiště, odkazují na tyto příklady toho, jak analyzovat data ukládaná ve službě Data Lake Storage Gen1 pomocí clusteru HDInsight.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Spuštění dotazu Hive s daty v účtu Data Lake Storage Gen1 (jako primární úložiště)

Ke spuštění dotazu Hive pomocí rozhraní Hive zobrazení Ambari portálu. Pokyny týkající se použití zobrazení Ambari Hive najdete v tématu [použijte zobrazení Hive se systémem Hadoop v HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Při práci s daty v účtu Data Lake Storage Gen1 existuje několik řetězců, chcete-li změnit.

Pokud používáte, například clusteru, který jste vytvořili pomocí Data Lake Storage Gen1 jako primární úložiště, je cesta k datům: *adl: / / < data_lake_storage_gen1_account_name > /azuredatalakestore.net/path/to/file*. Dotaz Hive, který chcete vytvořit tabulku ze vzorových dat, který je uložený v účtu Data Lake Storage Gen1 bude vypadat jako následující příkaz:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Popis:
* `adl://hdiadlsg1storage.azuredatalakestore.net/` je kořenový adresář účtu Data Lake Storage Gen1.
* `/clusters/myhdiadlcluster` je kořen dat clusteru, který jste zadali při vytváření clusteru.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` je umístění ukázkový soubor, který jste použili v dotazu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Spuštění dotazu Hive s daty v účtu Data Lake Storage Gen1 (jako další úložiště)

Pokud cluster, který jste vytvořili využívá úložiště objektů Blob jako výchozím úložištěm, ukázková data není obsažen v účtu Data Lake Storage Gen1, který se používá jako další úložiště. V takovém případě nejprve přenosu dat z úložiště objektů Blob do účtu Data Lake Storage Gen1 a pak spusťte dotazy, jak je znázorněno v předchozím příkladu.

Informace o tom, jak kopírovat data z úložiště objektů Blob v účtu Data Lake Storage Gen1 naleznete v následujících článcích:

* [Použití Distcp ke kopírování dat mezi objekty BLOB služby Azure Storage a Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Použití AdlCopy ke kopírování dat z Azure Storage BLOB do Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Použití Data Lake Storage Gen1 s clusterem Spark
Spark cluster můžete použít ke spuštění úlohy Spark na data, která je uložena v účtu Data Lake Storage Gen1. Další informace najdete v tématu [clusteru HDInsight Spark použít k analýze dat v Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Použití Data Lake Storage Gen1 v topologii Storm
Účet Data Lake Storage Gen1 můžete použít k zápisu dat z topologie Storm. Pokyny o tom, jak dosáhnout tohoto scénáře najdete v tématu [použití Azure Data Lake Storage Gen1 s Apache Storm pomocí HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Pomocí Data Lake Storage Gen1 s využitím clusterů Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Prostředí PowerShell: Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
