---
title: Spuštění příkazů Azure CLI nebo PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům objektů BLOB nebo Queue
titleSuffix: Azure Storage
description: Azure CLI a PowerShell podporují přihlašování pomocí přihlašovacích údajů Azure AD, aby mohli spouštět příkazy pro Azure Storage objekty BLOB a Queue data. K dispozici je přístupový token pro relaci a slouží k autorizaci operací volání. Oprávnění závisí na roli RBAC přiřazené k objektu zabezpečení Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 07abb9d604c14a5c78a088cb07f57088b84552a6
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74891860"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Spuštění příkazů Azure CLI nebo PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům objektů BLOB nebo Queue

Azure Storage poskytuje rozšíření pro rozhraní příkazového řádku Azure CLI a prostředí PowerShell, které vám umožní přihlašovat a spouštět skriptovací příkazy s přihlašovacími údaji Azure Active Directory (Azure AD). Když se přihlásíte k Azure CLI nebo PowerShellu pomocí přihlašovacích údajů Azure AD, vrátí se přístupový token OAuth 2,0. Tento token je automaticky používá CLI nebo PowerShell k autorizaci následných operací s daty v úložišti objektů BLOB nebo Queue. U podporovaných operací už nemusíte předávat klíč účtu nebo token SAS pomocí příkazu.

Pomocí řízení přístupu na základě role (RBAC) můžete přiřadit k objektu zabezpečení služby Azure AD oprávnění k objektům blob a frontám. Další informace o rolích RBAC v Azure Storage najdete v tématu [Správa přístupových práv k datům Azure Storage pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Podporované operace

Rozšíření jsou podporovaná pro operace na kontejnerech a frontách. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení Azure AD, se kterým se přihlašujete k Azure CLI nebo PowerShellu. Oprávnění pro Azure Storage kontejnery nebo fronty jsou přiřazena prostřednictvím řízení přístupu na základě role (RBAC). Pokud jste například přiřadili roli **čtečky dat objektů BLOB** , můžete spustit skriptovací příkazy, které čtou data z kontejneru nebo fronty. Pokud jste přiřadili roli **Přispěvatel dat objektu BLOB** , můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují kontejner nebo frontu nebo data, která obsahují. 

Podrobnosti o oprávněních potřebných pro jednotlivé operace Azure Storage v kontejneru nebo frontě najdete v tématu [volání operací úložiště s tokeny OAuth](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Volání příkazů rozhraní příkazového řádku pomocí přihlašovacích údajů Azure AD

Rozhraní příkazového řádku Azure podporuje parametr `--auth-mode` pro operace s daty BLOB a Queue:

- Nastavte parametr `--auth-mode`, aby se `login` přihlásil pomocí objektu zabezpečení Azure AD.
- Pokud nejsou k dispozici žádné parametry ověřování pro účet, nastavte parametr `--auth-mode` na hodnotu Legacy `key`, která se má pokusit zadat dotaz na klíč účtu. 

Následující příklad ukazuje, jak vytvořit kontejner v novém účtu úložiště z Azure CLI pomocí vašich přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami: 

1. Ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.46 nebo novější. Spusťte `az --version` a ověřte nainstalovanou verzi.

1. Spusťte `az login` a ověřte v okně prohlížeče: 

    ```azurecli
    az login
    ```

1. Zadejte požadované předplatné. Pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) vytvořte skupinu prostředků. Vytvořte účet úložiště v rámci této skupiny prostředků pomocí [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Než vytvoříte kontejner, přiřaďte roli [Přispěvatel dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště. Další informace o přiřazování rolí RBAC najdete [v tématu udělení přístupu k datům objektů blob Azure a frontě pomocí RBAC v Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Rozšiřování přiřazení rolí RBAC může trvat několik minut.

1. Zavolejte pomocí příkazu [AZ Storage Container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) s parametrem `--auth-mode` nastaveným na `login` k vytvoření kontejneru pomocí přihlašovacích údajů Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

Proměnná prostředí přidružená k parametru `--auth-mode` je `AZURE_STORAGE_AUTH_MODE`. Můžete zadat vhodnou hodnotu v proměnné prostředí, abyste se nemuseli zahrnout do každého volání operace Azure Storage data.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Volání příkazů PowerShellu pomocí přihlašovacích údajů Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete použít Azure PowerShell k přihlášení a spuštění dalších operací s Azure Storage pomocí přihlašovacích údajů Azure AD, vytvořte kontext úložiště pro odkazování na účet úložiště a zahrňte parametr `-UseConnectedAccount`.

Následující příklad ukazuje, jak vytvořit kontejner v novém účtu úložiště z Azure PowerShell pomocí vašich přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

1. Přihlaste se ke svému účtu Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Další informace o přihlášení k Azure pomocí PowerShellu najdete v tématu věnovaném [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Vytvořte skupinu prostředků Azure voláním [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Vytvořte účet úložiště voláním [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Získejte kontext účtu úložiště, který určuje nový účet úložiště voláním [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Když pracujete na účtu úložiště, můžete místo opakovaného předání přihlašovacích údajů odkazovat na kontext. Zahrňte parametr `-UseConnectedAccount` pro volání jakýchkoli následných operací s daty pomocí přihlašovacích údajů Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Než vytvoříte kontejner, přiřaďte roli [Přispěvatel dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště. Další informace o přiřazování rolí RBAC najdete [v tématu udělení přístupu k datům objektů blob Azure a frontě pomocí RBAC v Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Rozšiřování přiřazení rolí RBAC může trvat několik minut.

1. Vytvořte kontejner voláním [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Vzhledem k tomu, že toto volání používá kontext vytvořený v předchozích krocích, kontejner se vytvoří pomocí vašich přihlašovacích údajů Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Další kroky

- Další informace o rolích RBAC pro Azure Storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Další informace o použití spravovaných identit pro prostředky Azure s Azure Storage najdete v tématu [ověřování přístupu k objektům blob a frontám pomocí Azure Active Directory a spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md).
- Informace o tom, jak autorizovat přístup k kontejnerům a frontám v rámci aplikací úložiště, najdete v tématu [použití Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
