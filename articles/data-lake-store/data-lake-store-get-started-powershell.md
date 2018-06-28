---
title: Začínáme s Azure Data Lake Storage Gen1 pomocí prostředí PowerShell | Microsoft Docs
description: Použití prostředí Azure PowerShell k vytvoření účtu Data Lake Store a provádění základních operací
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: f208722d768e2bccf2e5b4d7b4543f8cbba4f185
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035852"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí Azure PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Naučte se používat Azure PowerShell k vytvoření účtu Azure Data Lake Store a provádění základních operací, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace o Data Lake Store najdete v tématu [přehled o Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 nebo vyšší**. Viz téma [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Authentication
Tento článek používá jednodušší přístup k ověřování ve službě Data Lake Store, kdy jste vyzváni k zadání svých pověření pro účet Azure. Úroveň přístupu k účtu služby Data Lake Store a systému souborů se pak řídí úrovní přístupu přihlášeného uživatele. Existují však i jiné přístupy k ověřování ve službě Data Lake Store. Je to **ověřování koncového uživatele** nebo **ověřování služba-služba**. Pokyny a další informace o ověřování najdete v tématu [Ověřování koncových uživatelů](data-lake-store-end-user-authenticate-using-active-directory.md) nebo [Ověřování služba-služba](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Vytvoření účtu Azure Data Lake Store
1. Otevřete na ploše nové okno Windows PowerShellu. Zadejte následující fragment kódu a přihlaste se tak k účtu Azure. Nastavte předplatné a zaregistrujte poskytovatele Data Lake Store. Po zobrazení výzvy k přihlášení se nezapomeňte přihlásit jako jeden ze správců / vlastník předplatného:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Účet Azure Data Lake Store je přidružený ke skupině prostředků Azure. Začněte vytvořením skupiny prostředků Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Vytvoření skupiny prostředků Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Vytvoření skupiny prostředků Azure")
3. Vytvořte účet Azure Data Lake Store. Zadaný název musí obsahovat jenom malá písmena a číslice.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Vytvoření účtu Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Vytvoření účtu Azure Data Lake Store")
4. Ověřte, že se účet úspěšně vytvořil.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Výstup této rutiny by měl být **True** (pravda).

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Vytváření struktur adresářů v Azure Data Lake Store
V rámci účtu Azure Data Lake Store můžete vytvářet adresáře, které slouží ke správě a ukládání dat.

1. Zadejte kořenový adresář.

        $myrootdir = "/"
2. V zadaném kořenovém adresáři vytvořte nový adresář s názvem **mynewdirectory**.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Ověřte, že se nový adresář úspěšně vytvořil.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Měl by se zobrazit výstup jako na následujícím snímku obrazovky:

    ![Ověření adresáře](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Ověření adresáře")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Nahrání dat do Azure Data Lake Store
Data můžete do služby Data Lake Store nahrát přímo na úrovni kořenového adresáře nebo do adresáře, který jste v rámci účtu vytvořili. Fragmenty kódu v této části ukazují, jak nahrát ukázková data do adresáře (**mynewdirectory**), který jste vytvořili v předchozí části.

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Stáhněte si tento soubor a uložte ho do místního adresáře v počítači, například C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Přejmenování, stažení a odstranění dat z Data Lake Store
Pokud chcete přejmenovat soubor, použijte tento příkaz:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Pokud chcete stáhnout soubor, použijte tento příkaz:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Pokud chcete odstranit soubor, použijte tento příkaz:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Po zobrazení výzvy zadejte **Y**, a položku tak odstraňte. Pokud chcete odstranit více souborů, můžete zadat všechny požadované cesty oddělené čárkou.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Odstranění účtu Azure Data Lake Store
Následujícím příkazem odstraňte účet Data Lake Store.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Po zobrazení výzvy zadejte **Y**, a účet tak odstraňte.

## <a name="next-steps"></a>Další postup
* [Průvodce laděním výkonu pro použití PowerShellu se službou Azure Data Lake Store](data-lake-store-performance-tuning-powershell.md)
* [Použití Azure Data Lake Store pro potřeby velkého objemu dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
