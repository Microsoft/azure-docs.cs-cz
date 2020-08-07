---
title: Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight
description: Naučte se používat Azure Data Lake Storage Gen2 s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/24/2020
ms.openlocfilehash: 21b09e6b7a2be6b87288d973b40c566fb6217841
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849977"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight

Azure Data Lake Storage Gen2 je cloudová služba úložiště vyhrazená pro analýzy velkých objemů dat, která je založená na službě Azure Blob Storage. Data Lake Storage Gen2 kombinuje možnosti služby Azure Blob Storage a Azure Data Lake Storage Gen1. Výsledná služba nabízí funkce z Azure Data Lake Storage Gen1. Mezi tyto funkce patří: sémantika systému souborů, zabezpečení na úrovni adresáře a souborů a přizpůsobivost. Společně s nízkými náklady, vrstvené úložiště, vysokou dostupností a možnostmi zotavení po havárii ze služby Azure Blob Storage.

## <a name="data-lake-storage-gen2-availability"></a>Dostupnost Data Lake Storage Gen2

Data Lake Storage Gen2 je k dispozici jako možnost úložiště pro téměř všechny typy clusterů Azure HDInsight jako výchozí i pro další účet úložiště. HBA ale můžou mít jenom jeden Data Lake Storage Gen2 účet.

