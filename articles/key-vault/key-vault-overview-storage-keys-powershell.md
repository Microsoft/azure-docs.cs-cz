---
title: Účet spravovaného úložiště Azure Key Vault – verze PowerShellu
description: Funkce účtu spravovaného úložiště poskytuje bezproblémovou integraci mezi Azure Key Vault a účtem úložiště Azure.
ms.topic: conceptual
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.openlocfilehash: 833f78d89a1a9033e62c10c3b16c5adfc65e1da4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78195086"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Správa klíčů účtů úložiště pomocí trezoru klíčů a Azure PowerShellu

Účet úložiště Azure používá přihlašovací údaje obsahující název účtu a klíč. Klíč je automaticky vygenerován a slouží jako heslo, nikoli jako kryptografický klíč. Trezor klíčů spravuje klíče účtů úložiště jejich uložením jako [tajné klíče trezoru klíčů](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-secrets). 

Pomocí funkce klíče spravovaného úložiště spravovaného úložiště úložiště key vault upsat (synchronizovat) klíče s účtem úložiště Azure a pravidelně je regenerovat (otáčet). Klíče můžete spravovat jak pro účty úložiště, tak pro klasické účty úložiště.

Při použití funkce klíče účtu spravovaného úložiště zvažte následující body:

- Hodnoty klíče jsou nikdy vráceny v reakci na volajícího.
- Klíče účtu úložiště by měl spravovat pouze trezor klíčů. Nespravujte klíče sami a nezasahujte do procesů trezoru klíčů.
- Klíče účtu úložiště by měl spravovat pouze jeden objekt trezoru klíčů. Nepovolujte správu klíčů z více objektů.
- Trezor klíčů můžete požádat o správu účtu úložiště pomocí uživatelského objektu, ale ne pomocí instančního objektu.
- Obnovte klíče pouze pomocí trezoru klíčů. Neobnovujte ručně klíče účtu úložiště.

Doporučujeme používat integraci Azure Storage s Azure Active Directory (Azure AD), cloudovou službou správy identit a přístupu Microsoftu. Integrace Azure AD je dostupná pro [objekty BLOB azure a fronty](../storage/common/storage-auth-aad.md)a poskytuje přístup k Azure Storage založený na tokenech OAuth2 (stejně jako Azure Key Vault).

Azure AD umožňuje ověřit klientskou aplikaci pomocí aplikace nebo identity uživatele, namísto přihlašovacích údajů účtu úložiště. Identitu [spravanou azure ad](/azure/active-directory/managed-identities-azure-resources/) můžete použít při spuštění v Azure. Spravované identity odeberou potřebu ověřování klientů a ukládání přihlašovacích údajů v aplikaci nebo s ní.

Azure AD používá řízení přístupu na základě rolí (RBAC) ke správě autorizace, která je také podporována trezoru klíčů.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>ID aplikace instančního objektu

