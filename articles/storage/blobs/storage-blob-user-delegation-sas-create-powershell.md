---
title: Použití PowerShellu k vytvoření SAS delegování uživatelů pro kontejner nebo objekt BLOB
titleSuffix: Azure Storage
description: Naučte se vytvářet delegování uživatelů pomocí Azure Active Directory přihlašovacích údajů pomocí PowerShellu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91714526"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Vytvoření SAS delegování uživatele pro kontejner nebo objekt BLOB pomocí PowerShellu

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

V tomto článku se dozvíte, jak pomocí pověření Azure Active Directory (Azure AD) vytvořit přidružení zabezpečení delegování uživatelů pro kontejner nebo objekt BLOB s Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Pokud chcete vytvořit SAS delegování uživatelů pomocí PowerShellu, nainstalujte verzi 1.10.0 nebo novější z modulu AZ. Storage. Pomocí těchto kroků nainstalujete nejnovější verzi modulu:

1. Odinstalujte všechny předchozí instalace Azure PowerShell:

    - Odeberte všechny předchozí instalace Azure PowerShell z Windows pomocí nastavení **funkce & aplikace** v části **Nastavení**.
    - Odeberte všechny moduly **Azure** z `%Program Files%\WindowsPowerShell\Modules` .

1. Ujistěte se, že máte nainstalovanou nejnovější verzi PowerShellGet. Otevřete okno prostředí Windows PowerShell a spuštěním následujícího příkazu nainstalujte nejnovější verzi:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Po instalaci PowerShellGet zavřete a znovu otevřete okno PowerShellu.

1. Nainstalujte nejnovější verzi Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Ujistěte se, že máte nainstalovanou verzi Azure PowerShell 3.2.0 nebo novější. Spuštěním následujícího příkazu nainstalujte nejnovější verzi modulu Azure Storage PowerShell:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Zavřete a znovu otevřete okno PowerShellu.

Pokud chcete zjistit, která verze modulu AZ. Storage je nainstalovaná, spusťte následující příkaz:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Další informace o instalaci Azure PowerShell najdete v tématu [instalace Azure PowerShell pomocí PowerShellGet](/powershell/azure/install-az-ps).

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Přihlášení k Azure PowerShell pomocí Azure AD

Voláním příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) se přihlaste pomocí účtu služby Azure AD:

```powershell
Connect-AzAccount
```

Další informace o přihlašování pomocí PowerShellu najdete v tématu věnovaném [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="assign-permissions-with-azure-rbac"></a>Přiřazení oprávnění k Azure RBAC

K vytvoření SAS delegování uživatele z Azure PowerShell musí být účet služby Azure AD, který se používá pro přihlášení do prostředí PowerShell, přiřazen roli, která zahrnuje akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Toto oprávnění umožňuje účtu Azure AD požádat o *klíč delegování uživatele*. Klíč pro delegování uživatelů se používá k podepsání SAS delegování uživatele. Role, která poskytuje akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** , musí být přiřazená na úrovni účtu úložiště, skupiny prostředků nebo předplatného. Další informace o oprávněních služby Azure RBAC pro vytvoření SAS delegování uživatelů najdete v části **přiřazení oprávnění k Azure RBAC** v tématu [Vytvoření SAS uživatele pro delegování](/rest/api/storageservices/create-user-delegation-sas).

Pokud nemáte dostatečná oprávnění k přiřazení rolí Azure k objektu zabezpečení služby Azure AD, může být nutné požádat vlastníka nebo správce účtu, aby přiřadil potřebná oprávnění.

Následující příklad přiřadí roli **Přispěvatel dat objektu BLOB úložiště** , která zahrnuje akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** . Role je vymezená na úrovni účtu úložiště.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Další informace o předdefinovaných rolích, které zahrnují akci **Microsoft. Storage/storageAccounts/blobServices/generateUserDelegationKey** , najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Použití přihlašovacích údajů Azure AD k zabezpečení SAS

Když vytvoříte SAS uživatele při delegování pomocí Azure PowerShell, vytvoří se klíč pro delegování uživatele, který se používá k podepsání SAS, implicitně. Čas spuštění a čas vypršení platnosti, který zadáte pro SAS, se používá také jako počáteční čas a čas vypršení platnosti klíče pro delegování uživatele. 

Vzhledem k tomu, že maximální interval, po který je klíč delegování uživatele platný, je 7 dní od počátečního data, měli byste zadat čas vypršení platnosti pro SAS, který je do 7 dnů od času spuštění. Podpis SAS je po vypršení platnosti klíče pro delegování uživatele neplatný, takže platnost SAS s časem vypršení platnosti delší než 7 dní bude platit i pro 7 dní.

Chcete-li vytvořit přidružení zabezpečení delegování uživatele pro kontejner nebo objekt BLOB s Azure PowerShell, nejprve vytvořte nový objekt kontextu Azure Storage a určete `-UseConnectedAccount` parametr. `-UseConnectedAccount`Parametr určuje, že příkaz vytvoří kontextový objekt pod účtem služby Azure AD, se kterým jste se přihlásili.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Vytvoření SAS pro delegování uživatelů pro kontejner

Chcete-li vrátit token SAS pro delegování uživatele pro kontejner, zavolejte příkaz [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) a předejte objekt kontextu Azure Storage, který jste vytvořili dříve.

Následující příklad vrátí token SAS pro delegování uživatele pro kontejner. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Vrácený token SAS pro delegování uživatelů bude vypadat přibližně takto:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Vytvoření SAS pro delegování uživatelů pro objekt BLOB

Chcete-li vrátit token SAS pro delegování uživatele pro objekt blob, zavolejte příkaz [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) , který předává objekt kontextu Azure Storage, který jste vytvořili dříve.

Následující syntaxe vrátí SAS delegování uživatele pro objekt BLOB. Příklad určuje `-FullUri` parametr, který vrátí identifikátor URI objektu BLOB s připojením tokenu SAS. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

Vrácený identifikátor URI SAS pro delegování uživatele bude vypadat přibližně takto:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> SAS delegování uživatele nepodporuje definování oprávnění s uloženými zásadami přístupu.

## <a name="revoke-a-user-delegation-sas"></a>Odvolání SAS uživatele na delegování

Chcete-li odvolat SAS delegování uživatele z Azure PowerShell, zavolejte příkaz **REVOKE-AzStorageAccountUserDelegationKeys** . Tento příkaz odvolá všechny klíče delegování uživatelů přidružené k zadanému účtu úložiště. Zruší se platnost všech sdílených přístupových podpisů přidružených k těmto klíčům.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Jak klíč delegování uživatele a přiřazení role Azure ukládá do mezipaměti Azure Storage, takže může dojít ke zpoždění mezi tím, kdy zahájíte proces odvolání a stávající přidružení zabezpečení delegování uživatele nebude platné.

## <a name="next-steps"></a>Další kroky

- [Vytvoření SAS delegování uživatele (REST API)](/rest/api/storageservices/create-user-delegation-sas)
- [Získat operaci klíče delegování uživatele](/rest/api/storageservices/get-user-delegation-key)
