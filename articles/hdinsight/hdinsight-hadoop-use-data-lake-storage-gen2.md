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
ms.openlocfilehash: 4ef53b2249f8ce57255c13126c9310f1c889d64f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855051"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Použití služby Azure Data Lake Storage Gen2 s clustery Azure HDInsight

[Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) je cloudová služba úložiště vyhrazená pro analýzy velkých objemů dat, která je založená na službě [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Data Lake Storage Gen2 kombinuje možnosti služby Azure Blob Storage a Azure Data Lake Storage Gen1. Výsledná služba nabízí funkce z Azure Data Lake Storage Gen1 včetně: sémantika systému souborů, zabezpečení na úrovni adresáře a souborů a přizpůsobivost. Společně s nízkými náklady, vrstvené úložiště, vysokou dostupností a možnostmi zotavení po havárii ze služby Azure Blob Storage.

Úplné porovnání možností vytváření clusteru pomocí Data Lake Storage Gen2 najdete v tématu [porovnání možností úložiště pro použití s clustery Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="data-lake-storage-gen2-availability"></a>Dostupnost Data Lake Storage Gen2

Data Lake Storage Gen2 je k dispozici jako možnost úložiště pro téměř všechny typy clusterů Azure HDInsight jako výchozí i pro další účet úložiště. HBA ale můžou mít jenom jeden účet s Data Lake Storage Gen2.

> [!Note]  
> Po výběru Data Lake Storage Gen2 jako **primárního typu úložiště**nemůžete vybrat data Lake Storage Gen1 jako další úložiště.

## <a name="create-hdinsight-clusters-using-data-lake-storage-gen2"></a>Vytváření clusterů HDInsight pomocí Data Lake Storage Gen2

Pomocí následujících odkazů najdete podrobné pokyny k vytvoření clusterů HDInsight s přístupem k Data Lake Storage Gen2.

* [Pomocí portálu](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-portal.md)
* [Použití Azure CLI](../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2-azure-cli.md)
* PowerShell se v současné době nepodporuje pro vytváření clusteru HDInsight s Azure Data Lake Storage Gen2.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Řízení přístupu pro Data Lake Storage Gen2 ve službě HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>Jaké druhy oprávnění podporuje Data Lake Storage Gen2 podporu?

Data Lake Storage Gen2 používá model řízení přístupu, který podporuje řízení přístupu na základě role (RBAC) a seznamy řízení přístupu (ACL) typu POSIX. Data Lake Storage Gen1 podporuje seznamy řízení přístupu pouze pro řízení přístupu k datům.

RBAC pomocí přiřazení rolí efektivně aplikuje sady oprávnění pro uživatele, skupiny a instanční objekty pro prostředky Azure. Tyto prostředky Azure jsou obvykle omezené na prostředky nejvyšší úrovně (například účty Azure Blob Storage). V případě služby Azure Blob Storage a také Data Lake Storage Gen2 tento mechanismus byl rozšířen na prostředek systému souborů.

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