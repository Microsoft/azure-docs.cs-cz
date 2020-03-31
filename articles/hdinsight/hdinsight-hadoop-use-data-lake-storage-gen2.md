---
title: Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight
description: Přečtěte si, jak používat Azure Data Lake Storage Gen2 s clustery Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272289"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight

Azure Data Lake Storage Gen2 je služba cloudového úložiště určená pro analýzu velkých objemů dat, která je postavená na úložišti objektů Blob Azure. Data Lake Storage Gen2 kombinuje možnosti úložiště objektů blob Azure a Azure Data Lake Storage Gen1. Výsledná služba nabízí funkce z Azure Data Lake Storage Gen1, jako je sémantiku systému souborů, zabezpečení na úrovni adresářů a souborů a škálovatelnost, spolu s levným, vrstveným úložištěm, vysokou dostupností a možnostmi zotavení po havárii z úložiště objektů blob Azure.

## <a name="data-lake-storage-gen2-availability"></a>Dostupnost úložiště datového jezera Gen2

Data Lake Storage Gen2 je k dispozici jako možnost úložiště pro téměř všechny typy clusterů Azure HDInsight jako výchozí i další účet úložiště. HBase však může mít pouze jeden účet Data Lake Storage Gen2.

Úplné porovnání možností vytváření clusteru pomocí data lake storage gen2 najdete [v tématu Porovnání možností úložiště pro použití s clustery Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Po výběru Data Lake Storage Gen2 jako **typ primárního úložiště**, nelze vybrat data lake storage Gen1 účet jako další úložiště.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Vytvoření clusteru pomocí portálu Data Lake Storage Gen2 prostřednictvím portálu Azure

Chcete-li vytvořit cluster HDInsight, který používá data lake storage Gen2 pro úložiště, postupujte takto a nakonfigurujte účet Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Pokud ještě nemáte, vytvořte spravovanou identitu přiřazenou uživateli.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levém horním rohu **klikněte na Vytvořit zdroj**.
1. Do vyhledávacího pole zadejte **přiřazený uživatel** a klepněte na **položku Přiřazená spravovaná identita uživatele**.
1. Klikněte na **Vytvořit**.
1. Zadejte název spravované identity, vyberte správné předplatné, skupinu prostředků a umístění.
1. Klikněte na **Vytvořit**.

Další informace o tom, jak spravované identity fungují ve Službě Azure HDInsight, najdete [v tématu Spravované identity v Azure HDInsight](hdinsight-managed-identities.md).

![Vytvoření spravované identity přiřazené uživatelem](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Vytvoření účtu Data Lake Storage Gen2

Vytvořte účet úložiště Azure Data Lake Storage Gen2.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levém horním rohu **klikněte na Vytvořit zdroj**.
1. Do vyhledávacího pole zadejte **úložiště** a klikněte na **Účet úložiště**.
1. Klikněte na **Vytvořit**.
1. Na obrazovce **Vytvořit účet úložiště:**
    1. Vyberte správné předplatné a skupinu prostředků.
    1. Zadejte název účtu Data Lake Storage Gen2. Další informace o konvencích pojmenování účtů úložiště najdete [v tématu Konvence pojmenování prostředků Azure](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage).
    1. Klikněte na kartu **Upřesnit.**
    1. V části **Data Lake Storage Gen2**klepněte na Možnost **Povoleno** vedle **hierarchického oboru názvů** .
    1. Klikněte na **Zkontrolovat a vytvořit**.
    1. Klikněte na **Vytvořit.**

Další informace o dalších možnostech při vytváření účtu úložiště najdete v [tématu Úvodní příručka: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Snímek obrazovky s vytvořením účtu úložiště na webu Azure Portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Nastavení oprávnění pro spravovanou identitu v účtu Data Lake Storage Gen2

Přiřaďte spravovanou identitu roli **Vlastník dat objektu blob úložiště** v účtu úložiště.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na účet úložiště.
1. Vyberte svůj účet úložiště a pak vyberte **Řízení přístupu (IAM),** chcete-li zobrazit nastavení řízení přístupu pro účet. Výběrem karty **Přiřazení rolí** zobrazíte seznam přiřazení rolí.

    ![Snímek obrazovky s nastavením řízení přístupu k úložišti](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Chcete-li přidat novou roli, vyberte tlačítko **+ Přidat přiřazení role.**
1. V okně **Přidat přiřazení role** vyberte roli Vlastník dat **objektu blob úložiště.** Potom vyberte předplatné, které má spravovanou identitu a účet úložiště. Dále vyhledejte uživatelem přiřazenou spravovanou identitu, kterou jste vytvořili dříve. Nakonec vyberte spravovanou identitu a bude uvedena v části **Vybrané členy**.

    ![Snímek obrazovky znázorňující přiřazení role RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Vyberte **Uložit**. Vybraná identita přiřazená uživateli je nyní uvedena pod vybranou rolí.
1. Po dokončení tohoto počátečního nastavení můžete vytvořit cluster prostřednictvím portálu. Cluster musí být ve stejné oblasti Azure jako účet úložiště. Na kartě **Úložiště** v nabídce vytvoření clusteru vyberte následující možnosti:

    * V **případě typu Primární úložiště**vyberte Azure Data Lake Storage **Gen2**.
    * V části **Účet primárního úložiště**vyhledejte a vyberte nově vytvořený účet úložiště Data Lake Storage Gen2.

    * V části **Identita**vyberte nově vytvořenou spravovanou identitu přiřazenou uživateli.

        ![Nastavení úložiště pro používání Data Lake Storage Gen2 s Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Chcete-li přidat sekundární účet Data Lake Storage Gen2, na úrovni účtu úložiště jednoduše přiřaďte spravovanou identitu vytvořenou dříve k novému účtu úložiště Data Lake Storage Gen2, který chcete přidat. Upozorňujeme, že přidání sekundárního účtu Data Lake Storage Gen2 prostřednictvím okna "Další účty úložiště" na HDInsight není podporováno.
    > * Ra-GRS nebo RA-ZRS můžete povolit na účtu úložiště Azure, který používá HDInsight. Vytvoření clusteru proti sekundárnímu koncovému bodu RA-GRS nebo RA-ZRS však není podporováno.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Vytvoření clusteru pomocí služby Data Lake Storage Gen2 prostřednictvím příkazového příkazového příkazového příkazu Azure

Můžete [si stáhnout ukázkový soubor šablony](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) a [stáhnout ukázkový soubor parametrů](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Před použitím šablony a fragmentu kódu vypolynání k vypouchovanému kódem Azure níže nahraďte následující zástupné symboly jejich správnými hodnotami:

| Zástupný symbol | Popis |
|---|---|
| `<SUBSCRIPTION_ID>` | ID vašeho předplatného Azure |
| `<RESOURCEGROUPNAME>` | Skupina prostředků, kde chcete vytvořit nový účet clusteru a úložiště. |
| `<MANAGEDIDENTITYNAME>` | Název spravované identity, která bude mít oprávnění na vašem účtu Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | Nový účet Azure Data Lake Storage Gen2, který se vytvoří. |
| `<CLUSTERNAME>` | Název clusteru HDInsight. |
| `<PASSWORD>` | Zvolené heslo pro přihlášení ke clusteru pomocí SSH, stejně jako řídicí panel Ambari. |

Fragment kódu níže provede následující počáteční kroky:

1. Přihlásí se ke svému účtu Azure.
1. Nastaví aktivní předplatné, kde budou provedeny operace vytvoření.
1. Vytvoří novou skupinu prostředků pro nové aktivity nasazení.
1. Vytvoří spravovanou identitu přiřazenou uživatelem.
1. Přidá rozšíření k azure cli používat funkce pro úložiště datových jezer Gen2.
1. Vytvoří nový účet Data Lake Storage `--hierarchical-namespace true` Gen2 pomocí příznaku.

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

Dále se přihlaste k portálu. Přidejte novou uživatelem přiřazenou spravovanou identitu do role **přispěvatele dat objektů blob úložiště** v účtu úložiště, jak je popsáno v kroku 3 v části [Použití portálu Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Po přiřazení role pro spravovanou identitu přiřazenou uživateli nasadíte šablonu pomocí následujícího fragmentu kódu.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Vytvoření clusteru pomocí služby Data Lake Storage Gen2 prostřednictvím Azure PowerShellu

Použití PowerShellu k vytvoření clusteru HDInsight pomocí Azure Data Lake Storage Gen2 není momentálně podporované.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Řízení přístupu pro Úložiště datových jezer Gen2 v HDInsightu

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jaké typy oprávnění podporuje Data Lake Storage Gen2?

Data Lake Storage Gen2 používá model řízení přístupu, který podporuje jak řízení přístupu na základě rolí (RBAC) a POSIX podobné seznamy řízení přístupu (ACLs). Data Lake Storage Gen1 podporuje seznamy řízení přístupu pouze pro řízení přístupu k datům.

RBAC používá přiřazení rolí efektivně použít sady oprávnění pro uživatele, skupiny a instanční objekty pro prostředky Azure. Tyto prostředky Azure jsou obvykle omezené na prostředky nejvyšší úrovně (například účty Azure Storage). Pro Azure Storage a také Data Lake Storage Gen2, tento mechanismus byl rozšířen na prostředek systému souborů.

 Další informace o oprávněních k souborům pomocí RBAC naleznete v [tématu Řízení přístupu na základě rolí Azure (RBAC).](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)

Další informace o oprávněních k souborům pomocí seznamů ACl naleznete v [tématu Access control lists on files and s.](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Jak můžu řídit přístup ke svým datům v aplikaci Data Lake Storage Gen2?

Schopnost vašeho clusteru HDInsight přistupovat k souborům v aplikaci Data Lake Storage Gen2 je řízena pomocí spravovaných identit. Spravovaná identita je identita registrovaná ve službě Azure Active Directory (Azure AD), jejíž přihlašovací údaje spravuje Azure. Se spravovanými identitami nemusíte registrovat instanční objekty ve službě Azure AD ani udržovat přihlašovací údaje, jako jsou certifikáty.

Služby Azure mají dva typy spravovaných identit: systémově přiřazené a přiřazené uživatelem. HDInsight používá uživatelem přiřazené spravované identity pro přístup k datově jezeru Storage Gen2. Spravovaná identita přiřazená uživatelem se vytváří jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure.

Životní cyklus identity přiřazené uživatelem se spravuje nezávisle na životním cyklu instancí služeb Azure, ke kterým je přiřazená. Další informace o spravovaných identitách najdete [v tématu Jak fungují spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Jak nastavím oprávnění pro uživatele Azure AD k dotazování dat v Gen2 úložiště datového jezera pomocí Hive nebo jiných služeb?

Chcete-li nastavit oprávnění pro uživatele k dotazování dat, použijte skupiny zabezpečení Azure AD jako přiřazené objektu zabezpečení v seznamu ACL. Nepřiřazujte přímo oprávnění k přístupu k souborům jednotlivým uživatelům nebo instančním objektům. Při použití skupiny zabezpečení Azure AD řídit tok oprávnění, můžete přidat a odebrat uživatele nebo instanční objekty bez opětovného použití Seznamů ACL pro celou strukturu adresáře. Jenom je třeba přidat nebo odebrat uživatele z příslušné skupiny zabezpečení Azure AD. Seznamy ACL nejsou zděděny, takže opětovné použití seznamů ACL vyžaduje aktualizaci seznamu ACL v každém souboru a podadresáři.

## <a name="access-files-from-the-cluster"></a>Přístup k souborům z clusteru

Existuje několik způsobů, jak získat přístup k souborům v datovém úložišti Data Lake Storage Gen2 z clusteru HDInsight.

* **Pomocí plně kvalifikovaného názvu**. S tímto přístupem zadáváte úplnou cestu k souboru, ke kterému chcete získat přístup.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Pomocí zkráceného formátu cesty**. Pomocí tohoto přístupu nahradíte cestu až ke kořenovému adresáři clusteru:

    ```
    abfs:///<file.path>/
    ```

* **Pomocí relativní cesty**. S tímto přístupem zadáváte pouze relativní cestu k souboru, ke kterému chcete získat přístup.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Příklady přístupu k datům

Příklady jsou založeny na [ssh připojení](./hdinsight-hadoop-linux-use-ssh-unix.md) k hlavnímu uzlu clusteru. Příklady používají všechna tři schémata URI. Nahraďte `CONTAINERNAME` a `STORAGEACCOUNT` s příslušnými hodnotami

#### <a name="a-few-hdfs-commands"></a>Několik příkazů hdfs

1. Vytvořte soubor v místním úložišti.

    ```bash
    touch testFile.txt
    ```

1. Vytvořte adresáře v clusterovém úložišti.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Zkopírujte data z místního úložiště do clusterového úložiště.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Seznam obsahu adresáře v úložišti clusteru.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Vytvoření tabulky Podregistru

Pro ilustrativní účely jsou zobrazena tři umístění souborů. Pro skutečné spuštění použijte pouze `LOCATION` jednu z položek.

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

* [Integrace Azure HDInsight s náhledem Data Lake Storage Gen2 – ACL a aktualizace zabezpečení](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Úvod do úložiště datových jezer Azure Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Kurz: Extrahování, transformace a načítání dat pomocí interaktivního dotazu v Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
