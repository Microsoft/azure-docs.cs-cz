---
title: Vytvoření sdílené složky Azure | Microsoft Docs
description: Postup vytvoření sdílené složky Azure ve službě Soubory Azure pomocí webu Azure Portal, PowerShellu a Azure CLI.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e56be394bc7667dfca9a0b417593e8e587073712
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699647"
---
# <a name="create-a-file-share-in-azure-files"></a>Vytvoření sdílené složky ve službě Soubory Azure
Sdílené složky Azure můžete vytvořit pomocí [Azure Portal](https://portal.azure.com/), rutin Azure Storage PowerShellu, Azure Storage klientských knihoven nebo Azure Storage REST API. V tomto kurzu se dozvíte:
* Postup vytvoření sdílené složky Azure pomocí Azure Portal
* [Jak vytvořit sdílenou složku Azure pomocí PowerShellu](#create-file-share-through-powershell)
* [Postup vytvoření sdílené složky Azure pomocí rozhraní příkazového řádku](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Požadavky
Pokud chcete vytvořit sdílenou složku Azure, můžete použít už existující účet úložiště nebo [vytvořit nový účet služby Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud chcete vytvořit sdílenou složku Azure pomocí PowerShellu, budete potřebovat klíč účtu a název účtu úložiště. Pokud plánujete použít PowerShell nebo rozhraní příkazového řádku, budete potřebovat klíč účtu úložiště.

## <a name="create-a-file-share-through-the-azure-portal"></a>Vytvoření sdílené složky pomocí Azure Portal
1. **V Azure Portal otevřete okno účtu úložiště**:    
    ![Okno Účet úložiště](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Klikněte na tlačítko Přidat sdílenou složku:**    
    ![Kliknutí na tlačítko Přidat sdílenou složku](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Zadejte název a kvótu. Aktuální maximální hodnota kvóty je 5 TiB**:    
    ![Zadání názvu a požadované kvóty pro novou sdílenou složku](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Podívejte se na novou sdílenou složku**:  ![Zobrazit novou sdílenou složku](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Nahrát soubor**:  ![Nahrát soubor](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Přejděte do sdílené složky a spravujte své adresáře a soubory**:  ![Procházet sdílení souborů](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Vytvoření sdílené složky prostřednictvím PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

K používání PowerShellu budete potřebovat stáhnout a nainstalovat rutiny modulu Azure PowerShell. Pokyny k instalaci a instalaci najdete v tématu [instalace a konfigurace Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) instalačního bodu.

> [!Note]  
> Doporučuje se stáhnout a nainstalovat nebo upgradovat na nejnovější modul Azure PowerShell.

1. **Vytvořte kontext pro účet úložiště a klíč.** Kontext obsahuje název účtu úložiště a klíč účtu. Pokyny k kopírování klíče účtu z [Azure Portal](https://portal.azure.com/)najdete v tématu [přístupové klíče k účtu úložiště](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Vytvořte novou sdílenou složku:**    
    
    ```powershell
    $share = New-AzStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Název vaší sdílené složky musí obsahovat jen malá písmena. Podrobné informace o pojmenovávání sdílených složek a souborů najdete v tématu pojmenování [a odkazování na sdílené složky, adresáře, soubory a metadata](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Vytvoření sdílené složky prostřednictvím rozhraní příkazového řádku (CLI)
1. **Pro přípravu na použití rozhraní příkazového řádku (CLI) si stáhněte a nainstalujte Azure CLI.**  
    Přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Vytvořte připojovací řetězec k účtu úložiště, kde chcete sdílenou složku vytvořit.**  
    V ```<storage-account>```následujícím ```<resource_group>```příkladu nahraďte a názvem svého účtu úložiště a skupinou prostředků:

   ```azurecli
    current_env_conn_string=$(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Vytvoření sdílené složky**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string > /dev/null
    ```

## <a name="next-steps"></a>Další kroky
* [Připojení ke sdílené složce a její připojení – Windows](storage-how-to-use-files-windows.md)
* [Připojení ke sdílené složce a její připojení – Linux](../storage-how-to-use-files-linux.md)
* [Připojení ke sdílené složce a její připojení – macOS](storage-how-to-use-files-mac.md)

Další informace o službě Soubory Azure najdete na těchto odkazech.

* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží ve Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Řešení potíží v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)   
