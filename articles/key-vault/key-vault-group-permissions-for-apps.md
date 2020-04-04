---
title: Udělit oprávnění aplikacím pro přístup k trezoru klíčů Azure – Trezor klíčů Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak udělit oprávnění mnoha aplikacím pro přístup k trezoru klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/27/2019
ms.author: mbaldwin
ms.openlocfilehash: a3be46f2b50afbe2347445a8b3220c74d1bfc52c
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656985"
---
# <a name="provide-key-vault-authentication-with-an-access-control-policy"></a>Poskytnutí ověřování trezoru klíčů pomocí zásad řízení přístupu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Nejjednodušší způsob ověření cloudové aplikace do trezoru klíčů je se spravovanou identitou; podrobnosti najdete [v tématu Použití spravované identity služby App Service pro přístup k azure key vault](managed-identity.md) pro podrobnosti.  Pokud vytváříte aplikaci on-prem, provádíte místní vývoj nebo jinak nemůžete použít spravovanou identitu, můžete místo toho zaregistrovat instanční objekt ručně a poskytnout přístup k trezoru klíčů pomocí zásad řízení přístupu.  

Trezor klíčů podporuje až 1024 položek zásad přístupu, přičemž každá položka uděluje odlišnou sadu oprávnění "jistině": Například takto aplikace konzoly v [klientské knihovně Azure Key Vault pro rychlý start rozhraní .NET](quick-create-net.md) přistupuje k trezoru klíčů.

