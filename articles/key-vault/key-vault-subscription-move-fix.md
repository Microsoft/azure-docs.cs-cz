---
title: Změna ID tenanta trezoru klíčů po přesunu předplatného Azure Key Vault | Microsoft Docs
description: Přečtěte si, jak přepnout ID tenanta pro Trezor klíčů po přesunu předplatného na jiného tenanta.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 3819742e82fe6877b6a1aa58e52eec01b6b05515
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72001242"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Změna ID tenanta trezoru klíčů po přesunu předplatného

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


Když vytvoříte nový trezor klíčů v rámci předplatného, automaticky se přiřadí k výchozímu Azure Active Directorymu ID tenanta daného předplatného. Všechny položky zásad přístupu jsou také vázané na toto ID tenanta. 

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

Teď, když je váš trezor přidružený ke správnému ID tenanta a staré položky zásad přístupu se odeberou, nastavte nové položky zásad přístupu pomocí rutiny Azure PowerShell [set-AzKeyVaultAccessPolicy](https://powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) nebo pomocí příkazu Azure CLI [AZ klíčů trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) .

Pokud používáte spravovanou identitu pro prostředky Azure, budete ji muset aktualizovat taky na nového tenanta Azure AD. Další informace o spravovaných identitách najdete v tématu [zajištění Key Vaultho ověřování pomocí spravované identity](managed-identity.md).


Pokud používáte MSI, budete muset aktualizovat taky identitu MSI, protože stará identita už nebude ve správném tenantovi AAD.

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy týkající se Azure Key Vault, navštivte [fóra Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
