---
title: Azure Key Vault spravovaného účtu úložiště – verze PowerShellu
description: Funkce účet spravovaného úložiště poskytuje bezproblémovou integraci mezi Azure Key Vault a účtem služby Azure Storage.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3bced101516e91259ea9018fe3c4aa44f867cbe6
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "96023104"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Správa klíčů účtu úložiště pomocí Key Vault a Azure PowerShell
> [!IMPORTANT]
> Doporučujeme používat Azure Storage integraci s Azure Active Directory (Azure AD), což je cloudová služba pro správu identit a přístupu od Microsoftu. Integrace Azure AD je k dispozici pro [objekty BLOB a fronty Azure](../../storage/common/storage-auth-aad.md)a poskytuje přístup založený na tokenech OAuth2 k Azure Storage (stejně jako Azure Key Vault).
> Azure AD umožňuje ověřování klientské aplikace pomocí identity aplikace nebo uživatele místo přihlašovacích údajů k účtu úložiště. Při spuštění v Azure můžete použít [spravovanou identitu Azure AD](../../active-directory/managed-identities-azure-resources/index.yml) . Spravované identity odstraňují nutnost ověřování klientů a ukládání přihlašovacích údajů do aplikace nebo s vaší aplikací. Použijte toto řešení pouze v případě, že není možné ověřování Azure AD.

Účet úložiště Azure používá přihlašovací údaje, které zahrnují název účtu a klíč. Klíč se vygeneruje automaticky a slouží jako heslo, nikoli jako kryptografický klíč. Key Vault spravuje klíče účtu úložiště tím, že je pravidelně znovu generuje v účtu úložiště, a poskytuje tokeny sdíleného přístupového podpisu pro delegovaný přístup k prostředkům ve vašem účtu úložiště.

Pomocí funkce klíče spravovaného účtu úložiště Key Vault můžete zobrazit (synchronizovat) klíče s účtem služby Azure Storage a pravidelně je znovu vygenerovat (otočit) klíče. Klíče můžete spravovat jak pro účty úložiště, tak pro účty klasického úložiště.

Když použijete funkci klíče spravovaného účtu úložiště, vezměte v úvahu následující body:

- Hodnoty klíče se nikdy nevrátí jako odpověď volajícímu.
- Klíče účtu úložiště by měly spravovat jenom Key Vault. Nespravujte klíče sami a zabraňte v narušování Key Vaultch procesů.
- Klíče účtu úložiště by měl spravovat jenom jeden objekt Key Vault. Nepovoluje správu klíčů z více objektů.
- Obnovte klíče jenom pomocí Key Vault. Neobnovujte ručně klíče účtu úložiště.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID aplikace instančního objektu

Tenant služby Azure AD poskytuje každou registrovanou aplikaci s [instančním objektem](../../active-directory/develop/developer-glossary.md#service-principal-object). Instanční objekt slouží jako ID aplikace, které se během autorizačního nastavení používá pro přístup k jiným prostředkům Azure prostřednictvím Azure RBAC.

Key Vault je aplikace Microsoftu, která je předem registrovaná ve všech klientech Azure AD. Key Vault je zaregistrované pod stejným ID aplikace v každém cloudu Azure.

| Tenanti | Cloud | ID aplikace |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Veřejný Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Jiné  | Libovolný | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tuto příručku, je třeba nejprve provést následující akce:

- [Nainstalujte modul Azure PowerShell](/powershell/azure/install-az-ps).
- [Vytvoření trezoru klíčů](quick-create-powershell.md)
- [Vytvořte účet úložiště Azure](../../storage/common/storage-account-create.md?tabs=azure-powershell). Název účtu úložiště musí obsahovat jenom malá písmena a číslice. Název musí mít délku 3 až 24 znaků.


## <a name="manage-storage-account-keys"></a>Správa klíčů účtu úložiště

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) ověřte svoji relaci PowerShellu.

```azurepowershell-interactive
Connect-AzAccount
```
Pokud máte více předplatných Azure, můžete je vypsat pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) a zadat předplatné, které chcete použít, pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Nastavení proměnných

Nejdřív nastavte proměnné, které budou používat rutiny prostředí PowerShell v následujících krocích. Nezapomeňte aktualizovat zástupné symboly "YourResourceGroupName", "YourStorageAccountName" a "YourKeyVaultName" a nastavit $keyVaultSpAppId na `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` (jak je uvedeno v [ID aplikace instančního objektu](#service-principal-application-id)výše).

Pomocí rutin Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext) a Get-AzStorageAccount budeme k získání ID uživatele a kontextu vašeho účtu služby Azure Storage používat také rutiny Get-a [Get-](/powershell/module/az.storage/get-azstorageaccount) .

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> Pro klasický účet úložiště pro $storageAccountKey použít "primární" a "sekundární". <br>
> Pro klasický účet úložiště použijte příkaz Get-AzResource-Name "ClassicStorageAccountName"-ResourceGroupName $resourceGroupName ', ale of'Get-AzStorageAccount '.

### <a name="give-key-vault-access-to-your-storage-account"></a>Zadejte Key Vault přístup k vašemu účtu úložiště.

Předtím, než Key Vault mít přístup a spravovat klíče účtu úložiště, musíte autorizovat svůj přístup k vašemu účtu úložiště. Aplikace Key Vault vyžaduje oprávnění k *vypsání* a *obnovení* klíčů pro váš účet úložiště. Tato oprávnění se povolují prostřednictvím předdefinované role [služby klíčového operátoru klíče účtu úložiště](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role)Azure.

Přiřaďte tuto roli k instančnímu objektu Key Vault, který omezí rozsah na svůj účet úložiště, pomocí rutiny Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) .

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
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

