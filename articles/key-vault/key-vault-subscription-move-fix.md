---
title: Změna ID tenanta trezoru klíčů po přesunu předplatného Azure Key Vault | Microsoft Docs
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
ms.openlocfilehash: 4531d3bdeab96cb4a753060d59d07e155f4a358e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197330"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Změna ID tenanta trezoru klíčů po přesunu předplatného

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Když v rámci předplatného vytvoříte nový trezor klíčů, je automaticky vázán na výchozí ID tenanta služby Azure Active Directory pro dané předplatné. Zároveň jsou k tomuto ID tenanta vázány i všechny položky zásad přístupu. 

Pokud přesunete předplatné Azure z tenanta A na tenanta B, stávající trezory klíčů budou pro objekty zabezpečení (uživatelé a aplikace) v tenantovi B nepřístupné. Chcete-li tento problém vyřešit, je třeba provést následující kroky:

* Změňte ID tenanta přidružené ke všem stávajícím trezorům klíčů v předplatném na tenanta B.
* Odeberte všechny stávající položky zásad přístupu.
* Přidejte nové položky zásad přístupu přidružené k tenantovi B.

Pokud máte například Trezor klíčů "myvault" v předplatném, které bylo přesunuto z tenanta A na tenanta B, můžete pomocí Azure PowerShell změnit ID tenanta a odebrat staré zásady přístupu.

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

Nebo můžete použít rozhraní příkazového řádku Azure CLI.

```azurecli
az account set <your-subscriptionId>                                       # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```

Teď, když je váš trezor přidružený ke správnému ID tenanta a staré položky zásad přístupu se odeberou, nastavte nové položky zásad přístupu pomocí rutiny Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) nebo pomocí příkazu Azure CLI [AZ klíčů trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

Pokud používáte spravovanou identitu pro prostředky Azure, budete ji muset aktualizovat taky na nového tenanta Azure AD. Další informace o spravovaných identitách najdete v tématu [zajištění Key Vaultho ověřování pomocí spravované identity](managed-identity.md).


Pokud používáte MSI, budete muset aktualizovat taky identitu MSI, protože stará identita už nebude ve správném tenantovi AAD.

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy ke službě Azure Key Vault, navštivte [fóra služby Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
