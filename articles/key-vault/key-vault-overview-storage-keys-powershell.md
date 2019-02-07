---
title: Služba Azure Key Vault spravovat účet úložiště – verze prostředí PowerShell
description: Funkce účtu spravovaného úložiště poskytuje seemless integrace mezi Azure Key Vault a účet úložiště Azure.
ms.topic: conceptual
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 11/28/2018
ms.openlocfilehash: 1c0502458a5c20991ada6f5a33d067a38596752b
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55817559"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Služba Azure Key Vault spravovat účet úložiště – PowerShell

> [!NOTE]
> [Integrace služby Azure storage s Azure Active Directory (Azure AD) je teď ve verzi preview](https://docs.microsoft.com/azure/storage/common/storage-auth-aad). Doporučujeme použít Azure AD pro ověřování a autorizaci, které poskytuje přístup na základě tokenu OAuth2 do úložiště Azure, stejně jako Azure Key Vault. To vám umožní:
> - Ověření klientské aplikace pomocí identitou aplikace nebo uživatele místo přihlašovacích údajů účtu úložiště. 
> - Použití [identita spravované služby Azure AD](/azure/active-directory/managed-identities-azure-resources/) při spouštění v Azure. Spravovat identity odebrat potřebné pro ověřování klientů všechno dohromady a ukládání přihlašovacích údajů v nebo s vaší aplikací.
> - Používejte na základě řízení přístupu Role (RBAC) pro správu autorizace, která je také podporována službou Key Vault.

[Účtu služby Azure storage](/azure/storage/storage-create-storage-account) používá pověření, která se skládá z názvu účtu a klíč. Klíč se automaticky generuje a obsluhuje více jako "heslo" na rozdíl od kryptografický klíč. Key Vault, můžete spravovat tyto klíče účtu úložiště jako ukládání [tajných kódů služby Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Přehled

Účet úložiště, že funkce provádí několik funkcí správy vaším jménem spravované služby Key Vault:

- Seznamy (synchronizace) klíče účtu služby Azure storage.
- Obnoví (obmění) klíče pravidelně.
- Spravuje klíče pro účty úložiště a účty úložiště Classic.
- Hodnoty klíče se nikdy vrátí odpověď na volajícího.

Při použití funkce klíče účtu spravovaného úložiště:

- **Povolte pouze služby Key Vault ke správě vašeho klíče účtu úložiště.** Nepokoušejte se spravovat sami, jak budete rušit procesy služby Key Vault.
- **Nepovolit klíče účtu úložiště lze spravovat pomocí více než jeden objekt služby Key Vault**.
- **Klíče účtu úložiště ručně negenerovat**. Doporučujeme vám, můžete obnovit pomocí služby Key Vault.

Následující příklad ukazuje, jak povolit služby Key Vault ke správě vašeho klíče účtu úložiště.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autorizace služby Key Vault pro přístup k účtu úložiště

> [!IMPORTANT]
> Tenanta služby Azure AD poskytuje každý registrované aplikaci  **[instanční objekt služby](/azure/active-directory/develop/developer-glossary#service-principal-object)**, který slouží jako identitu aplikace. ID aplikace instančního objektu se používá při zadání autorizaci pro přístup k další prostředky Azure prostřednictvím řízení přístupu na základě role (RBAC). Protože Key Vault je aplikace Microsoft, je předem registrovánu ve všech tenantů Azure AD v rámci stejné ID aplikace v rámci každé cloudu Azure:
> - ID aplikace používat klienty Azure AD v cloudu Azure government `7e7c393b-45d0-48b1-a35e-2905ddf8183c`.
> - ID aplikace používat klienty Azure AD ve veřejném cloudu Azure a všechny ostatní `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`.

Před služby Key Vault můžete přístup a spravovat své klíče účtu úložiště, musí povolit jeho přístup k účtu úložiště. Aplikace služby Key Vault vyžaduje oprávnění k *seznamu* a *znovu vygenerovat* klíče účtu úložiště. Tato oprávnění jsou povolené prostřednictvím předdefinované role RBAC [Role služby operátor klíč účtu úložiště](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Přiřaďte tuto roli služby Key Vault instančnímu objektu služby, omezení rozsahu do vašeho účtu úložiště pomocí následujících kroků. Nezapomeňte aktualizovat `$resourceGroupName`, `$storageAccountName`, `$storageAccountKey`, a `$keyVaultName` proměnné před spuštěním skriptu:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Authenticate your PowerShell session with Azure AD, for use with Azure Resource Manager cmdlets
$azureProfile = Connect-AzureRmAccount

# Get a reference to your Azure storage account
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzureRmRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Po přiřazení role úspěšné byste měli vidět výstup podobný následujícímu příkladu:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Pokud služby Key Vault je už přidaná do role v účtu úložiště, dostanete *"přiřazení role už existuje."* Došlo k chybě. Přiřazení role, můžete ověřit také pomocí webu Azure portal na stránce "Řízení přístupu (IAM)" účet úložiště.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Udělili oprávnění účtu uživatele k účtům úložiště spravované

>[!TIP] 
> Stejně jako nabízí Azure AD **instanční objekt služby** pro identitu aplikace **hlavní název uživatele** je k dispozici pro identitu uživatele. Uživatel instančního objektu můžete pak získat autorizaci pro přístup k trezoru klíčů, prostřednictvím oprávnění zásad přístupu trezoru klíčů.

Pomocí stejné relaci Powershellu, aktualizace zásad přístupu trezoru klíčů pro účty spravované úložiště. Tento krok platí oprávnění účtu úložiště pro váš uživatelský účet, zajištění, že může přístup k funkcím účtu spravovaného úložiště: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $azureProfile.Context.Account.Id -PermissionsToStorage get, list, listsas, delete, set, update, regeneratekey, recover, backup, restore, purge
```

Všimněte si, že oprávnění pro účty úložiště nejsou k dispozici na stránce "Zásady přístupu" účet úložiště na webu Azure Portal.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Přidat spravovaný účet úložiště k vaší instanci služby Key Vault

Spravovaný účet úložiště pomocí stejné relaci Powershellu vytvořte ve vaší instanci služby Key Vault. `-DisableAutoRegenerateKey` Přepínač určuje nechcete obnovit klíče účtu úložiště.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Po úspěšné přidání účtu úložiště pomocí žádné opětovném generování přístupového klíče byste měli vidět výstup podobný následujícímu příkladu:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

### <a name="enable-key-regeneration"></a>Povolit generování klíče

Pokud chcete služby Key Vault se pravidelně znovu vygenerovat své klíče účtu úložiště, můžete nastavit dobu obnovení. V následujícím příkladu jsme nastavili období opětovné vygenerování tří dnů. Po třech dnech se služby Key Vault znovu vygenerovat "key1" a Prohodit aktivní klíč z "key2" k "key1".

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Po úspěšném přidání s opětovném generování přístupového klíče účtu úložiště byste měli vidět výstup podobný následujícímu příkladu:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                : 
```

## <a name="next-steps"></a>Další postup

- [Spravované ukázky klíče účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- [Odkaz na Key Vault prostředí PowerShell](/powershell/module/azurerm.keyvault/)
