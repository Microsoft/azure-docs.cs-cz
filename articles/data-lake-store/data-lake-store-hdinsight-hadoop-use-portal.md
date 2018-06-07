---
title: Pomocí portálu Azure k vytvoření clusterů se Azure HDInsight s Data Lake Store | Microsoft Docs
description: Vytvoření a použití clusterů HDInsight s Azure Data Lake Store pomocí portálu Azure
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
ms.openlocfilehash: ce217714b90a6bdce31437e7ecb97b1b9e59e77c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624884"
---
# <a name="create-hdinsight-clusters-with-data-lake-store-by-using-the-azure-portal"></a>Vytvoření clusterů HDInsight s Data Lake Store pomocí portálu Azure
> [!div class="op_single_selector"]
> * [Použití portálu Azure Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Pomocí prostředí PowerShell (pro výchozí úložiště)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Pomocí prostředí PowerShell (pro další úložiště)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Pomocí Správce prostředků](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Další informace o použití portálu Azure k vytvoření clusteru HDInsight pomocí účtu Azure Data Lake Store jako výchozí úložiště nebo další úložiště. I když je dodatečné úložiště pro HDInsight cluster volitelný, doporučujeme ukládat firemní data v účtech úložiště.

## <a name="prerequisites"></a>Požadavky
Než začnete tento kurz, ujistěte se, že jste splněny následující požadavky:

* **Předplatné Azure**. Přejděte na [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Postupujte podle pokynů z [Začínáme s Azure Data Lake Store pomocí portálu Azure](data-lake-store-get-started-portal.md). Musíte taky vytvořit kořenové složky na účtu.  V tomto kurzu kořenovou složku s názvem __/clusterů__ se používá.
* **Objektu služby Azure Active Directory**. V tomto kurzu poskytuje pokyny o tom, jak vytvořit službu objektu zabezpečení ve službě Azure Active Directory (Azure AD). Pokud chcete vytvořit objekt služby, ale musí být správce Azure AD. Pokud jste správce, můžete přeskočit tento požadavek a pokračujte v tomto kurzu.

    >[!NOTE]
    >Pouze v případě, že jste správce Azure AD, vytvořte službu objektu zabezpečení. Správce služby Azure AD musí vytvořte službu objektu zabezpečení před vytvořením clusteru HDInsight s Data Lake Store. Také musí být objekt služby vytvořen pomocí certifikátu, jak je popsáno v [vytvořit objekt služby pomocí certifikátu](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).
    >

## <a name="create-an-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

V této části vytvoříte clusteru HDInsight s účty Data Lake Store jako výchozí nebo další úložiště. Tento článek se týká pouze část konfigurace účtů Data Lake Store.  Informace o vytvoření obecné clusteru a postupy najdete v tématu [vytvoření Hadoop clusterů v HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-store-as-default-storage"></a>Vytvoření clusteru s Data Lake Store jako výchozí úložiště

**K vytvoření clusteru HDInsight s Data Lake Store jako výchozí účet úložiště**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Postupujte podle [vytvořit clustery se](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) obecné informace o vytváření clusterů HDInsight.
3. Na **úložiště** okno, v části **primárního úložiště typu**, vyberte **Data Lake Store**a potom zadejte následující informace:

    ![Objekt služby přidat do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "instanční objekt přidat do clusteru HDInsight")

    - **Vyberte Data Lake Store účtu**: Vyberte existující účet Data Lake Store. Existující účet Data Lake Store je povinný.  V tématu [požadavky](#prerequisites).
    - **Kořenová cesta**: Zadejte cestu, kam mají ukládat soubory specifických pro cluster. Na na snímku obrazovky je __/clusterů/myhdiadlcluster/__, ve kterém __/clusterů__ složka musí existovat a vytvoří portálu *myhdicluster* složky.  *Myhdicluster* je název clusteru.
    - **Data Lake Store přístup**: Konfigurace přístupu mezi účtu Data Lake Store a HDInsight cluster. Pokyny najdete v tématu [přístup konfigurovat Data Lake Store](#configure-data-lake-store-access).
    - **Další účty úložiště**: účtů přidat účty úložiště Azure jako dodatečné úložiště pro cluster. Chcete-li přidat další úložiště Data Lake provádí tím, že při konfiguraci účtu Data Lake Store jako typ primárního úložiště clusteru oprávnění na data v další účty Data Lake Store. Viz [Konfigurace přístupu ke službě Data Lake Store](#configure-data-lake-store-access).

4. Na **přístup Data Lake Store**, klikněte na tlačítko **vyberte**a poté pokračovat ve vytváření clusteru, jak je popsáno v [vytvoření Hadoop clusterů v HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).


### <a name="create-a-cluster-with-data-lake-store-as-additional-storage"></a>Vytvoření clusteru s Data Lake Store jako další úložiště

Podle následujících pokynů vytvoření clusteru HDInsight se pomocí účtu Azure Storage jako výchozí úložiště a účet Data Lake Store jako další úložiště.
**K vytvoření clusteru HDInsight s Data Lake Store jako výchozí účet úložiště**

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Postupujte podle [vytvořit clustery se](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) obecné informace o vytváření clusterů HDInsight.
3. Na **úložiště** okno, v části **primárního úložiště typu**, vyberte **Azure Storage**a potom zadejte následující informace:

    ![Objekt služby přidat do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "instanční objekt přidat do clusteru HDInsight")

    - **Metody výběru**: použijte jednu z následujících možností:

        * Chcete-li zadat účet úložiště, který je součástí vašeho předplatného Azure, vyberte **mé odběry**a potom vyberte účet úložiště.
        * Chcete-li zadat účet úložiště, který je mimo vašeho předplatného Azure, vyberte **přístupový klíč**a pak zadejte informace pro mimo účet úložiště.

    - **Výchozí kontejner**: použijte výchozí hodnotu nebo zadejte vlastní název.

    - Další účty úložiště: přidat další účty Azure Storage jako další úložiště.
    - Data Lake Store přístup: Konfigurace přístupu mezi účtu Data Lake Store a HDInsight cluster. Pokyny naleznete v části [přístup konfigurovat Data Lake Store](#configure-data-lake-store-access).

## <a name="configure-data-lake-store-access"></a>Konfigurace přístupu Data Lake Store 

V této části nakonfigurujete Data Lake Store přístup z clusterů HDInsight pomocí Azure Active Directory instanční objekt. 

### <a name="specify-a-service-principal"></a>Zadejte hlavní název služby

Z portálu Azure můžete použít stávající instanční objekt nebo vytvořte novou.

**Chcete-li vytvořit objekt služby z portálu Azure**

1. Klikněte na tlačítko **Data Lake Store přístup** z okna úložiště.
2. Na **Data Lake Store přístup** okně klikněte na tlačítko **vytvořit nový**.
3. Klikněte na tlačítko **instanční objekt**a pak postupujte podle pokynů vytvořte objekt služby.
4. Stažení certifikátu, pokud se rozhodnete použít znovu. Stáhnout certifikát je užitečné, pokud chcete použít stejný objekt zabezpečení služby, když vytvoříte další clustery HDInsight.

    ![Objekt služby přidat do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "instanční objekt přidat do clusteru HDInsight")

4. Klikněte na tlačítko **přístup** konfigurovat přístup ke složce.  V tématu [konfigurace oprávnění k souboru](#configure-file-permissions).


**Chcete-li použít existující objekt zabezpečení služby z portálu Azure**

1. Klikněte na tlačítko **Data Lake Store přístup**.
1. Na **Data Lake Store přístup** okně klikněte na tlačítko **použít existující**.
2. Klikněte na tlačítko **instanční objekt**a potom vyberte objekt služby. 
3. Nahrajte certifikát (soubor .pfx), který je spojen s vybranou instanční objekt a potom zadejte heslo certifikátu.

    ![Objekt služby přidat do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "instanční objekt přidat do clusteru HDInsight")

4. Klikněte na tlačítko **přístup** konfigurovat přístup ke složce.  V tématu [konfigurace oprávnění k souboru](#configure-file-permissions).


### <a name="configure-file-permissions"></a>Nakonfigurujte oprávnění souborů

Nakonfiguruje se liší v závislosti na tom, jestli účet se používá jako výchozí úložiště nebo účet další úložiště:

- Používat jako výchozí úložiště

    - oprávnění na úrovni kořenového účtu Data Lake Store
    - oprávnění na kořenové úrovni úložiště clusteru HDInsight. Například __/clusterů__ složku dříve v tomto kurzu použili.
- Použít jako další úložiště

    - Oprávnění u složky, které potřebují přístup k souborům.

**Můžete přiřadit oprávnění na úrovni kořenového účtu Data Lake Store**

1. Na **Data Lake Store přístup** okně klikněte na tlačítko **přístup**. **Vyberte oprávnění k souboru** otevře okno. Zobrazí seznam všech účtů Data Lake Store v rámci vašeho předplatného.
2. Pozastavte ukazatel myši (neklikejte na) myši na název účtu Data Lake Store, chcete-li zaškrtnutí políčka Zobrazit, pak vyberte zaškrtávací pole.

    ![Objekt služby přidat do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "instanční objekt přidat do clusteru HDInsight")

  Ve výchozím nastavení __ČÍST__, __zápisu__, a __EXECUTE__ jsou vybrané všechny.

3. Klikněte na tlačítko **vyberte** v dolní části stránky.
4. Klikněte na tlačítko **spustit** můžete přiřadit oprávnění.
5. Klikněte na **Done** (Hotovo).

**Můžete přiřadit oprávnění na úrovni kořenového clusteru HDInsight**

1. Na **Data Lake Store přístup** okně klikněte na tlačítko **přístup**. **Vyberte oprávnění k souboru** otevře okno. Zobrazí seznam všech účtů Data Lake Store v rámci vašeho předplatného.
1. Z **vyberte oprávnění k souboru** okně klikněte na název Data Lake Store se má zobrazit jeho obsah.
2. Vyberte kořenové úložiště clusteru HDInsight zaškrtnutím políčka nalevo od složce. Podle na snímku obrazovky dříve, kořenového úložiště clusteru je __/clusterů__ složky, kterou jste zadali, že při výběru Data Lake Store jako výchozí úložiště.
3. Nastavte oprávnění ve složce.  Ve výchozím nastavení, číst, zapisovat a spouštět jsou vybrané všechny.
4. Klikněte na tlačítko **vyberte** v dolní části stránky.
5. Klikněte na **Run** (Spustit).
6. Klikněte na **Done** (Hotovo).

Pokud používáte Data Lake Store jako další úložiště, je nutné přiřadit oprávnění jen pro složky, které chcete získat přístup z clusteru HDInsight. Například na tomto snímku obrazovky můžete poskytnout přístup pouze k **hdiaddonstorage** složek v účtu Data Lake Store.

![Přiřazení oprávnění hlavní služby ke clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "přiřadit oprávnění hlavní služby ke clusteru HDInsight")


## <a name="verify-cluster-set-up"></a>Ověřte nastavení clusteru

Po dokončení instalace clusteru v okně clusteru ověřte výsledky provedením jedné nebo obou následujících kroků:

* Ověřte, zda přidruženého úložiště pro cluster účtu Data Lake Store, který jste zadali, klikněte na tlačítko **účty úložiště** v levém podokně.

    ![Objekt služby přidat do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "instanční objekt přidat do clusteru HDInsight")

* Ověřte, zda je správně přidružena ke clusteru HDInsight objektu služby, klikněte na tlačítko **Data Lake Store přístup** v levém podokně.

    ![Objekt služby přidat do clusteru HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "instanční objekt přidat do clusteru HDInsight")


## <a name="examples"></a>Příklady

Poté, co jste nastavili jako úložiště clusteru s Data Lake Store, naleznete v těchto příkladech použití clusteru HDInsight k analýze dat, která je uložená v Data Lake Store.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-primary-storage"></a>Spouštění dotazů Hive proti dat v Data Lake Store (jako primární úložiště)

Ke spuštění dotazu Hive, použijte rozhraní Hive zobrazení Ambari portálu. Pokyny týkající se používání zobrazení Ambari Hive naleznete v tématu [použít zobrazení Hive se systémem Hadoop v HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Při práci s daty v Data Lake Store, existuje několik řetězce, chcete-li změnit.

Pokud chcete použít, například cluster, který jste vytvořili s Data Lake Store jako primární úložiště, je cesta k datům: *adl: / / < data_lake_store_account_name > /azuredatalakestore.net/path/to/file*. Dotaz Hive a vytvořte tabulku z ukázkových dat, který je uložený v účtu Data Lake Store vypadá následující příkaz:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsstorage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Popis:
* `adl://hdiadlstorage.azuredatalakestore.net/` je kořenem účtu Data Lake Store.
* `/clusters/myhdiadlcluster` je kořenovém clusteru data, která jste zadali při vytváření clusteru.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` je umístění ukázkový soubor, který jste použili v dotazu.

### <a name="run-a-hive-query-against-data-in-a-data-lake-store-as-additional-storage"></a>Spouštění dotazů Hive proti dat v Data Lake Store (jako další úložiště)

Pokud cluster, který jste vytvořili používá jako výchozí úložiště Blob storage, ukázková data není obsažen v účtu Azure Data Lake Store, který se používá jako další úložiště. V takovém případě nejdřív přenést data z úložiště objektů Blob do Data Lake Store a poté spusťte dotazy, jak je znázorněno v předchozím příkladu.

Informace o tom, jak zkopírovat data z úložiště objektů Blob do Data Lake Store naleznete v následujících článcích:

* [Použití Distcp ke kopírování dat mezi objektů BLOB služby Azure Storage a Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)
* [Použít AdlCopy ke zkopírování dat z Azure úložiště objektů BLOB do Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-store-with-a-spark-cluster"></a>Použití Data Lake Store s clusterem Spark
Spark cluster můžete použít ke spuštění úloh Spark na data, která je uložená v Data Lake Store. Další informace najdete v tématu [clusteru používejte HDInsight Spark k analýze dat v Data Lake Store](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).


### <a name="use-data-lake-store-in-a-storm-topology"></a>Použití Data Lake Store v topologii Storm
Data Lake Store můžete použít k zápisu dat ze topologie Storm. Pokyny o tom, jak dosáhnout tento scénář naleznete v tématu [pomocí Azure Data Lake Store s Apache Storm v prostředí HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Použití Data Lake Store s clustery Azure HDInsight](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [Prostředí PowerShell: Vytvoření clusteru HDInsight používat Data Lake Store](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
