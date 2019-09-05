---
title: Azure Key Vault spravovaného účtu úložiště – verze PowerShellu
description: Funkce spravovaného účtu úložiště poskytuje bezchybnou integraci mezi Azure Key Vault a účtem služby Azure Storage.
ms.topic: conceptual
ms.service: key-vault
author: msmbaldwin
ms.author: mbaldwin
manager: barbkess
ms.date: 03/01/2019
ms.openlocfilehash: 530f38289afb3fce85bbb025e7b699862eedbadc
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382703"
---
# <a name="azure-key-vault-managed-storage-account---powershell"></a>Azure Key Vault spravovaného účtu úložiště – PowerShell

> [!NOTE]
> [Integrace služby Azure Storage s Azure Active Directory (Azure AD) je teď ve verzi Preview](../storage/common/storage-auth-aad.md). Pro ověřování a autorizaci doporučujeme používat Azure AD, který poskytuje přístup založený na tokenech OAuth2 k úložišti Azure, stejně jako Azure Key Vault. To vám umožní:
> - Místo přihlašovacích údajů k účtu úložiště ověřte svoji klientskou aplikaci pomocí identity aplikace nebo uživatele. 
> - Při provozu v Azure použijte [spravovanou identitu Azure AD](/azure/active-directory/managed-identities-azure-resources/) . Spravované identity odstraňují nutnost ověřování klientů společně a ukládají přihlašovací údaje do nebo do aplikace.
> - Pro správu autorizací použijte Access Control na základě rolí (RBAC), kterou podporuje taky Key Vault.
> - Přístup AAD k účtu úložiště nefunguje pro přístup k tabulkám, které ještě nejsou.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Účet úložiště Azure](/azure/storage/storage-create-storage-account) používá přihlašovací údaje, které se skládají z názvu účtu a klíče. Klíč se vygeneruje automaticky a bude sloužit jako "heslo", a to na rozdíl od kryptografického klíče. Key Vault můžou tyto klíče účtu úložiště spravovat tak, že je uloží jako [Key Vault tajné klíče](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

## <a name="overview"></a>Přehled

Funkce Key Vault spravovaného účtu úložiště provádí vaším jménem několik funkcí správy:

- Zobrazí (synchronizuje) klíče s účtem služby Azure Storage.
- Pravidelně generuje (otáčí) klíče.
- Spravuje klíče pro účty úložiště i pro účty klasického úložiště.
- Hodnoty klíče se nikdy nevrátí v reakci na volajícího.

Když použijete funkci klíče spravovaného účtu úložiště:

- **Povoluje Key Vault jenom spravovat klíče účtu úložiště.** Nepokoušejte se spravovat sami, protože budete rušit procesy Key Vault.
- **Nepovolujte správu klíčů účtu úložiště více než jedním objektem Key Vault**.
- **Neobnovujte ručně klíče účtu úložiště**. Doporučujeme je znovu vygenerovat prostřednictvím Key Vault.

Následující příklad ukazuje, jak Key Vault spravovat klíče účtu úložiště.

## <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) ověřte svoji relaci PowerShellu. 
```azurepowershell-interactive
Connect-AzAccount
```
Pokud máte více předplatných Azure, můžete je vypsat pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) a zadat předplatné, které chcete použít, pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) . 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>Autorizovat Key Vault k přístupu k vašemu účtu úložiště

