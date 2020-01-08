---
title: Vytvoření sdílené složky Azure
titleSuffix: Azure Files
description: Postup vytvoření sdílené složky Azure pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ca4904940c7974e83445892b9597a9d3c36b44ee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452879"
---
# <a name="create-a-file-share"></a>Vytvoření sdílené složky
Můžete vytvořit sdílenou složku ve službě soubory Azure pomocí [Azure Portal](https://portal.azure.com/), rutin Azure Storage PowerShellu, Azure Storage klientských knihoven nebo Azure Storage REST API. V tomto článku se dozvíte, jak:
- Vytvoření sdílené složky Azure pomocí Azure Portal
- Vytvoření sdílené složky Azure pomocí PowerShellu
- Vytvoření sdílené složky Azure pomocí Azure CLI

## <a name="prerequisites"></a>Požadavky
Pokud chcete vytvořit sdílenou složku Azure, můžete použít účet úložiště, který už existuje, nebo [vytvořit nový účet úložiště Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud chcete vytvořit sdílenou složku Azure pomocí PowerShellu, budete potřebovat klíč účtu a název svého účtu úložiště. Pokud plánujete použít PowerShell nebo rozhraní příkazového řádku, budete potřebovat klíč účtu úložiště.

> [!NOTE]
> Pokud chcete vytvořit sdílené složky větší než 5 TiB, přečtěte si téma [Povolení velkých sdílených složek](storage-files-how-to-create-large-file-share.md).

## <a name="create-a-file-share-through-the-azure-portal"></a>Vytvoření sdílené složky pomocí Azure Portal

1. V Azure Portal otevřete podokno **účet úložiště** .
    ![podokno účtu úložiště](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

1. Klikněte na tlačítko **+ sdílet soubor** .
    ![tlačítko Přidat sdílení souborů](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

1. Zadejte informace pro **název** a **kvótu**.
    ![název a kvótu pro novou sdílenou složku](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

1. Podívejte se na novou sdílenou složku.
    ![novou sdílenou složku v uživatelském rozhraní](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

1. Nahrajte soubor.
    ![tlačítko nahrát na navigačním panelu](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

1. Přejděte do sdílené složky a spravujte své adresáře a soubory.
    zobrazení složky ![sdílených složek](./media/storage-how-to-create-file-share/create-file-share-portal6.png)

## <a name="create-a-file-share-through-powershell"></a>Vytvoření sdílené složky prostřednictvím PowerShellu

Stáhněte a nainstalujte rutiny Azure PowerShell. Pokyny k instalaci a instalaci najdete v tématu [instalace a konfigurace Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) .

> [!Note]  
> Doporučujeme stáhnout a nainstalovat nebo upgradovat na nejnovější modul Azure PowerShell.

1. Vytvořte nový účet úložiště.
    Účet úložiště je sdílený fond úložiště, ve kterém můžete nasazovat sdílené složky Azure a prostředky úložiště, jako jsou objekty blob nebo fronty.

    ```PowerShell
    $resourceGroup = "myresourcegroup"
    $storAcctName = "myuniquestorageaccount"
    $region = "westus2"
    $storAcct = New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storAcctName -SkuName Standard_LRS -Location $region -Kind StorageV2
    ```

1. Vytvořte novou sdílenou složku.        
    ```PowerShell
    $shareName = "myshare"
    $share = New-AzStorageShare -Context $storAcct.Context -Name $shareName
    ```

> [!Note]  
> Název vaší sdílené složky musí obsahovat jen malá písmena. Podrobné informace o pojmenovávání sdílených složek a souborů najdete v tématu [pojmenování a odkazování na sdílené složky, adresáře, soubory a metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-a-file-share-through-the-cli"></a>Vytvoření sdílené složky prostřednictvím rozhraní příkazového řádku

1. Stáhněte a nainstalujte rozhraní příkazového řádku Azure CLI.
    Přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

1. Vytvořte připojovací řetězec k účtu úložiště, ve kterém chcete vytvořit sdílenou složku.
    V následujícím příkladu nahraďte ```<storage-account>``` a ```<resource_group>``` s názvem svého účtu úložiště a skupinou prostředků:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

1. Vytvořte sdílenou složku.
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Další kroky

* [Připojení a připojení sdílené složky ve Windows](storage-how-to-use-files-windows.md)
* [Připojení a připojení sdílené složky v systému Linux](storage-how-to-use-files-linux.md)
* [Připojení a připojení sdílené složky na macOS](storage-how-to-use-files-mac.md)

Další informace o službě Soubory Azure najdete na těchto odkazech:

* [Soubory úložiště – nejčastější dotazy](storage-files-faq.md)
* [Řešení potíží se soubory Azure ve Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Řešení potíží se soubory Azure v systému Linux](storage-troubleshoot-linux-file-connection-problems.md)
