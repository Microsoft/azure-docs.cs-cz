---
title: Změna ID klienta trezoru klíčů po přesunutí předplatného – Azure Key Vault | Dokumenty společnosti Microsoft
description: Zjistěte, jak přepnout ID tenanta pro trezor klíčů po přesunu předplatného na jiného tenanta
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 2b262823323e9491965bc16818220b2f80cf236a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79457300"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Změna ID tenanta trezoru klíčů po přesunu předplatného

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Když v rámci předplatného vytvoříte nový trezor klíčů, je automaticky vázán na výchozí ID tenanta služby Azure Active Directory pro dané předplatné. Zároveň jsou k tomuto ID tenanta vázány i všechny položky zásad přístupu. 

Pokud přesunete předplatné Azure z tenanta A do tenanta B, vaše stávající trezory klíčů jsou nepřístupné objekty (uživatelé a aplikace) v tenantovi B. Chcete-li tento problém vyřešit, musíte:

* Změňte ID klienta přidružené ke všem existujícím trezorům klíčů v předplatném klienta B.
* Odeberte všechny stávající položky zásad přístupu.
* Přidejte nové položky zásad přístupu přidružené k tenantovi B.

Například pokud máte trezor klíčů 'myvault' v předplatném, který byl přesunut z tenanta A do tenanta B, můžete použít Azure PowerShell změnit ID klienta a odebrat staré zásady přístupu.

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.
````

Nebo můžete použít azure cli.

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Teď, když je váš trezor přidružený ke správnému ID klienta a starým položkám zásad přístupu, nastavte nové položky zásad přístupu pomocí rutiny Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) nebo příkazu Azure CLI [az az keyvault set-policy.](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy)

Pokud používáte spravovanou identitu pro prostředky Azure, budete ji muset aktualizovat i na nového klienta Azure AD. Další informace o spravovaných identitách naleznete v [tématu Poskytování ověřování trezoru klíčů se spravovanou identitou](managed-identity.md).


Pokud používáte MSI, budete také muset aktualizovat identitu MSI, protože stará identita již nebude ve správném tenantovi AAD.

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy ke službě Azure Key Vault, navštivte [fóra služby Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