Klient Azure AD poskytuje každé registrované aplikaci [s instančním objektem](/azure/active-directory/develop/developer-glossary#service-principal-object). Instanční objekt slouží jako ID aplikace, které se používá při nastavení autorizace pro přístup k jiným prostředkům Azure prostřednictvím RBAC.

Trezor klíčů je aplikace Microsoftu, která je předem registrovaná ve všech klientech Azure AD. Trezor klíčů se zaeviduje pod stejným ID aplikace v každém cloudu Azure.

| Tenanti | Cloud | ID aplikace |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Veřejný Azure | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Ostatní  | Všechny | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Požadavky

Chcete-li tuto příručku dokončit, musíte nejprve provést následující kroky:

- [Nainstalujte modul Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.6.0).
- [Vytvoření trezoru klíčů](quick-create-powershell.md)
- [Vytvořte účet úložiště Azure](../storage/common/storage-account-create.md?tabs=azure-powershell). Název účtu úložiště musí používat pouze malá písmena a čísla. Délka názvu musí být mezi 3 a 24 znaky.
      

## <a name="manage-storage-account-keys"></a>Správa klíčů účtu úložiště

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Ověřte relaci prostředí PowerShell pomocí rutiny [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 

```azurepowershell-interactive
Connect-AzAccount
```
Pokud máte více předplatných Azure, můžete je uvést pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0) a zadat předplatné, které chcete použít s rutinou [Set-AzContext.](/powershell/module/az.accounts/set-azcontext?view=azps-2.5.0) 

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Nastavení proměnných

Nejprve nastavte proměnné, které mají být použity rutiny Prostředí PowerShell v následujících krocích. Nezapomeňte aktualizovat <YourResourceGroupName>, <YourStorageAccountName>a <YourKeyVaultName> zástupné symboly a `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` nastavit $keyVaultSpAppId na (jak je uvedeno v [ID aplikace instančního objektu](#service-principal-application-id), výše).

Budeme také používat rutiny Azure PowerShell [Get-AzContext](/powershell/module/az.accounts/get-azcontext?view=azps-2.6.0) a [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-2.6.0) k získání vašeho ID uživatele a kontextu vašeho účtu úložiště Azure.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1"

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName
```

### <a name="give-key-vault-access-to-your-storage-account"></a>Poznamte trezoru klíčů přístup k účtu úložiště

Před key vault přístup a spravovat klíče účtu úložiště, musíte autorizovat jeho přístup k účtu úložiště. Aplikace Trezor klíčů vyžaduje oprávnění k *vypáčit* a *znovu vygenerovat* klíče pro váš účet úložiště. Tato oprávnění jsou povolena prostřednictvím integrované role RBAC [role Úložiště účtu Key Operator role service role](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role). 

Přiřaďte tuto roli k instančnímu objektu trezoru klíčů, který omezuje obor na váš účet úložiště pomocí rutiny Azure PowerShell [New-AzRoleAssignment.](/powershell/module/az.resources/new-azroleassignment?view=azps-2.6.0)

```azurepowershell-interactive
# Assign RBAC role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role." 
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Po úspěšném přiřazení role byste měli vidět výstup podobný následujícímu příkladu:

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

Pokud trezor klíčů již byl přidán do role na vašem účtu úložiště, obdržíte *"Přiřazení role již existuje."* . Přiřazení role můžete také ověřit pomocí stránky Účet úložiště "Řízení přístupu (IAM)" na webu Azure Portal.  

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Udělit svému uživatelskému účtu oprávnění k účtům spravovaného úložiště

Pomocí rutiny Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.6.0) aktualizujte zásady přístupu trezoru klíčů a udělte uživatelský účet oprávnění účtu úložiště.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Upozorňujeme, že oprávnění pro účty úložiště nejsou dostupná na stránce Zásady přístupu na webu Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Přidání účtu spravovaného úložiště do instance úložiště klíčů

Pomocí rutiny [Add-AzKeyVaultStorageAccount služby](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) Azure PowerShell můžete vytvořit účet spravovaného úložiště v instanci trezoru klíčů. Přepínač `-DisableAutoRegenerateKey` určuje, že není znovu vygenerovat klíče účtu úložiště.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

Po úspěšném přidání účtu úložiště bez regenerace klíče byste měli vidět výstup podobný následujícímu příkladu:

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

### <a name="enable-key-regeneration"></a>Povolit regeneraci klíčů

Pokud chcete, aby trezor klíčů pravidelně regeneroval klíče účtu úložiště, můžete použít rutinu [Add-AzKeyVaultStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount?view=azps-2.6.0) azure prostředí k nastavení období regenerace. V tomto příkladu jsme nastavili období regenerace tří dnů. Po třech dnech trezor klíčů regeneruje 'key2' a vyměňuje aktivní klíč z 'key2' na 'key1'.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

Po úspěšném přidání účtu úložiště s regenerací klíčů byste měli vidět výstup podobný následujícímu příkladu:

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

## <a name="shared-access-signature-tokens"></a>Tokeny sdílených přístupových podpisů

Trezor klíčů můžete také požádat o vygenerování tokenů podpisu sdíleného přístupu. Sdílený přístupový podpis poskytuje delegovaný přístup k prostředkům v účtu úložiště. Klientům můžete udělit přístup k prostředkům v účtu úložiště bez sdílení klíčů účtu. Sdílený přístupový podpis poskytuje bezpečný způsob sdílení prostředků úložiště bez ohrožení klíčů účtu.

Příkazy v této části dokončí následující akce:

- Nastavte definici podpisu sdíleného přístupu účtu. 
- Vytvořte token podpisu sdíleného přístupu účtu pro služby blob, soubor, tabulka a fronty. Token je vytvořen pro typy prostředků Service, Container a Object. Token je vytvořen se všemi oprávněními, přes https a se zadaným počátečním a koncovým datem.
- Nastavte v trezoru definici sdíleného přístupového podpisu spravovaného úložiště úložiště klíčů. Definice obsahuje šablonu URI tokenu sdíleného přístupového podpisu, který byl vytvořen. Definice má typ sdíleného `account` přístupového podpisu a je platná pro N dní.
- Ověřte, zda byl sdílený přístupový podpis uložen v trezoru klíčů jako tajný klíč.
- 
### <a name="set-variables"></a>Nastavení proměnných

Nejprve nastavte proměnné, které mají být použity rutiny Prostředí PowerShell v následujících krocích. Nezapomeňte aktualizovat zástupné symboly <YourStorageAccountName> a. <YourKeyVaultName>

Budeme také používat rutiny Azure PowerShell [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext?view=azps-2.6.0) k získání kontextu vašeho účtu úložiště Azure.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1
```

### <a name="create-a-shared-access-signature-token"></a>Vytvoření tokenu sdíleného přístupového podpisu

Vytvořte definici sdíleného přístupového podpisu pomocí rutin Azure PowerShell [New-AzStorageAccountSASToken.](/powershell/module/az.storage/new-azstorageaccountsastoken?view=azps-2.6.0)
 
```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
Hodnota $sasToken bude vypadat podobně jako tento.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Generování definice sdíleného přístupového podpisu

Pomocí rutiny Azure PowerShell [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition?view=azps-2.6.0) vytvořte definici sdíleného přístupového podpisu.  Můžete zadat název dle vašeho `-Name` výběru parametru.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>Ověření definice sdíleného přístupového podpisu

Můžete ověřit, že definice sdíleného přístupového podpisu byla uložena v trezoru klíčů pomocí rutiny Azure PowerShell [Get-AzKeyVaultSecret.](/powershell/module/az.keyvault/get-azkeyvaultsecret?view=azps-2.6.0)

Nejprve vyhledejte definici sdíleného přístupového podpisu v trezoru klíčů.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Tajný klíč odpovídající definici SAS bude mít tyto vlastnosti:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Nyní můžete použít [Rutina Get-AzKeyVaultSecret](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) a `Name` tajné vlastnosti k zobrazení obsahu tohoto tajného klíče.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>

Write-Host $secret.SecretValueText
```

Výstup tohoto příkazu zobrazí řetězec definice SAS.


## <a name="next-steps"></a>Další kroky

- [Ukázky klíčů účtu spravovaného úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- [Odkaz na prostředí PowerShell trezoru klíčů](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)
