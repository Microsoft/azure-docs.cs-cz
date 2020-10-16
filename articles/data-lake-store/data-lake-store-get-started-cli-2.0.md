---
title: Správa účtu Azure Data Lake Storage Gen1 – Azure CLI
description: Pomocí rozhraní příkazového řádku Azure můžete vytvořit účet Data Lake Storage Gen1 a provádět základní operace.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: de98e25cf5703a43282e551a0eda20d7767c6ce8
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2020
ms.locfileid: "92103624"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-cli"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí Azure CLI

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Azure Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Naučte se používat Azure CLI k vytvoření účtu Azure Data Lake Storage Gen1 a provádění základních operací, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace o Data Lake Storage Gen1 najdete v tématu [přehled Data Lake Storage Gen1](data-lake-store-overview.md).

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Je možné používat ho v systémech macOS, Linux a Windows. Další informace najdete v tématu [Přehled Azure CLI](/cli/azure). Úplný seznam příkazů a syntaxe můžete zobrazit také v [referenčních informacích](/cli/azure/dls) k rozhraní příkazového řádku Azure Data Lake Storage Gen1.


## <a name="prerequisites"></a>Předpoklady
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** – pokyny najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

## <a name="authentication"></a>Authentication

Tento článek používá jednodušší přístup k ověřování s Data Lake Storage Gen1, ke kterému se přihlašujete jako uživatel s koncovým uživatelem. Úroveň přístupu k účtu Data Lake Storage Gen1 a systému souborů se pak řídí úrovní přístupu přihlášeného uživatele. Existují však i další přístupy k ověřování pomocí Data Lake Storage Gen1, což jsou **ověřování koncového uživatele** nebo **ověřování služba-služba**. Pokyny a další informace o ověřování najdete v tématu [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md) nebo [Ověřování služba-služba](./data-lake-store-service-to-service-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

1. Přihlaste se ke svému předplatnému Azure.

    ```azurecli
    az login
    ```

    Obdržíte kód pro použití v dalším kroku. Pomocí webového prohlížeče otevřete stránku https://aka.ms/devicelogin a zadejte kód pro ověření. Zobrazí se výzva k přihlášení pomocí vašich přihlašovacích údajů.

2. Po přihlášení se v okně zobrazí všechna předplatná Azure, která jsou přidružena k vašemu účtu. Chcete-li používat konkrétní předplatné, použijte následující příkaz.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-storage-gen1-account"></a>Vytvoření účtu Azure Data Lake Storage Gen1

1. Vytvoříte novou skupinu prostředků. V následujícím příkazu zadejte hodnoty parametrů, které chcete použít. Pokud název umístění obsahuje mezery, dejte ho do uvozovek. Například „East US 2“. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Vytvořte účet Data Lake Storage Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Vytváření složek v účtu Data Lake Storage Gen1

Můžete vytvořit složky pod účtem Azure Data Lake Storage Gen1, abyste mohli spravovat a ukládat data. Pomocí následujícího příkazu vytvořte složku s názvem **mynewfolder** v kořenovém adresáři Data Lake Storage Gen1 účtu.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Parametr `--folder` zajistí, že příkaz vytvoří složku. Pokud tento parametr není k dispozici, příkaz vytvoří prázdný soubor s názvem mynewfolder v kořenovém adresáři Data Lake Storage Gen1 účtu.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Nahrajte data na účet Data Lake Storage Gen1.

Data můžete do Data Lake Storage Gen1 nahrát přímo na úrovni kořenového adresáře nebo do složky, kterou jste v rámci účtu vytvořili. Níže zobrazené fragmenty kódu ukazují, jak nahrát ukázková data do složky (**mynewfolder**), kterou jste vytvořili v předchozí části.

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Stáhněte si tento soubor a uložte ho do místního adresáře v počítači, například C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Jako cíl je třeba zadat úplnou cestu včetně názvu souboru.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Výpis souborů v Data Lake Storage Gen1 účtu

Pomocí následujícího příkazu můžete zobrazit seznam souborů v účtu Data Lake Storage Gen1.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
```

Výstup by měl vypadat přibližně takto:

```output
[
    {
        "accessTime": 1491323529542,
        "aclBit": false,
        "blockSize": 268435456,
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "length": 1589881,
        "modificationTime": 1491323531638,
        "msExpirationTime": 0,
        "name": "mynewfolder/vehicle1_09142014.csv",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "pathSuffix": "vehicle1_09142014.csv",
        "permission": "770",
        "replication": 1,
        "type": "FILE"
    }
]
```

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Přejmenování, stažení a odstranění dat z Data Lake Storage Gen1 účtu 

* Pokud **chcete přejmenovat soubor**, použijte následující příkaz:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **K stažení souboru**použijte následující příkaz. Ujistěte se, že zadaná cílová cesta už existuje.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Příkaz vytvoří cílovou složku, pokud neexistuje.
    > 
    >

* **Chcete-li odstranit soubor**, použijte následující příkaz:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Pokud chcete odstranit složku **mynewfolder** a soubor **vehicle1_09142014_copy.csv** společně v jednom příkazu, použijte parametr --recurse.

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Práce s oprávněními a seznamy ACL pro účet Data Lake Storage Gen1

V této části se dozvíte, jak spravovat seznamy ACL a oprávnění pomocí Azure CLI. Podrobné informace o způsobu implementace seznamů ACL v Azure Data Lake Storage Gen1 najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

* **Chcete-li aktualizovat vlastníka souboru nebo složky**, použijte následující příkaz:

    ```azurecli
    az dls fs access set-owner --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Chcete-li aktualizovat oprávnění pro soubor nebo složku**, použijte následující příkaz:

    ```azurecli
    az dls fs access set-permission --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Chcete-li získat seznamy ACL pro danou cestu**, použijte následující příkaz:

    ```azurecli
    az dls fs access show --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014.csv
    ```

    Výstup by měl vypadat přibližně takto:

    ```output
    {
        "entries": [
        "user::rwx",
        "group::rwx",
        "other::---"
        ],
        "group": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "owner": "1808bd5f-62af-45f4-89d8-03c5e81bac20",
        "permission": "770",
        "stickyBit": false
    }
    ```

* **Chcete-li nastavit položku pro seznam ACL**, použijte následující příkaz:

    ```azurecli
    az dls fs access set-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Chcete-li odebrat položku pro seznam ACL**, použijte následující příkaz:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestoragegen1 --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Chcete-li odebrat celý výchozí seznam ACL**, použijte následující příkaz:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder --default-acl
    ```

* **Chcete-li odebrat celý seznam ACL, který není výchozí**, použijte následující příkaz:

    ```azurecli
    az dls fs access remove-all --account mydatalakestoragegen1 --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Odstranit účet Data Lake Storage Gen1
Pomocí následujícího příkazu odstraňte účet Data Lake Storage Gen1.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Po zobrazení výzvy zadejte **Y**, a účet tak odstraňte.

## <a name="next-steps"></a>Další kroky
* [Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)