---
title: Vytvoření sdílené složky Azure | Microsoft Docs
description: Postup vytvoření sdílené složky Azure ve službě Soubory Azure pomocí webu Azure Portal, PowerShellu a Azure CLI.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.component: files
ms.openlocfilehash: 83829264f16fb295a1f5fa4f2efc74d8b35ec6eb
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2018
ms.locfileid: "52309187"
---
# <a name="create-a-file-share-in-azure-files"></a>Vytvoření sdílené složky ve službě Soubory Azure
Můžete vytvořit sdílené složky Azure pomocí [webu Azure portal](https://portal.azure.com/), rutin Powershellu pro Azure Storage, klientských knihoven Azure Storage nebo REST API služby Azure Storage. V tomto kurzu se dozvíte:
* [Jak vytvořit sdílenou složku Azure pomocí webu Azure Portal](#create-file-share-through-the-azure-portal)
* [Jak vytvořit sdílenou složku Azure pomocí PowerShellu](#create-file-share-through-powershell)
* [Postup vytvoření sdílené složky Azure pomocí rozhraní příkazového řádku](#create-file-share-through-command-line-interface-cli)

## <a name="prerequisites"></a>Požadavky
Pokud chcete vytvořit sdílenou složku Azure, můžete použít už existující účet úložiště nebo [vytvořit nový účet služby Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json). Pokud chcete vytvořit sdílenou složku Azure pomocí PowerShellu, budete potřebovat klíč účtu a název účtu úložiště. Pokud máte v plánu pomocí Powershellu nebo rozhraní příkazového řádku, budete potřebovat klíč účtu úložiště.

## <a name="create-a-file-share-through-the-azure-portal"></a>Vytvoření sdílené složky prostřednictvím webu Azure portal
1. **Přejděte do okna účet úložiště na webu Azure portal**:    
    ![Okno Účet úložiště](./media/storage-how-to-create-file-share/create-file-share-portal1.png)

2. **Klikněte na tlačítko Přidat sdílenou složku:**    
    ![Kliknutí na tlačítko Přidat sdílenou složku](./media/storage-how-to-create-file-share/create-file-share-portal2.png)

3. **Zadejte název a kvótu. Kvóta pro aktuální maximální hodnota je 5 TiB**:    
    ![Zadání názvu a požadované kvóty pro novou sdílenou složku](./media/storage-how-to-create-file-share/create-file-share-portal3.png)

4. **Zobrazte novou sdílenou složku:**![Zobrazení nové sdílené složky](./media/storage-how-to-create-file-share/create-file-share-portal4.png)

5. **Nahrajte soubor:**![Nahrání souboru](./media/storage-how-to-create-file-share/create-file-share-portal5.png)

6. **Přejděte do sdílené složky a spravujte adresáře a soubory:**![Procházení sdílené složky](./media/storage-how-to-create-file-share/create-file-share-portal6.png)


## <a name="create-file-share-through-powershell"></a>Vytvoření sdílené složky prostřednictvím PowerShellu
K používání PowerShellu budete potřebovat stáhnout a nainstalovat rutiny modulu Azure PowerShell. Zobrazit [instalace a konfigurace Azure Powershellu](https://azure.microsoft.com/documentation/articles/powershell-install-configure/) postup instalace bodu a instalaci.

> [!Note]  
> Doporučuje se stáhnout a nainstalovat nebo upgradovat na nejnovější modul Azure PowerShell.

1. **Vytvořte kontext pro účet úložiště a klíč.** Kontext obsahuje název účtu úložiště a klíč účtu. Pokyny pro zkopírování klíče účtu z [webu Azure portal](https://portal.azure.com/), naleznete v tématu [přístupové klíče účtu úložiště](../common/storage-account-manage.md#access-keys).

    ```powershell
    $storageContext = New-AzureStorageContext <storage-account-name> <storage-account-key>
    ```
    
2. **Vytvořte novou sdílenou složku:**    
    
    ```powershell
    $share = New-AzureStorageShare logs -Context $storageContext
    ```

> [!Note]  
> Název vaší sdílené složky musí obsahovat jen malá písmena. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [pojmenování a odkazování na sdílené, adresářů, souborů a metadat](https://msdn.microsoft.com/library/azure/dn167011.aspx).

## <a name="create-file-share-through-command-line-interface-cli"></a>Vytvoření sdílené složky prostřednictvím rozhraní příkazového řádku (CLI)
1. **Příprava na použití rozhraní příkazového řádku (CLI), stáhněte a nainstalujte Azure CLI.**  
    Zobrazit [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).

2. **Vytvořte připojovací řetězec k účtu úložiště, kde chcete sdílenou složku vytvořit.**  
    Nahraďte ```<storage-account>``` a ```<resource_group>``` s vašeho úložiště účtu a skupinou prostředků v následujícím příkladu:

   ```azurecli
    current_env_conn_string = $(az storage account show-connection-string -n <storage-account> -g <resource-group> --query 'connectionString' -o tsv)

    if [[ $current_env_conn_string == "" ]]; then  
        echo "Couldn't retrieve the connection string."
    fi
    ```

3. **Vytvoření sdílené složky**
    ```azurecli
    az storage share create --name files --quota 2048 --connection-string $current_env_conn_string 1 > /dev/null
    ```

## <a name="next-steps"></a>Další postup
* [Připojení ke sdílené složce a její připojení – Windows](storage-how-to-use-files-windows.md)
* [Připojení ke sdílené složce a její připojení – Linux](../storage-how-to-use-files-linux.md)
* [Připojení ke sdílené složce a její připojení – macOS](storage-how-to-use-files-mac.md)

Další informace o službě Soubory Azure najdete na těchto odkazech.

* [Nejčastější dotazy](../storage-files-faq.md)
* [Řešení potíží ve Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Řešení potíží v Linuxu](storage-troubleshoot-linux-file-connection-problems.md)   
