---
title: Spusťte příkazy prostředí PowerShell nebo rozhraní příkazového řádku Azure pomocí přihlašovacích údajů Azure AD pro přístup k datům objektu blob nebo fronty | Dokumentace Microsoftu
description: Azure CLI a Powershellu podporu přihlášení pomocí přihlašovacích údajů Azure AD ke spouštění příkazů na data objektů blob a fronty Azure Storage. Přístupový token je k dispozici pro relaci a použít k autorizaci volání operace. Oprávnění, závisí na role RBAC přiřazená k objektu zabezpečení služby Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/19/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 96be1e600c8d5895cc0eb5b058ce17f7265fa0a9
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149644"
---
# <a name="run-azure-cli-or-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Spusťte příkazy prostředí PowerShell nebo rozhraní příkazového řádku Azure pomocí přihlašovacích údajů Azure AD pro přístup k datům objektu blob nebo fronty

Azure Storage poskytuje rozšíření pro Azure CLI a Powershellu, které vám umožní přihlásit a spuštění příkazů skriptu s přihlašovacími údaji služby Azure Active Directory (Azure AD). Při přihlášení k Azure CLI nebo Powershellu pomocí přihlašovacích údajů Azure AD, je vrácena přístupového tokenu OAuth 2.0. Tento token slouží automaticky podle prostředí PowerShell nebo rozhraní příkazového řádku k autorizaci následná data operace s úložištěm objektů Blob nebo fronty. Pro podporované operace je už nebude potřeba předat klíč k účtu nebo token SAS pomocí příkazu.

Můžete přiřadit oprávnění k datům objektu blob a fronty k objektu zabezpečení Azure AD prostřednictvím řízení přístupu na základě role (RBAC). Další informace o rolích RBAC ve službě Azure Storage najdete v tématu [Správa přístupových práv k datům služby Azure Storage pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Podporované operace

Rozšíření jsou podporovány pro operace s kontejnery a fronty. Operace, které může volat závisí na oprávněních udělených k objektu zabezpečení služby Azure AD, se kterým přihlásíte k Azure CLI nebo Powershellu. Oprávnění pro kontejnery služby Azure Storage nebo fronty jsou přiřazeny prostřednictvím řízení přístupu na základě role (RBAC). Například, pokud jste přiřadili **Čtenář dat objektu Blob** roli a potom spuštěním příkazů skriptu, které načítají data z kontejneru nebo fronty. Pokud jste přiřadili **Přispěvatel dat objektu Blob** role, pak je můžete spustit příkazy skriptu, které číst, zapsat nebo odstranit kontejner nebo fronty nebo data obsahují. 

Podrobnosti o oprávněních požadovaných pro každou operaci služby Azure Storage v kontejneru nebo fronty, naleznete v tématu [oprávnění pro volání operace REST](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-rest-operations).  

## <a name="call-cli-commands-using-azure-ad-credentials"></a>Volání příkazů rozhraní příkazového řádku pomocí přihlašovacích údajů Azure AD

Azure CLI podporuje `--auth-mode` parametr pro operace s daty objektů blob a fronty:

- Nastavte `--auth-mode` parametr `login` k přihlášení pomocí instančního objektu zabezpečení služby Azure AD.
- Nastavte `--auth-mode` parametr starší `key` jsou k dispozici hodnota pokusu o dotaz na klíče if účet žádné parametry ověřování pro účet. 

Následující příklad ukazuje, jak vytvořit kontejner ve nový účet úložiště z příkazového řádku Azure pomocí přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami: 

1. Ujistěte se, že máte nainstalovanou verzi Azure CLI 2.0.46 nebo novější. Spustit `az --version` zkontrolujte nainstalovanou verzi.

1. Spustit `az login` a ověřit v okně prohlížeče: 

    ```azurecli
    az login
    ```
    
1. Zadejte požadované předplatné. Vytvořte skupinu prostředků pomocí příkazu [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Vytvoření účtu úložiště v rámci této skupiny prostředků pomocí [vytvořit účet úložiště az](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create): 

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
    
1. Než vytvoříte kontejner, přiřaďte [Přispěvatel dat objektu Blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) role na vás. I když jste vlastník účtu, potřebují explicitní oprávnění k provedení operace s daty proti účtu úložiště. Další informace o přiřazení role RBAC najdete v tématu [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Přiřazení rolí pro RBAC může trvat několik minut na dokončení propagace.
    
1. Volání [vytvořit kontejner úložiště az](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) příkazů `--auth-mode` parametr nastaven na `login` vytvořit kontejner pomocí svých přihlašovacích údajů Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

Přidružený k proměnné prostředí `--auth-mode` parametr `AZURE_STORAGE_AUTH_MODE`. Zadejte příslušnou hodnotu v proměnné prostředí, aby se zabránilo včetně při každém volání operace dat služby Azure Storage.

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Volat příkazy prostředí PowerShell pomocí přihlašovacích údajů Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Použití Azure Powershellu pro přihlášení a následné operace spustit využívající službu Azure Storage pomocí přihlašovacích údajů Azure AD, vytvoří kontext úložiště tak, aby odkazovaly na účet úložiště a včetně `-UseConnectedAccount` parametru.

Následující příklad ukazuje, jak vytvořit kontejner ve nový účet úložiště z prostředí Azure PowerShell pomocí svých přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

1. Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` příkaz a postupujte podle pokynů na obrazovce pokynů k zadání přihlašovacích údajů Azure AD: 

    ```powershell
    Connect-AzAccount
    ```
    
1. Vytvořte skupinu prostředků Azure pomocí volání [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Vytvoření účtu úložiště pomocí volání [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Získat kontext účtu úložiště, který určuje nový účet úložiště pomocí volání [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Když používáte v účtu úložiště, můžete odkazovat na kontext místo opakovaně předávání přihlašovací údaje. Zahrnout `-UseConnectedAccount` parametr volejte libovolné operace následná data pomocí přihlašovacích údajů Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Než vytvoříte kontejner, přiřaďte [Přispěvatel dat objektu Blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) role na vás. I když jste vlastník účtu, potřebují explicitní oprávnění k provedení operace s daty proti účtu úložiště. Další informace o přiřazení role RBAC najdete v tématu [udělit přístup k Azure data objektů blob a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Přiřazení rolí pro RBAC může trvat několik minut na dokončení propagace.

1. Vytvořte kontejner zavoláním [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Protože toto volání používá kontext vytvořený v předchozích krocích, vytvoří se v kontejneru pomocí svých přihlašovacích údajů Azure AD. 

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Další postup

- Další informace o rolích RBAC pro Azure storage najdete v tématu [Správa přístupových práv k datům úložiště pomocí RBAC](storage-auth-aad-rbac.md).
- Další informace o použití spravované identity pro prostředky Azure pomocí služby Azure Storage, najdete v článku [ověření přístupu k objektům BLOB a fronty s Azure Active Directory a spravovaným identitám pro prostředky Azure](storage-auth-aad-msi.md).
- Zjistěte, jak autorizovat přístup ke kontejnerům a front z v rámci aplikace úložiště, najdete v článku [pomocí služby Azure AD s aplikacemi úložiště](storage-auth-aad-app.md).