Úplné porovnání možností vytváření clusteru pomocí Data Lake Storage Gen2 najdete v tématu [porovnání možností úložiště pro použití s clustery Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Po výběru Data Lake Storage Gen2 jako **primárního typu úložiště**nemůžete vybrat data Lake Storage Gen1 účet jako další úložiště.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Vytvoření clusteru s Data Lake Storage Gen2 přes Azure Portal

Pokud chcete vytvořit cluster HDInsight, který používá Data Lake Storage Gen2 pro úložiště, postupujte podle těchto kroků a nakonfigurujte účet Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Vytvořte spravovanou identitu přiřazenou uživatelem, pokud ji ještě nemáte.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V levém horním rohu klikněte na **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **přiřazený uživatel** a klikněte na položku **spravovaná identita přiřazená uživateli**.
1. Klikněte na **Vytvořit**.
1. Zadejte název spravované identity, vyberte správné předplatné, skupinu prostředků a umístění.
1. Klikněte na **Vytvořit**.

Další informace o tom, jak spravované identity fungují ve službě Azure HDInsight, najdete v tématu [spravované identity ve službě Azure HDInsight](hdinsight-managed-identities.md).

![Vytvoření spravované identity přiřazené uživatelem](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Vytvoření účtu Data Lake Storage Gen2

Vytvořte účet úložiště Azure Data Lake Storage Gen2.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V levém horním rohu klikněte na **vytvořit prostředek**.
1. Do vyhledávacího pole zadejte **Storage** a klikněte na **účet úložiště**.
1. Klikněte na **Vytvořit**.
1. Na obrazovce **vytvořit účet úložiště** :
    1. Vyberte správné předplatné a skupinu prostředků.
    1. Zadejte název účtu Data Lake Storage Gen2.
    1. Klikněte na kartu **Upřesnit** .
    1. V části **Data Lake Storage Gen2**klikněte na **povoleno** vedle **hierarchického oboru názvů** .
    1. Klikněte na **Zkontrolovat a vytvořit**.
    1. Klikněte na **Vytvořit**.

Další informace o dalších možnostech při vytváření účtu úložiště najdete v tématu [rychlý Start: vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Snímek obrazovky, který ukazuje vytvoření účtu úložiště v Azure Portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Nastavení oprávnění pro spravovanou identitu na účtu Data Lake Storage Gen2

Přiřaďte spravovanou identitu k roli **vlastníka dat objektů BLOB úložiště** v účtu úložiště.

1. V [Azure Portal](https://portal.azure.com)přejít na účet úložiště.
1. Vyberte svůj účet úložiště a pak vyberte **řízení přístupu (IAM)** a zobrazte nastavení řízení přístupu pro tento účet. Vyberte kartu **přiřazení rolí** a zobrazte seznam přiřazení rolí.

    ![Snímek obrazovky s nastavením řízení přístupu k úložišti](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Kliknutím na tlačítko **+ Přidat přiřazení role** přidejte novou roli.
1. V okně **Přidat přiřazení role** vyberte roli **vlastníka dat objektu BLOB úložiště** . Pak vyberte předplatné, které má spravovanou identitu a účet úložiště. V dalším kroku vyhledejte uživatelem přiřazenou spravovanou identitu, kterou jste předtím vytvořili. Nakonec vyberte spravovanou identitu a zobrazí se v seznamu **Vybrané členy**.

    ![Snímek obrazovky, který ukazuje, jak přiřadit roli Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Vyberte **Uložit**. Uživatelem přiřazená identita, kterou jste vybrali, je teď uvedená v seznamu vybraná role.
1. Po dokončení tohoto počátečního nastavení můžete cluster vytvořit prostřednictvím portálu. Cluster musí být ve stejné oblasti Azure jako účet úložiště. Na kartě **úložiště** v nabídce vytvoření clusteru vyberte následující možnosti:

    * Jako **typ primárního úložiště**vyberte **Azure Data Lake Storage Gen2**.
    * V části **primární účet úložiště**vyhledejte a vyberte nově vytvořený účet úložiště data Lake Storage Gen2.

    * V části **Identita**vyberte nově vytvořenou spravovanou identitu přiřazenou uživatelem.

        ![Nastavení úložiště pro použití Data Lake Storage Gen2 se službou Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Pokud chcete přidat sekundární Data Lake Storage Gen2 účet, na úrovni účtu úložiště jednoduše přiřaďte spravovanou identitu vytvořenou dříve k novému účtu úložiště Data Lake Storage Gen2, který chcete přidat. Doporučujeme přidat sekundární Data Lake Storage Gen2 účet přes okno další účty úložiště ve službě HDInsight se nepodporuje.
    > * V účtu Azure Storage, který používá HDInsight, můžete povolit RA-GRS nebo RA-ZRS. Vytvoření clusteru se sekundárním koncovým bodem RA-GRS nebo RA-ZRS se ale nepodporuje.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Vytvoření clusteru s Data Lake Storage Gen2 prostřednictvím Azure CLI

Můžete [stáhnout ukázkový soubor šablony](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) a [stáhnout ukázkový soubor parametrů](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Než použijete šablonu a fragment kódu Azure CLI níže, nahraďte následující zástupné symboly jejich správnými hodnotami:

| Zástupný symbol | Popis |
|---|---|
| `<SUBSCRIPTION_ID>` | ID vašeho předplatného Azure |
| `<RESOURCEGROUPNAME>` | Skupina prostředků, ve které chcete vytvořit nový cluster a účet úložiště. |
| `<MANAGEDIDENTITYNAME>` | Název spravované identity, kterému budou udělena oprávnění k vašemu Azure Data Lake Storage Gen2 účtu. |
| `<STORAGEACCOUNTNAME>` | Nový účet Azure Data Lake Storage Gen2, který se vytvoří. |
| `<FILESYSTEMNAME>`  | Název systému souborů, který by měl tento cluster používat v účtu úložiště. |
| `<CLUSTERNAME>` | Název clusteru HDInsight. |
| `<PASSWORD>` | Vaše zvolené heslo pro přihlášení ke clusteru pomocí SSH a řídicího panelu Ambari |

Níže uvedený fragment kódu provede následující úvodní kroky:

1. Přihlásí se k účtu Azure.
1. Nastaví aktivní předplatné, ve kterém se budou provádět operace vytvoření.
1. Vytvoří novou skupinu prostředků pro nové aktivity nasazení.
1. Vytvoří uživatelem přiřazenou spravovanou identitu.
1. Přidá do Azure CLI rozšíření pro použití funkcí pro Data Lake Storage Gen2.
1. Vytvoří nový účet Data Lake Storage Gen2 pomocí `--hierarchical-namespace true` příznaku.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Potom se přihlaste k portálu. Přidejte do role **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště novou spravovanou identitu přiřazenou uživatelem. Tento krok je popsaný v kroku 3 tématu [použití Azure Portal](hdinsight-hadoop-use-data-lake-storage-gen2.md).

 > [!IMPORTANT]
 > Ujistěte se, že váš účet úložiště má přiřazenou identitu role **Přispěvatel dat objektů BLOB úložiště** , jinak se vytvoření clusteru nezdaří.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Vytvoření clusteru pomocí Data Lake Storage Gen2 přes Azure PowerShell

Použití PowerShellu k vytvoření clusteru HDInsight s Azure Data Lake Storage Gen2 není aktuálně podporováno.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Řízení přístupu pro Data Lake Storage Gen2 ve službě HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jaké druhy oprávnění podporuje Data Lake Storage Gen2 podporu?

Data Lake Storage Gen2 používá model řízení přístupu, který podporuje řízení přístupu na základě role (RBAC) a seznamy řízení přístupu (ACL) typu POSIX. Data Lake Storage Gen1 podporuje seznamy řízení přístupu pouze pro řízení přístupu k datům.

RBAC pomocí přiřazení rolí efektivně aplikuje sady oprávnění pro uživatele, skupiny a instanční objekty pro prostředky Azure. Tyto prostředky Azure jsou obvykle omezené na prostředky nejvyšší úrovně (například účty Azure Storage). Pro Azure Storage a také Data Lake Storage Gen2 tento mechanismus byl rozšířen na prostředek systému souborů.

 Další informace o oprávněních k souborům pomocí RBAC najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Další informace o oprávněních k souborům pomocí seznamů řízení přístupu najdete v tématu [seznam řízení přístupu pro soubory a adresáře](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Návody řízení přístupu k datům v Data Lake Storage Gen2?

Schopnost clusteru HDInsight přistupovat k souborům v Data Lake Storage Gen2 je řízena prostřednictvím spravovaných identit. Spravovaná identita je identita registrovaná v Azure Active Directory (Azure AD), jejíž přihlašovací údaje jsou spravované v Azure. U spravovaných identit nemusíte registrovat instanční objekty ve službě Azure AD. Nebo Udržujte přihlašovací údaje, jako jsou certifikáty.

Služby Azure mají dva typy spravovaných identit: přiřazeno systémem a přiřazeno uživateli. HDInsight používá pro přístup k Data Lake Storage Gen2 spravované identity přiřazené uživatelem. A vytvoří `user-assigned managed identity` se jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure.

Životní cyklus identity přiřazené uživatelem se spravuje nezávisle na životním cyklu instancí služeb Azure, ke kterým je přiřazená. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Návody nastavení oprávnění pro uživatele Azure AD k dotazování na data v Data Lake Storage Gen2 pomocí podregistru nebo jiných služeb?

Pokud chcete nastavit oprávnění pro uživatele k dotazování na data, použijte skupiny zabezpečení Azure AD jako přiřazený objekt zabezpečení v seznamech ACL. Nepřiřazujte přímo jednotlivým uživatelům nebo instančním objektům oprávnění k přístupu k souborům. Pomocí skupin zabezpečení služby Azure AD pro řízení toku oprávnění můžete přidávat a odebírat uživatele nebo instanční objekty bez nutnosti znovu použít seznamy ACL pro celou adresářovou strukturu. Stačí přidat nebo odebrat uživatele z příslušné skupiny zabezpečení Azure AD. Seznamy ACL nejsou zděděné, takže při opakovaném použití seznamů ACL je potřeba aktualizovat seznam ACL u všech souborů a podadresářů.

## <a name="access-files-from-the-cluster"></a>Přístup k souborům z clusteru

Existuje několik způsobů, jak můžete přistupovat k souborům v Data Lake Storage Gen2 z clusteru HDInsight.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Pomocí zkráceného formátu cesty**. Pomocí tohoto přístupu nahradíte cestu až ke kořenu clusteru:

    ```
    abfs:///<file.path>/
    ```

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Příklady přístupu k datům

Příklady jsou založené na [připojení SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) k hlavnímu uzlu clusteru. V příkladech se používají všechna tři schémata identifikátoru URI. Nahradit `CONTAINERNAME` a `STORAGEACCOUNT` za relevantní hodnoty

#### <a name="a-few-hdfs-commands"></a>Několik příkazů HDFS

1. Vytvoří soubor v místním úložišti.

    ```bash
    touch testFile.txt
    ```

1. Vytvořte adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Kopírovat data z místního úložiště do úložiště clusteru.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Vypíše obsah adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Vytvoření tabulky podregistru

Pro ilustrativní účely se zobrazí tři umístění souborů. Pro skutečné provedení použijte jenom jednu z `LOCATION` položek.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Další kroky

* [Integrace Azure HDInsight s Data Lake Storage Gen2 Preview – seznam ACL a aktualizace zabezpečení](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Úvod do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kurz: extrakce, transformace a načtení dat pomocí interaktivního dotazu ve službě Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