Podrobné informace o řízení přístupu trezoru klíčů najdete v [tématu Azure Key Vault zabezpečení: Správa identit a přístupu](overview-security.md#identity-and-access-management). Úplné podrobnosti o řízení [přístupu klíče, tajné klíče a certifikáty](about-keys-secrets-and-certificates.md) naleznete v tématu: 

- [Řízení přístupu ke klíčům](about-keys-secrets-and-certificates.md#key-access-control)
- [Řízení přístupu k tajným kódům](about-keys-secrets-and-certificates.md#secret-access-control)
- [Řízení přístupu k certifikátům](about-keys-secrets-and-certificates.md#certificate-access-control)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

- Trezor klíčů. Můžete použít existující trezor klíčů nebo vytvořit nový podle kroků v jednom z těchto rychlých startů:
   - [Vytvoření trezoru klíčů pomocí příkazového příkazového příkazu Azure](quick-create-cli.md)
   - [Vytvoření trezoru klíčů pomocí Azure PowerShellu](quick-create-powershell.md)
   - [Vytvořte trezor klíčů pomocí portálu Azure](quick-create-portal.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview). Případně můžete použít [portál Azure](https://portal.azure.com).

## <a name="grant-access-to-your-key-vault"></a>Udělení přístupu do trezoru klíčů

Každá položka zásad přístupu k trezoru klíčů uděluje odlišné sadě oprávnění objektu zabezpečení:

- **Aplikace** Pokud je aplikace založená na cloudu, měli byste místo toho [použít spravovanou identitu pro přístup k Azure Key Vault](managed-identity.md), pokud je to možné
- **Skupina Azure AD** Přestože trezor klíčů podporuje pouze položky zásad přístupu 1024, můžete přidat více aplikací a uživatelů do jedné skupiny Azure AD a pak přidat tuto skupinu jako jednu položku do zásad řízení přístupu.
- **Uživatel** Nedoporučujeme uživatelům přímý přístup k trezoru klíčů **.** V ideálním případě by uživatelé měli být přidáni do skupiny Azure AD, která je zase udělen přístup k trezoru klíčů. Viz [Zabezpečení trezoru klíčů Azure: Správa identit a přístupu](overview-security.md#identity-and-access-management).


### <a name="get-the-objectid"></a>Získat id objektu

Chcete-li poskytnout aplikaci, skupině Azure AD nebo přístup u uživatelů k trezoru klíčů, musíte nejprve získat jeho objectId.

#### <a name="applications"></a>Aplikace

ObjectId pro aplikace odpovídá jeho přidružené instanční objekt. Podrobné informace o instančních objektech. viz [Objekty zaregistrovaný objekty aplikace a služby ve službě Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Existují dva způsoby, jak získat objectId pro aplikaci.  Prvním z vás je registrace aplikace ve službě Azure Active Directory. Postupujte podle pokynů v rychlém startu [Registrace aplikace s platformou microsoft identity](../active-directory/develop/quickstart-register-app.md). Po dokončení registrace bude id objektu uveden jako "ID aplikace (klienta".

Druhým je vytvoření instančního objektu v okně terminálu. Pomocí příkazového příkazu Azure CLI použijte příkaz [az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

```azurecli-interactive
az ad sp create-for-rbac -n "http://mySP"
```

Id objectId bude uveden `clientID`ve výstupu jako .

V prostředí Azure PowerShell použijte rutinu [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal?view=azps-2.7.0)


```azurepowershell-interactive
New-AzADServicePrincipal -DisplayName mySP
```

Id objectId bude uveden `Id` ve `ApplicationId`výstupu jako (ne ).

#### <a name="azure-ad-groups"></a>Azurové sestavy

Do skupiny Azure AD můžete přidat více aplikací a uživatelů a pak skupině udělit přístup k trezoru klíčů.  Další podrobnosti najdete [v tématu vytváření a přidávání členů do části skupiny Azure AD,](#creating-and-adding-members-to-an-azure-ad-group) níže.

Chcete-li najít objektId skupiny Azure AD s Azure CLI, použijte příkaz [az az ad group seznam.](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-list) Vzhledem k velkému počtu skupin, které mohou být ve vaší organizaci, měli byste také zadat vyhledávací řetězec k parametru. `--display-name`

```azurecli-interactive
az ad group list --display-name <search-string>
```
ObjectId budou vráceny v JSON:

```json
    "objectId": "48b21bfb-74d6-48d2-868f-ff9eeaf38a64",
    "objectType": "Group",
    "odata.type": "Microsoft.DirectoryServices.Group",
```

Chcete-li najít objectId skupiny Azure AD s Azure PowerShell, použijte [rutinu Get-AzADGroup.](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) Vzhledem k velkému počtu skupin, které mohou být ve vaší organizaci, budete `-SearchString` pravděpodobně chtít také poskytnout vyhledávací řetězec k parametru.

```azurepowershell-interactive
Get-AzADGroup -SearchString <search-string>
```

Ve výstupu je id objektu `Id`uveden jako :

```console
...
Id                    : 1cef38c4-388c-45a9-b5ae-3d88375e166a
...
```

#### <a name="users"></a>Uživatelé

Můžete také přidat jednotlivého uživatele do zásad řízení přístupu trezoru klíčů. **Nedoporučujeme to.** Místo toho doporučujeme přidat uživatele do skupiny Azure AD a přidat skupinu na zásady.

Pokud přesto chcete najít uživatele pomocí rozhraní příkazového řádku Azure, použijte příkaz [az ad](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-show) user `--id` show a předáte parametru e-mailovou adresu uživatelů.


```azurecli-interactive
az ad user show --id <email-address-of-user>
```

Id objektu uživatele bude vrácena ve výstupu:

```console
  ...
  "objectId": "f76a2a6f-3b6d-4735-9abd-14dccbf70fd9",
  "objectType": "User",
  ...
```

Chcete-li najít uživatele s Azure PowerShell, použijte [rutinu Get-AzADUser](/powershell/module/az.resources/get-azaduser?view=azps-2.7.0) `-UserPrincipalName` a předavá teču e-mailové adresy uživatelů.

```azurepowershell-interactive
 Get-AzAdUser -UserPrincipalName <email-address-of-user>
```

Id objektu uživatele bude vrácena ve `Id`výstupu jako .

```console
...
Id                : f76a2a6f-3b6d-4735-9abd-14dccbf70fd9
Type              :
```

### <a name="give-the-principal-access-to-your-key-vault"></a>Poskytnutí hlavního přístupu k trezoru klíčů

Nyní, když máte objectID objektu vašeho hlavního povinného, můžete vytvořit zásady přístupu pro trezor klíčů, který mu dává získat, seznam, nastavit a odstranit oprávnění pro klíče i tajné klíče, plus všechna další oprávnění, která chcete.

S Azure CLI se to provádí předáním objectId [příkazu az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

```azurecli-interactive
az keyvault set-policy -n <your-unique-keyvault-name> --spn <ApplicationID-of-your-service-principal> --secret-permissions get list set delete --key-permissions create decrypt delete encrypt get list unwrapKey wrapKey
```

S Azure PowerShell, to se provádí předáním objectId [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) rutina. 

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy –VaultName <your-key-vault-name> -PermissionsToKeys create,decrypt,delete,encrypt,get,list,unwrapKey,wrapKey -PermissionsToSecrets get,list,set,delete -ObjectId <Id>

```

## <a name="creating-and-adding-members-to-an-azure-ad-group"></a>Vytváření a přidávání členů do skupiny Azure AD

Můžete vytvořit skupinu Azure AD, přidat aplikace a uživatele do skupiny a udělit skupině přístup k trezoru klíčů.  To umožňuje přidat několik aplikací do trezoru klíčů jako položku zásad jednoho přístupu a eliminuje potřebu poskytnout uživatelům přímý přístup k trezoru klíčů (což nedoporučujeme). Další podrobnosti najdete [v tématu Správa přístupu k aplikacím a prostředkům pomocí skupin Azure Active Directory](../active-directory/fundamentals/active-directory-manage-groups.md).

### <a name="additional-prerequisites"></a>Další požadavky

Kromě výše [uvedených předpokladů](#prerequisites)budete potřebovat oprávnění k vytváření a úpravám skupin v tenantovi služby Azure Active Directory. Pokud nemáte oprávnění, možná budete muset kontaktovat správce služby Azure Active Directory.

Pokud máte v úmyslu používat PowerShell, budete také potřebovat [modul Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/2.0.2.50)

### <a name="create-an-azure-active-directory-group"></a>Vytvoření skupiny Služby Azure Active Directory

Vytvořte novou skupinu Azure Active Directory pomocí příkazu [vytvořit az az](/cli/azure/ad/group?view=azure-cli-latest#az-ad-group-create) služby Azure CLI nebo rutiny Azure PowerShell [New-AzureADGroup.](/powershell/module/azuread/new-azureadgroup?view=azureadps-2.0)


```azurecli-interactive
az ad group create --display-name <your-group-display-name> --mail-nickname <your-group-mail-nickname>
```

```powershell
New-AzADGroup -DisplayName <your-group-display-name> -MailNickName <your-group-mail-nickname>
```

V obou případech poznamenejte si nově vytvořené skupiny GroupId, jak budete potřebovat pro níže uvedené kroky.

### <a name="find-the-objectids-of-your-applications-and-users"></a>Najděte objekty Ids vašich aplikací a uživatelů

Můžete najít objectIds vašich aplikací pomocí příkazu Azure CLI s [az ad sp seznam](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-list) příkazu, s parametrem. `--show-mine`

```azurecli-interactive
az ad sp list --show-mine
```

Najděte objekty Ids vašich aplikací pomocí Azure PowerShell s [Rutina Get-AzADServicePrincipal,](/powershell/module/az.resources/get-azadserviceprincipal?view=azps-2.7.0) předávání vyhledávací řetězec `-SearchString` na parametr.

```azurepowershell-interactive
Get-AzADServicePrincipal -SearchString <search-string>
```

Chcete-li najít objektIds vašich uživatelů, postupujte podle pokynů v části [Uživatelé,](#users) výše.

### <a name="add-your-applications-and-users-to-the-group"></a>Přidání aplikací a uživatelů do skupiny

Teď přidejte objectIds do nově vytvořené skupiny Azure AD.

Pomocí rozhraní příkazového řádku Azure použijte [přidání člena az reklamní](/cli/azure/ad/group/member?view=azure-cli-latest#az-ad-group-member-add)sestavy `--member-id` , který parametru předá id objectId.


```azurecli-interactive
az ad group member add -g <groupId> --member-id <objectId>
```

V prostředí Azure PowerShell použijte rutinu [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember?view=azps-2.7.0) a předajte `-MemberObjectId` parametru id objectId.

```azurepowershell-interactive
Add-AzADGroupMember -TargetGroupObjectId <groupId> -MemberObjectId <objectId> 
```

### <a name="give-the-ad-group-access-to-your-key-vault"></a>Udělit skupině AD přístup k trezoru klíčů

Nakonec udělte skupině AD oprávnění k trezoru klíčů pomocí příkazu azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) nebo rutiny Azure PowerShell [Set-AzKeyVaultAccessPolicy.](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy?view=azps-2.7.0) Příklady najdete v tématu [Dát aplikaci, skupina Azure AD nebo uživatelský přístup k oddílu trezoru klíčů](#give-the-principal-access-to-your-key-vault) výše.

Aplikace také potřebuje alespoň jednu roli správy identit y a přístupu (IAM) přiřazenou k trezoru klíčů. V opačném případě se nebude moci přihlásit a nebude mít dostatečná práva pro přístup k předplatnému.

## <a name="next-steps"></a>Další kroky

- [Zabezpečení azure trezoru klíčů: Správa identit a přístupu](overview-security.md#identity-and-access-management)
- [Poskytnutí ověřování trezoru klíčů pomocí spravované identity služby App Service](managed-identity.md)
- [Informace o klíčích, tajných kódech a certifikátech](about-keys-secrets-and-certificates.md)
- [Zabezpečte trezor klíčů](key-vault-secure-your-key-vault.md).
- [Průvodce vývojářem azure key vaultu](key-vault-developers-guide.md)
- Kontrola [doporučených postupů azure key vaultu](key-vault-best-practices.md)
