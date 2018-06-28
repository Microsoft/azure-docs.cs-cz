---
title: Pomocí příkazového řádku Azure 2.0 rozhraní začít s Azure Data Lake Storage Gen1 | Microsoft Docs
description: Použití multiplatformního rozhraní příkazového řádku Azure 2.0 k vytvoření účtu Data Lake Store a provádění základních operací
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: acd1182fdc66374e9abbc4964207417a7de3aadb
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035840"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-cli-20"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí Azure CLI 2.0
> [!div class="op_single_selector"]
> * [Azure Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Naučte se používat rozhraní příkazového řádku Azure CLI 2.0 k vytvoření účtu Azure Data Lake Store a provádění základních operací, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace o Data Lake Store najdete v tématu [přehled o Data Lake Storage Gen1](data-lake-store-overview.md).

Rozhraní příkazového řádku Azure CLI 2.0 představuje nové prostředí příkazového řádku Azure pro správu prostředků Azure. Je možné používat ho v systémech macOS, Linux a Windows. Další informace najdete v [přehledu rozhraní příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure). Můžete si také prostudovat [referenční informace k rozhraní příkazového řádku Azure Data Lake Store CLI 2.0](https://docs.microsoft.com/cli/azure/dls) obsahující úplný seznam příkazů a syntaxi.


## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Rozhraní příkazového řádku Azure CLI 2.0** – Pokyny najdete v článku [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="authentication"></a>Authentication

Tento článek využívá jednodušší přístup ověřování ve službě Data Lake Store, kdy se přihlašujete jako koncový uživatel. Úroveň přístupu k účtu služby Data Lake Store a systému souborů se pak řídí úrovní přístupu přihlášeného uživatele. Existují však i jiné přístupy k ověřování ve službě Data Lake Store. Je to **ověřování koncového uživatele** nebo **ověřování služba-služba**. Pokyny a další informace o ověřování najdete v tématu [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md) nebo [Ověřování služba-služba](data-lake-store-authenticate-using-active-directory.md).


## <a name="log-in-to-your-azure-subscription"></a>Přihlášení k předplatnému Azure

1. Přihlaste se ke svému předplatnému Azure.

    ```azurecli
    az login
    ```

    Obdržíte kód pro použití v dalším kroku. Použít webový prohlížeč otevře se stránka https://aka.ms/devicelogin a zadejte kód pro ověření. Zobrazí se výzva k přihlášení pomocí vašich přihlašovacích údajů.

2. Po přihlášení se v okně zobrazí všechna předplatná Azure, která jsou přidružena k vašemu účtu. Chcete-li používat konkrétní předplatné, použijte následující příkaz.
   
    ```azurecli
    az account set --subscription <subscription id> 
    ```

## <a name="create-an-azure-data-lake-store-account"></a>Vytvoření účtu Azure Data Lake Store

1. Vytvořte novou skupinu prostředků. V následujícím příkazu zadejte hodnoty parametrů, které chcete použít. Pokud název umístění obsahuje mezery, dejte ho do uvozovek. Například „East US 2“. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Vytvořte účet Data Lake Store.
   
    ```azurecli
    az dls account create --account mydatalakestore --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-store-account"></a>Vytváření složek v účtu Data Lake Store

V rámci účtu Azure Data Lake Store můžete vytvářet složky, které slouží ke správě a ukládání dat. Následujícím příkazem vytvořte v kořenovém adresáři Data Lake Store složku s názvem **mynewfolder**.

```azurecli
az dls fs create --account mydatalakestore --path /mynewfolder --folder
```

> [!NOTE]
> Parametr `--folder` zajistí, že příkaz vytvoří složku. Pokud není tento parametr zadán, příkaz vytvoří prázdný soubor s názvem mynewfolder v kořenovém adresáři účtu Data Lake Store.
> 
>

## <a name="upload-data-to-a-data-lake-store-account"></a>Nahrání dat do účtu Data Lake Store

Data můžete do Data Lake Store nahrát přímo na úrovni kořenového adresáře nebo do složky, kterou jste v rámci účtu vytvořili. Níže zobrazené fragmenty kódu ukazují, jak nahrát ukázková data do složky (**mynewfolder**), kterou jste vytvořili v předchozí části.

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Stáhněte si tento soubor a uložte ho do místního adresáře v počítači, například C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestore --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Jako cíl je třeba zadat úplnou cestu včetně názvu souboru.
> 
>


## <a name="list-files-in-a-data-lake-store-account"></a>Zobrazení seznamu souborů v účtu Data Lake Store

Následujícím příkazem zobrazte seznam souborů v účtu Data Lake Store.

```azurecli
az dls fs list --account mydatalakestore --path /mynewfolder
```

Výstup by měl vypadat přibližně takto:

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

## <a name="rename-download-and-delete-data-from-a-data-lake-store-account"></a>Přejmenování, stažení a odstranění dat z účtu Data Lake Store 

* **Pokud chcete přejmenovat soubor**, použijte tento příkaz:
  
    ```azurecli
    az dls fs move --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Pokud chcete stáhnout soubor**, použijte tento příkaz: Ujistěte se, že zadaná cílová cesta už existuje.
  
    ```azurecli     
    az dls fs download --account mydatalakestore --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Příkaz vytvoří cílovou složku, pokud neexistuje.
    > 
    >

* **Pokud chcete odstranit soubor**, použijte tento příkaz:
  
    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Pokud chcete odstranit složku **mynewfolder** a soubor **vehicle1_09142014_copy.csv** společně v jednom příkazu, použijte parametr --recurse.

    ```azurecli
    az dls fs delete --account mydatalakestore --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-store-account"></a>Práce s oprávněními a seznamy řízení přístupu (ACL) pro účet Data Lake Store

V této části se naučíte spravovat seznamy řízení přístupu a oprávnění prostřednictvím rozhraní příkazového řádku Azure CLI 2.0. Podrobný rozbor implementace seznamů řízení přístupu v Azure Data Lake Store najdete v článku [Řízení přístupu v Azure Data Lake Store](data-lake-store-access-control.md).

* **Chcete-li aktualizovat vlastníka souboru nebo složky**, použijte následující příkaz:

    ```azurecli
    az dls fs access set-owner --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --group 80a3ed5f-959e-4696-ba3c-d3c8b2db6766 --owner 6361e05d-c381-4275-a932-5535806bb323
    ```

* **Chcete-li aktualizovat oprávnění pro soubor nebo složku**, použijte následující příkaz:

    ```azurecli
    az dls fs access set-permission --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv --permission 777
    ```
    
* **Chcete-li získat seznamy ACL pro danou cestu**, použijte následující příkaz:

    ```azurecli
    az dls fs access show --account mydatalakestore --path /mynewfolder/vehicle1_09142014.csv
    ```

    Výstup by měl vypadat přibližně takto:

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

* **Chcete-li nastavit položku pro seznam ACL**, použijte následující příkaz:

    ```azurecli
    az dls fs access set-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323:-w-
    ```

* **Chcete-li odebrat položku pro seznam ACL**, použijte následující příkaz:

    ```azurecli
    az dls fs access remove-entry --account mydatalakestore --path /mynewfolder --acl-spec user:6360e05d-c381-4275-a932-5535806bb323
    ```

* **Chcete-li odebrat celý výchozí seznam ACL**, použijte následující příkaz:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder --default-acl
    ```

* **Chcete-li odebrat celý seznam ACL, který není výchozí**, použijte následující příkaz:

    ```azurecli
    az dls fs access remove-all --account mydatalakestore --path /mynewfolder
    ```
    
## <a name="delete-a-data-lake-store-account"></a>Odstranění účtu Data Lake Store
Následujícím příkazem odstraňte účet Data Lake Store.

```azurecli
az dls account delete --account mydatalakestore
```

Po zobrazení výzvy zadejte **Y**, a účet tak odstraňte.

## <a name="next-steps"></a>Další postup
* [Použití Azure Data Lake Store pro potřeby velkého objemu dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
