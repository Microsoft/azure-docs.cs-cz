---
title: Udělení oprávnění aplikacím pro přístup k trezoru klíčů Azure – Azure Key Vault | Microsoft Docs
description: Zjistěte, jak udělit oprávnění k mnoha aplikacím pro přístup k trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 10445aba9c52c5367a8ea03729462d14e2d51085
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707181"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Zajištění Key Vault ověřování pomocí zásad řízení přístupu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nejjednodušší způsob, jak ověřit cloudovou aplikaci, která je Key Vault, je spravovaná identita; Podrobnosti najdete v tématu [použití spravované identity App Service pro přístup k Azure Key Vault](managed-identity.md) .  Pokud vytváříte aplikaci Prem, provedete místní vývoj nebo jinak nemůžete použít spravovanou identitu, můžete místo toho zaregistrovat instanční objekt ručně a poskytnout přístup k trezoru klíčů pomocí zásad řízení přístupu.  

Trezor klíčů podporuje až 1024 záznamů zásad přístupu, přičemž každá položka uděluje zvláštní sadu oprávnění objektu zabezpečení: Jedná se například o způsob, jakým aplikace konzoly v [Azure Key Vault klientské knihovně pro .NET rychlý Start](quick-create-net.md) přistupuje k trezoru klíčů.

