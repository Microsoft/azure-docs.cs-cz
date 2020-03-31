---
title: Vytvoření sazby SAS pro kontejner nebo objekt blob pomocí prostředí PowerShell
titleSuffix: Azure Storage
description: Zjistěte, jak vytvořit delegování uživatele SAS s přihlašovacími údaji služby Azure Active Directory pomocí PowerShellu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: 5250a27e6c5fcf012207f1edb95ad46c0aabfe63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536169"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Vytvoření uživatelskédelegování SAS pro kontejner nebo objekt blob s Prostředím PowerShell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

Tento článek ukazuje, jak pomocí přihlašovacích údajů služby Azure Active Directory (Azure AD) vytvořit delegování uživatele SAS pro kontejner nebo objekt blob s Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Chcete-li vytvořit uživatelské delegování SAS s prostředím PowerShell, nainstalujte verzi 1.10.0 nebo novější modulu Az.Storage. Chcete-li nainstalovat nejnovější verzi modulu, postupujte takto:

1. Odinstalujte všechny předchozí instalace Azure PowerShellu:

    - Odebrání všech předchozích instalací Prostředí Azure PowerShell z Windows pomocí nastavení **funkce aplikace &** v části **Nastavení**.
    - Odeberte všechny `%Program Files%\WindowsPowerShell\Modules`moduly **Azure** z aplikace .

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet. Otevřete okno prostředí Windows PowerShell a pro instalaci nejnovější verze nainstalujte následující příkaz:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Zavřete a znovu otevřete okno PowerShellu po instalaci PowerShellGet.

1. Nainstalujte nejnovější verzi Azure PowerShellu:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Ujistěte se, že jste nainstalovali Azure PowerShell verze 3.2.0 nebo novější. Chcete-li nainstalovat nejnovější verzi modulu Azure Storage PowerShell, spusťte následující příkaz:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Zavřete a znovu otevřete okno PowerShellu.

Chcete-li zkontrolovat, která verze modulu Az.Storage je nainstalována, spusťte následující příkaz:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Další informace o instalaci Azure PowerShellu najdete [v tématu Instalace Azure PowerShellu pomocí PowerShellu](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Přihlášení k Azure PowerShellu pomocí Azure AD

Volání [příkazu Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) pro přihlášení pomocí účtu Azure AD:

```powershell
Connect-AzAccount
```

Další informace o přihlášení pomocí PowerShellu najdete [v tématu Přihlášení pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-rbac"></a>Přiřazení oprávnění pomocí RBAC

Chcete-li vytvořit delegování uživatele SAS z Azure PowerShell, musí být účtu Azure AD používanému k přihlášení k PowerShellu přiřazena role, která zahrnuje akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Toto oprávnění umožňuje, aby účet Azure AD požádat *o klíč delegování uživatele*. Klíč delegování uživatele se používá k podepsání uživatelské delegování SAS. Role poskytující akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** musí být přiřazena na úrovni účtu úložiště, skupiny prostředků nebo předplatného. Další informace o oprávněních RBAC pro vytvoření sas uživatelské delegování naleznete v části **Přiřazení oprávnění s RBAC** v [tématu Vytvoření pověření uživatele SAS](/rest/api/storageservices/create-user-delegation-sas).

Pokud nemáte dostatečná oprávnění k přiřazení rolí RBAC k objektu zabezpečení Azure AD, budete muset požádat vlastníka účtu nebo správce o přiřazení potřebných oprávnění.

Následující příklad přiřadí roli **přispěvatele dat objektů blob úložiště,** která zahrnuje akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey.** Role je vymezena na úrovni účtu úložiště.

Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Další informace o předdefinovaných rolích, které zahrnují akci **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey,** najdete [v tématu Předdefinované role pro prostředky Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Použití přihlašovacích údajů Azure AD k zabezpečení SAS

Když vytvoříte delegování uživatele SAS s Azure PowerShell, klíč delegování uživatele, který se používá k podpisu SAS se vytvoří pro vás implicitně. Čas zahájení a čas vypršení platnosti, které zadáte pro SAS se také používají jako čas zahájení a čas vypršení platnosti pro klíč delegování uživatele. 

Vzhledem k tomu, že maximální interval, po který je platný klíč delegování uživatele, je 7 dní od počátečního data, měli byste zadat čas vypršení platnosti pro SAS, který je do 7 dnů od počátečního času. SAS je neplatný po vypršení platnosti klíče delegování uživatele, takže SAS s dobou vypršení platnosti delší než 7 dní bude stále platný po dobu 7 dnů.

Chcete-li vytvořit delegování uživatele SAS pro kontejner nebo objekt blob s Azure `-UseConnectedAccount` PowerShell, nejprve vytvořte nový objekt kontextu úložiště Azure a zadejte parametr. Parametr `-UseConnectedAccount` určuje, že příkaz vytvoří objekt kontextu pod účtem Azure AD, pomocí kterého jste se přihlásili.

Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Vytvoření uživatelskédelegování SAS pro kontejner

Chcete-li vrátit token SAS delegování uživatele pro kontejner, zavolejte příkaz [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) a předejte objekt kontextu úložiště Azure, který jste vytvořili dříve.

Následující příklad vrátí token SAS delegování uživatele pro kontejner. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Vrácený token SAS delegování uživatele bude podobný:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Vytvoření uživatelskédelegování SAS pro objekt blob

Chcete-li vrátit token SAS delegování uživatele pro objekt blob, zavolejte příkaz [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) a předejte objekt kontextu úložiště Azure, který jste vytvořili dříve.

Následující syntaxe vrátí delegování uživatele SAS pro objekt blob. Příklad určuje `-FullUri` parametr, který vrátí identifikátor URI objektu BLOB s připojeným tokenem SAS. Nezapomeňte nahradit zástupné hodnoty v závorkách vlastními hodnotami:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Vrácené identifikátory URI delegování uživatele SAS budou podobné:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Uživatelské delegování SAS nepodporuje definování oprávnění s uloženou zásadou přístupu.

## <a name="revoke-a-user-delegation-sas"></a>Odvolání uživatelského delegování SAS

Chcete-li odvolat delegování uživatele SAS z Prostředí Azure PowerShell, zavolejte příkaz **Revoke-AzStorageAccountUserDelegationKeys.** Tento příkaz odvolá všechny klíče delegování uživatelů přidružené k zadanému účtu úložiště. Všechny sdílené přístupové podpisy přidružené k těmto klíčům jsou zneplatněny.

Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Klíč delegování uživatele a přiřazení rolí RBAC jsou uloženy do mezipaměti služby Azure Storage, takže může být zpoždění mezi zahájením procesu odvolání a když existující uživatel delegování SAS stane neplatným.

## <a name="next-steps"></a>Další kroky

- [Vytvoření uživatelskédelegace SAS (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
