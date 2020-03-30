---
title: Začínáme s Azure Data Lake Storage Gen1 – PowerShell | Dokumenty společnosti Microsoft
description: Pomocí Azure PowerShellu můžete vytvořit účet Azure Data Lake Storage Gen1 a provést základní operace.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 42ddab6991b418af3e41da9966cdab69ded87461
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73837896"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí Azure PowerShellu

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Naučte se používat Azure PowerShell k vytvoření účtu Azure Data Lake Storage Gen1 a provádět základní operace, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace o úložišti datových jezer Gen1 najdete v [tématu Přehled úložiště datových jezer Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Ověřování

Tento článek používá jednodušší přístup ověřování s Data Lake Storage Gen1, kde budete vyzváni k zadání přihlašovacích údajů účtu Azure. Úroveň přístupu k účtu Data Lake Storage Gen1 a systému souborů se pak řídí úrovní přístupu přihlášeného uživatele. Existují však i jiné přístupy k ověřování pomocí Data Lake Storage Gen1, které jsou ověřování koncovým uživatelem nebo ověřování služby služby. Pokyny a další informace o ověřování najdete v tématu [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md) nebo [Ověřování služba-služba](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1

1. Otevřete na ploše nové okno Windows PowerShellu. Zadejte následující úryvek pro přihlášení ke svému účtu Azure, nastavte předplatné a zaregistrujte poskytovatele Data Lake Storage Gen1. Po zobrazení výzvy k přihlášení se ujistěte, že se přihlásíte jako jeden ze správců/vlastníků předplatného:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Účet Gen1 úložiště datového jezera je přidružený ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Vytvoření skupiny prostředků Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Vytvoření skupiny prostředků Azure")

1. Vytvořte účet Data Lake Storage Gen1. Zadaný název musí obsahovat jenom malá písmena a číslice.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Vytvoření účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Vytvoření účtu Data Lake Storage Gen1")

1. Ověřte, že se účet úspěšně vytvořil.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    Výstup této rutiny by měl být **True** (pravda).

## <a name="create-directory-structures"></a>Vytvoření adresářových struktur

Můžete vytvořit adresáře pod účtem Data Lake Storage Gen1 pro správu a ukládání dat.

1. Zadejte kořenový adresář.

    ```PowerShell
    $myrootdir = "/"
    ```

1. V zadaném kořenovém adresáři vytvořte nový adresář s názvem **mynewdirectory**.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Ověřte, že se nový adresář úspěšně vytvořil.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    Měl by se zobrazit výstup jako na následujícím snímku obrazovky:

    ![Ověření adresáře](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Ověření adresáře")

## <a name="upload-data"></a>Nahrání dat

Data můžete nahrát do data Lake Storage Gen1 přímo na kořenové úrovni nebo do adresáře, který jste vytvořili v rámci účtu. Fragmenty kódu v této části ukazují, jak nahrát ukázková data do adresáře (**mynewdirectory**), který jste vytvořili v předchozí části.

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Stáhněte si tento soubor a uložte ho do místního adresáře v počítači, například C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Přejmenování, stažení a odstranění dat

Pokud chcete přejmenovat soubor, použijte tento příkaz:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Pokud chcete stáhnout soubor, použijte tento příkaz:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Pokud chcete odstranit soubor, použijte tento příkaz:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Po zobrazení výzvy zadejte **Y**, a položku tak odstraňte. Pokud chcete odstranit více souborů, můžete zadat všechny požadované cesty oddělené čárkou.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Odstranit účet

Pomocí následujícího příkazu odstraňte svůj účet Data Lake Storage Gen1.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Po zobrazení výzvy zadejte **Y**, a účet tak odstraňte.

## <a name="next-steps"></a>Další kroky

* [Pokyny pro ladění výkonu pro použití PowerShellu s Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](data-lake-store-data-scenarios.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
