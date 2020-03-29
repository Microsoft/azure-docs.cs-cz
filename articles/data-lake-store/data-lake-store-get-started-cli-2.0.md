---
title: Využijte azure cli a můžete začít s Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: Použití nastavení příkazu Konstat Azure k vytvoření účtu Data Lake Storage Gen1 a provedení základních operací
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 9431cc7fa12b86371ce6b2325aca8e13d264442e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60885342"
---
# <a name="get-started-with-azure-data-lake-store-using-azure-cli"></a>Začínáme s Azure Data Lake Store pomocí Azure CLI

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

Naučte se používat rozhraní příkazového příkazového nastavení Azure k vytvoření účtu Azure Data Lake Storage Gen1 a provádět základní operace, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace o úložišti datových jezer Gen1 najdete v [tématu Přehled úložiště datových jezer Gen1](data-lake-store-overview.md).

Azure CLI je prostředí příkazového řádku Azure pro správu prostředků Azure. Je možné používat ho v systémech macOS, Linux a Windows. Další informace najdete [v tématu Přehled azure cli](https://docs.microsoft.com/cli/azure). Můžete se také podívat na [odkaz Azure Data Lake Storage Gen1 CLI](https://docs.microsoft.com/cli/azure/dls) pro úplný seznam příkazů a syntaxe.


## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto článku měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure CLI** – pokyny najdete [v tématu Instalace příkazového příkazového příkazu k Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="authentication"></a>Ověřování

Tento článek používá jednodušší přístup ověřování s Data Lake Storage Gen1, kde se přihlásíte jako koncový uživatel. Úroveň přístupu k účtu Data Lake Storage Gen1 a systému souborů se pak řídí úrovní přístupu přihlášeného uživatele. Existují však i jiné přístupy k ověření pomocí data lake storage gen1, které jsou **ověřování koncovým uživatelem** nebo **ověřování služby služby**. Pokyny a další informace o ověřování najdete v tématu [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md) nebo [Ověřování služba-služba](data-lake-store-authenticate-using-active-directory.md).


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

1. Vytvořte novou skupinu prostředků. V následujícím příkazu zadejte hodnoty parametrů, které chcete použít. Pokud název umístění obsahuje mezery, dejte ho do uvozovek. Například „East US 2“. 
   
    ```azurecli
    az group create --location "East US 2" --name myresourcegroup
    ```

2. Vytvořte účet Data Lake Storage Gen1.
   
    ```azurecli
    az dls account create --account mydatalakestoragegen1 --resource-group myresourcegroup
    ```

## <a name="create-folders-in-a-data-lake-storage-gen1-account"></a>Vytvoření složek v účtu Data Lake Storage Gen1

Můžete vytvořit složky pod účtem Azure Data Lake Storage Gen1 pro správu a ukládání dat. Pomocí následujícího příkazu vytvořte složku s názvem **mynewfolder** v kořenovém adresáři účtu Data Lake Storage Gen1.

```azurecli
az dls fs create --account mydatalakestoragegen1 --path /mynewfolder --folder
```

> [!NOTE]
> Parametr `--folder` zajistí, že příkaz vytvoří složku. Pokud tento parametr není k dispozici, příkaz vytvoří prázdný soubor s názvem mynewfolder v kořenovém adresáři účtu Data Lake Storage Gen1.
> 
>

## <a name="upload-data-to-a-data-lake-storage-gen1-account"></a>Nahrání dat do účtu Data Lake Storage Gen1

Data můžete nahrát do data Lake Storage Gen1 přímo na kořenové úrovni nebo do složky, kterou jste vytvořili v rámci účtu. Níže zobrazené fragmenty kódu ukazují, jak nahrát ukázková data do složky (**mynewfolder**), kterou jste vytvořili v předchozí části.

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Stáhněte si tento soubor a uložte ho do místního adresáře v počítači, například C:\sampledata\.

```azurecli
az dls fs upload --account mydatalakestoragegen1 --source-path "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" --destination-path "/mynewfolder/vehicle1_09142014.csv"
```

> [!NOTE]
> Jako cíl je třeba zadat úplnou cestu včetně názvu souboru.
> 
>


## <a name="list-files-in-a-data-lake-storage-gen1-account"></a>Seznam souborů v účtu Data Lake Storage Gen1

Pomocí následujícího příkazu můžete vypsat soubory v účtu Data Lake Storage Gen1.

```azurecli
az dls fs list --account mydatalakestoragegen1 --path /mynewfolder
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

## <a name="rename-download-and-delete-data-from-a-data-lake-storage-gen1-account"></a>Přejmenování, stažení a odstranění dat z účtu Data Lake Storage Gen1 

* **Chcete-li soubor přejmenovat**, použijte následující příkaz:
  
    ```azurecli
    az dls fs move --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014.csv --destination-path /mynewfolder/vehicle1_09142014_copy.csv
    ```

* **Chcete-li stáhnout soubor**, použijte následující příkaz. Ujistěte se, že zadaná cílová cesta už existuje.
  
    ```azurecli     
    az dls fs download --account mydatalakestoragegen1 --source-path /mynewfolder/vehicle1_09142014_copy.csv --destination-path "C:\mysampledata\vehicle1_09142014_copy.csv"
    ```

    > [!NOTE]
    > Příkaz vytvoří cílovou složku, pokud neexistuje.
    > 
    >

* **Chcete-li soubor odstranit**, použijte následující příkaz:
  
    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder/vehicle1_09142014_copy.csv
    ```

    Pokud chcete odstranit složku **mynewfolder** a soubor **vehicle1_09142014_copy.csv** společně v jednom příkazu, použijte parametr --recurse.

    ```azurecli
    az dls fs delete --account mydatalakestoragegen1 --path /mynewfolder --recurse
    ```

## <a name="work-with-permissions-and-acls-for-a-data-lake-storage-gen1-account"></a>Práce s oprávněními a akly pro účet Data Lake Storage Gen1

V této části se dozvíte, jak spravovat seznamy ACL a oprávnění pomocí azure CLI. Podrobná diskuse o tom, jak se implementují seznamy ACL v Azure Data Lake Storage Gen1, najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen1](data-lake-store-access-control.md).

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
    
## <a name="delete-a-data-lake-storage-gen1-account"></a>Odstranění účtu Gen1 úložiště datového jezera
Pomocí následujícího příkazu odstraňte účet Data Lake Storage Gen1.

```azurecli
az dls account delete --account mydatalakestoragegen1
```

Po zobrazení výzvy zadejte **Y**, a účet tak odstraňte.

## <a name="next-steps"></a>Další kroky
* [Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