Pokud se už Key Vault do role vašeho účtu úložiště přidalo, obdržíte, *že přiřazení role už existuje.* . Přiřazení role můžete také ověřit pomocí stránky účet úložiště "řízení přístupu (IAM)" v Azure Portal.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Poskytnutí oprávnění ke spravovaným účtům úložiště vašemu uživatelskému účtu

Pomocí rutiny Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) aktualizujte zásady Key Vault přístupu a udělte účtu úložiště oprávnění k vašemu uživatelskému účtu.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Všimněte si, že oprávnění k účtům úložiště nejsou k dispozici na stránce zásady přístupu na účtu úložiště v Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Přidání spravovaného účtu úložiště do instance Key Vault

Pomocí rutiny Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) vytvořte v instanci Key Vault spravovaný účet úložiště. Přepínač určuje, že se  `-DisableAutoRegenerateKey` klíče účtu úložiště znovu negenerují.

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

Pokud chcete, Key Vault pravidelně znovu vygenerovat klíče účtu úložiště, můžete použít rutinu Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) a nastavit dobu obnovení. V tomto příkladu nastavíme dobu obnovení tři dny. Když je čas na otočení, Key Vault znovu vygeneruje klíč, který není aktivní, a pak nastaví nově vytvořený klíč jako aktivní. K vystavování tokenů SAS v jednom okamžiku se používá jenom jeden z klíčů. Toto je aktivní klíč.

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

## <a name="shared-access-signature-tokens"></a>Tokeny sdíleného přístupového podpisu

Můžete také požádat Key Vault o generování tokenů sdíleného přístupového podpisu. Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům ve vašem účtu úložiště. Klientům můžete udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. Sdílený přístupový podpis poskytuje zabezpečený způsob, jak sdílet prostředky úložiště bez narušení klíčů účtu.

Příkazy v této části dokončí následující akce:

- Nastavte definici sdíleného přístupového podpisu účtu.
- Vytvořte token sdíleného přístupového podpisu účtu pro služby blob, File, Table a Queue. Token se vytvoří pro službu typů prostředků, kontejner a objekt. Token se vytvoří se všemi oprávněními přes HTTPS a zadaným počátečním a koncovým datem.
- V trezoru nastavte Key Vault definici sdíleného přístupového podpisu spravovaného úložiště. Definice obsahuje identifikátor URI šablony tokenu sdíleného přístupového podpisu, který byl vytvořen. Definice má typ sdíleného přístupového podpisu `account` a je platná po dobu N dní.
- Ověřte, že se sdílený přístupový podpis uložil do trezoru klíčů jako tajný kód.
-
### <a name="set-variables"></a>Nastavení proměnných

Nejdřív nastavte proměnné, které budou používat rutiny prostředí PowerShell v následujících krocích. Nezapomeňte aktualizovat <YourStorageAccountName> <YourKeyVaultName> zástupné symboly a.

K získání kontextu vašeho účtu úložiště Azure použijeme také rutiny Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) .

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Vytvoření tokenu sdíleného přístupového podpisu

Pomocí rutin Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken) vytvořte definici sdíleného přístupového podpisu.

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Hodnota $sasToken bude vypadat podobně jako v tomto příkladu.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generování definice sdíleného přístupového podpisu

Pomocí rutiny Azure PowerShell [set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) vytvořte definici sdíleného přístupového podpisu.  Můžete zadat název svého výběru do `-Name` parametru.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Ověření definice sdíleného přístupového podpisu

Azure PowerShell pomocí rutiny [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) můžete ověřit, jestli je definice sdíleného přístupového podpisu uložená v trezoru klíčů.

Nejdřív v trezoru klíčů Najděte definici sdíleného přístupového podpisu.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Tajný klíč odpovídající vaší definici SAS bude mít tyto vlastnosti:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Teď můžete pomocí rutiny [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) s `VaultName` `Name` parametry a zobrazit obsah tohoto tajného klíče.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

Výstup tohoto příkazu zobrazí řetězec definice SAS.

## <a name="next-steps"></a>Další kroky

- [Ukázky klíčů spravovaného účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Reference k Key Vault PowerShellu](/powershell/module/az.keyvault/#key_vault)