Úplné podrobnosti o Key Vault řízení přístupu najdete v tématu [Azure Key Vault Security: Správa identit a přístupu](overview-security.md#identity-and-access-management). Úplné podrobnosti o [klíčích, tajných klíčích a](about-keys-secrets-and-certificates.md) řízení přístupu k certifikátům najdete v těchto tématech: 

- [Řízení přístupu ke klíčům](about-keys-secrets-and-certificates.md#key-access-control)
- [Řízení přístupu k tajným klíčům](about-keys-secrets-and-certificates.md#secret-access-control)
- [Řízení přístupu k certifikátům](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Předpoklady

- Trezor klíčů. Můžete použít existující Trezor klíčů nebo vytvořit nový pomocí následujících kroků v jednom z těchto rychlých startů:
   - [Vytvoření trezoru klíčů pomocí Azure CLI](quick-create-cli.md)
   - [Vytvoření trezoru klíčů pomocí Azure PowerShell](quick-create-powershell.md)
   - [Vytvořte Trezor klíčů pomocí Azure Portal](quick-create-portal.md).
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview). Alternativně můžete použít [Azure Portal](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Každá položka zásad přístupu trezoru klíčů uděluje samostatné sadě oprávnění objektu zabezpečení:

- **Aplikace** Pokud je aplikace založená na cloudu, měli byste místo toho [použít spravovanou identitu pro přístup k Azure Key Vault](managed-identity.md), pokud je to možné.
- **Skupina Azure AD** I když Trezor klíčů podporuje jenom 1024 položek zásad přístupu, můžete přidat víc aplikací a uživatelů do jedné skupiny Azure AD a pak tuto skupinu přidat jako jednu položku do zásad řízení přístupu.
- **Uživatel** **Nedoporučuje**se uživatelům poskytnout přímý přístup k trezoru klíčů. V ideálním případě by se uživatelé měli přidat do skupiny Azure AD. tím se zase udělí přístup k trezoru klíčů. Viz [Azure Key Vault zabezpečení: Správa identit a přístupu](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Získat objectID

Pokud chcete aplikaci, skupině Azure AD udělit přístup k trezoru klíčů, musíte nejdřív získat jeho identifikátor objectId.

#### <a name="applications"></a>Aplikace

Identifikátor objectId pro aplikace odpovídá přidruženému objektu služby. Úplné podrobnosti o instančních objektech. Viz část [aplikace a objekty zabezpečení služby v Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Existují dva způsoby, jak získat identifikátor objectId pro aplikaci.  Prvním je registrace aplikace pomocí Azure Active Directory. Pokud to chcete provést, postupujte podle kroků v rychlém startu [Registrace aplikace s platformou Microsoft Identity](../active-directory/develop/quickstart-register-app.md). Po dokončení registrace bude identifikátor objectID uveden jako "aplikace (klienta)".

Druhým je Vytvoření instančního objektu v okně terminálu. Pomocí Azure CLI použijte příkaz [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) .

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

Identifikátor objectId bude uveden ve výstupu jako `clientID`.

Pomocí Azure PowerShell použijte rutinu [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0) .


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

Identifikátor objectId bude uveden ve výstupu jako `Id` (není `ApplicationId`).

#### <a name="azure-ad-groups"></a>Skupiny Azure AD

Do skupiny Azure AD můžete přidat víc aplikací a uživatelů a pak skupině udělit přístup k trezoru klíčů.  Další podrobnosti najdete v části [Vytvoření a přidání členů do skupiny Azure AD](#creating-and-adding-members-to-an-azure-ad-group) níže.

Pokud chcete najít ID objektu skupiny Azure AD pomocí Azure CLI, použijte příkaz [AZ AD Group list](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) . Z důvodu velkého počtu skupin, které mohou být ve vaší organizaci, byste měli také zadat vyhledávací řetězec pro parametr `--display-name`.

```azurecli-interactive
az ad group list --display-name <search-string>
```
Identifikátor objectId bude vrácen ve formátu JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

K vyhledání ID objektu skupiny Azure AD pomocí Azure PowerShell použijte rutinu [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) . Vzhledem k velkému počtu skupin, které mohou být ve vaší organizaci, pravděpodobně budete chtít zadat také vyhledávací řetězec pro parametr `-SearchString`.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Ve výstupu je identifikátor objectId uveden jako `Id`:

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Uživatelé

Můžete také přidat jednotlivé uživatele do zásad řízení přístupu trezoru klíčů. **Nedoporučujeme to.** Místo toho doporučujeme přidat uživatele do skupiny Azure AD a přidat skupinu do těchto zásad.

Pokud si přesto přejete najít uživatele pomocí Azure CLI, použijte příkaz [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) , který předáte e-mailovou adresu uživatele do parametru `--id`.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

Identifikátor objectId uživatele bude vrácen ve výstupu:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Pokud chcete najít uživatele s Azure PowerShell, použijte rutinu [Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) , která předá e-mailovou adresu uživatelů parametru `-UserPrincipalName`.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

Identifikátor objectId uživatele se vrátí ve výstupu jako `Id`.

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Udělte hlavnímu přístupu k trezoru klíčů.

Teď, když máte objectID objektu zabezpečení, můžete vytvořit zásady přístupu pro váš Trezor klíčů, který poskytuje oprávnění získat, vypsat, nastavit a odstranit pro klíče a tajné klíče a také všechna další oprávnění, která chcete.

Pomocí Azure CLI se to dělá předáním objectId do příkazu [AZ klíčů trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

V Azure PowerShell je to provedeno předáním objectId rutině [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ApplicationId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Vytváření a přidávání členů do skupiny Azure AD

Můžete vytvořit skupinu Azure AD, přidat do ní aplikace a uživatele a dát skupině přístup k trezoru klíčů.  Díky tomu můžete do trezoru klíčů přidat několik aplikací jako položku zásad jediného přístupu a eliminovat nutnost poskytnout uživatelům přímý přístup k vašemu trezoru klíčů (což se odrazí). Další podrobnosti najdete v tématu [Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Další požadavky

Kromě [výše uvedených požadavků](#prerequisites)budete potřebovat oprávnění k vytváření a úpravám skupin ve vašem tenantovi Azure Active Directory. Pokud nemáte oprávnění, možná budete muset kontaktovat správce Azure Active Directory.

Pokud máte v úmyslu používat PowerShell, budete také potřebovat [modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50) .

### <a name="create-an-azure-active-directory-group"></a>Vytvoření skupiny Azure Active Directory

Vytvořte novou Azure Active Directory skupinu pomocí příkazu Azure CLI [AZ AD Group Create](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) nebo rutiny Azure PowerShell [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0) .


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

V obou případech si poznamenejte u nově vytvořených identifikátorů ID skupiny, jak ho budete potřebovat pro následující kroky.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Najít objectId vašich aplikací a uživatelů

Můžete najít objectId vašich aplikací pomocí rozhraní příkazového řádku Azure pomocí příkazu [AZ AD SP list](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) s parametrem `--show-mine`.

```azurecli-interactive
az ad sp list --show-mine
```

Vyhledejte objectId vašich aplikací pomocí Azure PowerShell pomocí rutiny [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) a předejte vyhledávací řetězec do parametru `-SearchString`.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Chcete-li najít objectId uživatelů, postupujte podle kroků v části [Uživatelé](#users) výše.

### <a name="add-your-applications-and-users-to-the-group"></a>Přidat aplikace a uživatele do skupiny

Nyní přidejte objectId do nově vytvořené skupiny Azure AD.

Pomocí Azure CLI použijte příkaz [AZ AD Group member Add](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)a předejte objectId parametru `--member-id`.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

Pomocí Azure PowerShell použijte rutinu [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) a předejte objectId parametru `-MemberObjectId`.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Udělte skupině AD přístup k vašemu trezoru klíčů.

Nakonec udělte skupině AD oprávnění k vašemu trezoru klíčů pomocí rozhraní příkazového řádku Azure CLI [AZ Key trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) nebo rutiny Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) . Příklady najdete v části [poskytnutí aplikace, skupiny Azure AD nebo přístupu uživatele k sekci trezoru klíčů](#give-the-principal-access-to-your-key-vault) výše.

Aplikace taky potřebuje alespoň jednu roli správy identit a přístupu (IAM) přiřazenou k trezoru klíčů. V opačném případě se nebude moci přihlásit a nezdaří se jim nedostatečná oprávnění pro přístup k předplatnému.

## <a name="next-steps"></a>Další kroky

- [Azure Key Vault zabezpečení: Správa identit a přístupu](overview-security.md#identity-and-access-management)
- [Zajištění Key Vault ověřování pomocí App Service spravované identity](managed-identity.md)
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- [Zabezpečte svůj Trezor klíčů](key-vault-secure-your-key-vault.md).
- [Azure Key Vault příručka pro vývojáře](key-vault-developers-guide.md)
- Kontrola [Azure Key Vault osvědčených postupů](key-vault-best-practices.md)
