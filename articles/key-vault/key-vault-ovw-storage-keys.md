---
ms.assetid: ''
title: Klíče účtu úložiště Azure Key Vault
description: Klíče účtu úložiště poskytují seemless integrace mezi službami Azure Key Vault a klíče přístupu na základě do účtu úložiště Azure.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: e62c599d82f488bf1fc30ce503c271084c5ae59d
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300215"
---
# <a name="azure-key-vault-storage-account-keys"></a>Klíče účtu úložiště Azure Key Vault

> [!NOTE]
> [Azure storage teď podporuje ověřování AAD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Doporučujeme použít Azure Active Directory pro ověřování a autorizace pro úložiště, jak uživatelé nemusí dělat starosti o otáčení své klíče účtu úložiště.

- Azure Key Vault se spravuje klíče z Azure Storage účtu (ASA).
    - Azure Key Vault interně, můžete zobrazit seznam klíčů (sync) pomocí účtu služby Azure Storage.    
    - Služba Azure Key Vault obnoví (obmění) klíče pravidelně.
    - Hodnoty klíče se nikdy vrátí odpověď na volajícího.
    - Služba Azure Key Vault slouží ke správě klíčů účtů úložiště a klasické účty úložiště.

<a name="prerequisites"></a>Požadavky
--------------
1. [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) instalace Azure CLI   
2. [Vytvoření účtu úložiště](https://azure.microsoft.com/services/storage/)
    - Postupujte podle kroků v tomto [dokumentu](https://docs.microsoft.com/azure/storage/) k vytvoření účtu úložiště  
    - **Pokyny pro pojmenování:** názvy účtů úložiště musí mít délku 3 až 24 znaků a může obsahovat jenom číslice a malá písmena.        
      
<a name="step-by-step-instructions-on-how-to-use-key-vault-to-manage-storage-account-keys"></a>Krok pokyny o tom, jak používat ke správě klíčů účtu úložiště služby Key Vault
--------------------------------------------------------------------------------
V následujících pokynů, jsme služby Key Vault přiřazování jako službu, která mají oprávnění operátora na vašem účtu úložiště

> [!NOTE]
> Prosím Všimněte si, že když jste nastavili Azure Key Vault spravovat účet úložiště klíčů, měli **ne** už může změnit pouze pomocí služby Key Vault. Spravované úložiště klíčů účtu znamená, že Key Vault spravovat obměně klíče účtu úložiště

1. Po vytvoření účtu úložiště, spuštěním následujícího příkazu Získejte ID prostředku účtu úložiště, kterou chcete spravovat

    ```
    az storage account show -n storageaccountname (Copy ID field out of the result of this command)
    ```
    
2. Získat aplikace ID z Azure Key Vault pro službu objektu zabezpečení 

    ```
    az ad sp show --id cfa8b339-82a2-471a-a3c9-0fc0be7a4093
    ```
    
3. Přiřadit roli operátora klíč úložiště do služby Azure Key Vault Identity

    ```
    az role assignment create --role "Storage Account Key Operator Service Role"  --assignee-object-id <ApplicationIdOfKeyVault> --scope <IdOfStorageAccount>
    ```
    
4. Vytvoření trezoru klíčů spravovaného účtu úložiště.     <br /><br />
   Níže jsme nastavujete opětovné generování uplynutí 90 dnů. Po 90 dnech se služby Key Vault znovu vygenerovat "key1" a Prohodit aktivní klíč z "key2" k "key1".
   
    ```
    az keyvault storage add --vault-name <YourVaultName> -n <StorageAccountName> --active-key-name key2 --auto-regenerate-key --regeneration-period P90D --resource-id <Resource-id-of-storage-account>
    ```
    V případě uživatel nevytvořili účet úložiště a nemá oprávnění k účtu úložiště, následujícím způsobem nastavit oprávnění pro svůj účet a ujistěte se, že můžete spravovat všechna úložiště oprávnění ve službě Key Vault.
 > [!NOTE] 
    V případě, že uživatel nemá oprávnění k účtu úložiště dostaneme nejprve Id objektu uživatele

    ```
    az ad user show --upn-or-object-id "developer@contoso.com"

    az keyvault set-policy --name <YourVaultName> --object-id <ObjectId> --storage-permissions backup delete list regeneratekey recover     purge restore set setsas update
    ```
### <a name="relavant-azure-cli-cmdlets"></a>Rutiny Relavant Azure CLI
- [Rutiny úložiště v Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/storage?view=azure-cli-latest)

### <a name="relevant-powershell-cmdlets"></a>Odpovídající rutiny prostředí Powershell

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="see-also"></a>Další informace najdete v tématech

- [Informace o klíčích, tajných kódech a certifikátech](https://docs.microsoft.com/rest/api/keyvault/)
- [Blog týmu služby Key Vault](https://blogs.technet.microsoft.com/kv/)
