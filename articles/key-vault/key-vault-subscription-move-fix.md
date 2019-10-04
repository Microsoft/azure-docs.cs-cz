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
ms.openlocfilehash: 87025767725142cc2f861ff8b390d6ea916f8e38
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947734"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Změna ID tenanta trezoru klíčů po přesunu předplatného

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>Otázka: moje předplatné bylo přesunuto z tenanta A na tenanta B. Návody změnit ID tenanta pro stávající Trezor klíčů a nastavit správné seznamy ACL pro objekty zabezpečení v tenantovi B?

Když vytvoříte nový trezor klíčů v rámci předplatného, automaticky se přiřadí k výchozímu Azure Active Directorymu ID tenanta daného předplatného. Všechny položky zásad přístupu jsou také vázané na toto ID tenanta. Když přesunete předplatné Azure z tenanta A na tenanta B, stávající trezory klíčů budou pro objekty zabezpečení (uživatelé a aplikace) v tenantovi B nepřístupné. Chcete-li tento problém vyřešit, je třeba provést následující kroky:

* Změňte ID tenanta přidružené ke všem stávajícím trezorům klíčů v tomto předplatném na tenanta B.
* Odeberte všechny stávající položky zásad přístupu.
* Přidejte nové položky zásad přístupu, které jsou přidružené k tenantovi B.

Pokud máte například Trezor klíčů "myvault" v předplatném, které bylo přesunuto z tenanta A na tenanta B, zde je postup, jak změnit ID tenanta pro tento trezor klíčů a odebrat staré zásady přístupu.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID                   # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your Keyvault's Resource ID 
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your Keyvault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your Keyvault resides in
$vault.Properties.AccessPolicies = @()                                     # Accesspolicies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                                                                              # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the kevault's properties.
</pre>

Vzhledem k tomu, že tento trezor byl v tenantovi A před přesunem, původní hodnota **$Vault. Vlastnost. TenantId** je tenant A, zatímco **(Get-AzContext). Tenant. TenantId** je tenant B.

Teď, když je váš trezor přidružený ke správnému ID tenanta a staré položky zásad přístupu se odeberou, nastavte nové položky zásad přístupu pomocí [set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

Pokud používáte MSI, budete muset aktualizovat taky identitu MSI, protože stará identita už nebude ve správném tenantovi AAD.

## <a name="next-steps"></a>Další kroky

Pokud máte dotazy týkající se Azure Key Vault, navštivte [fóra Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