> [!IMPORTANT]
> Tenant služby Azure AD poskytuje každou registrovanou aplikaci s **[instančním objektem](/azure/active-directory/develop/developer-glossary#service-principal-object)** , který slouží jako identita aplikace. ID aplikace instančního objektu se používá, když udělujete oprávnění pro přístup k jiným prostředkům Azure prostřednictvím řízení přístupu na základě role (RBAC). Vzhledem k tomu, že Key Vault je aplikace Microsoftu, je předregistrovaná ve všech klientech Azure AD pod stejným ID aplikace v rámci každého cloudu Azure:
> - Klienti Azure AD v cloudu Azure pro státní správu používají `7e7c393b-45d0-48b1-a35e-2905ddf8183c`ID aplikace.
> - Klienti Azure AD ve veřejném cloudu Azure a všichni ostatní používají ID `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`aplikace.

Předtím, než Key Vault mít přístup a spravovat klíče účtu úložiště, musíte autorizovat svůj přístup k vašemu účtu úložiště. Aplikace Key Vault vyžaduje oprávnění k *vypsání* a *obnovení* klíčů pro váš účet úložiště. Tato oprávnění se povolují prostřednictvím předdefinované role [služby klíčového operátoru klíče účtu úložiště](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role)RBAC. 

Přiřaďte tuto roli k Key Vault instančnímu objektu, čímž omezíte rozsah na svůj účet úložiště, a to pomocí následujících kroků. Nezapomeňte aktualizovat `$resourceGroupName`proměnné, `$storageAccountName`, `$storageAccountKey`a `$keyVaultName` před spuštěním skriptu:

```azurepowershell-interactive
# TODO: Update with the resource group where your storage account resides, your storage account name, the name of your active storage account key, and your Key Vault instance name
$resourceGroupName = "rgContoso"
$storageAccountName = "sacontoso"
$storageAccountKey = "key1"
$keyVaultName = "kvContoso"
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093" # See "IMPORTANT" block above for information on Key Vault Application IDs

# Get your User Id for later commands
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Po úspěšném přiřazení role by se měl zobrazit výstup podobný následujícímu příkladu:

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

Pokud se už Key Vault do role vašeho účtu úložiště přidalo, obdržíte, *že přiřazení role už existuje.* Chyba. Přiřazení role můžete také ověřit pomocí stránky účet úložiště "řízení přístupu (IAM)" v Azure Portal.  

## <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Poskytnutí oprávnění ke spravovaným účtům úložiště vašemu uživatelskému účtu

>[!TIP] 
> Stejně jako služba Azure AD poskytuje **instanční objekt** pro identitu aplikace, je k dispozici **hlavní povinný uživatel** pro identitu uživatele. Objekt zabezpečení uživatele pak může udělit autorizaci pro přístup k Key Vault prostřednictvím oprávnění zásad přístupu Key Vault.

Pomocí stejné relace PowerShellu aktualizujte zásady přístupu Key Vault pro spravované účty úložiště. Tento krok aplikuje oprávnění účtu úložiště na váš uživatelský účet, což zajistí, že budete mít přístup ke spravovaným funkcím účtu úložiště: 

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Všimněte si, že oprávnění k účtům úložiště nejsou k dispozici na stránce zásady přístupu na účtu úložiště v Azure Portal.

## <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Přidání spravovaného účtu úložiště do instance Key Vault

Pomocí stejné relace PowerShellu vytvořte v instanci Key Vault spravovaný účet úložiště. `-DisableAutoRegenerateKey` Přepínač určuje, že se klíče účtu úložiště znovu negenerují.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Po úspěšném přidání účtu úložiště bez opětovného vygenerování klíče byste měli vidět výstup podobný následujícímu příkladu:

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

### <a name="enable-key-regeneration"></a>Povolit opakované generování klíče

Pokud chcete, Key Vault pravidelně znovu vygenerovat klíče účtu úložiště, můžete nastavit dobu vygenerování. V následujícím příkladu nastavíme dobu obnovení tři dny. Po třech dnech Key Vault znovu vygeneruje "key2" a prohodí aktivní klíč z "key2" na "klíč1".

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)
Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Po úspěšném přidání účtu úložiště s obnovením klíče byste měli vidět výstup podobný následujícímu příkladu:

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

## <a name="next-steps"></a>Další kroky

- [Ukázky klíčů spravovaného účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- [Reference k Key Vault PowerShellu](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
