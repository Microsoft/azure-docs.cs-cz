---
title: Změna ID tenanta trezoru klíčů po přesunu předplatného - Azure Key Vault | Dokumentace Microsoftu
description: Zjistěte, jak přepnout ID tenanta pro trezor klíčů po přesunu předplatného na jiného tenanta
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 46d7bc21-fa79-49e4-8c84-032eef1d813e
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 975a020cb6dee5881c6addaafa0cf7d2b75368be
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996266"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Změna ID tenanta trezoru klíčů po přesunu předplatného

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>DOTAZ: Moje předplatné se přesunulo z tenanta A na tenanta B. Jak změním ID tenanta pro stávající trezor klíčů a nastavím správné seznamy ACL pro objekty zabezpečení v tenantovi B?

Když v rámci předplatného vytvoříte nový trezor klíčů, je automaticky vázán na výchozí ID tenanta služby Azure Active Directory pro dané předplatné. Zároveň jsou k tomuto ID tenanta vázány i všechny položky zásad přístupu. Když přesunete předplatné Azure z tenanta A na tenanta B, stávající trezory klíčů budou pro objekty zabezpečení (uživatelé a aplikace) v tenantu B nepřístupné. Oprava tohoto problému vyžaduje následující postup:

* Změňte ID tenanta přidružené ke všem stávajícím trezorům klíčů v tomto předplatném na tenanta B.
* Odeberte všechny stávající položky zásad přístupu.
* Přidejte nové položky zásad přístupu, které jsou přidružené k tenantu B.

Například pokud máte trezor klíčů s názvem „muj_trezor“ v předplatném, které bylo přesunuto z tenanta A na tenanta B, následujícím postupem změníte ID tenanta pro tento trezor klíčů a odeberete staré zásady přístupu.

<pre>
Select-AzSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId
$vault = Get-AzResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Protože byl tento trezor v tenantu A před přesunem, původní hodnota **$vault. Properties.TenantId** je tenant A, přestože **(Get-AzContext). Tenant.TenantId** je tenant B.

Teď, když je váš trezor přiřazen ke správnému ID tenanta a staré položky zásad přístupu jsou odebrány, nastavte nové položky zásad přístupu pomocí [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy).

## <a name="next-steps"></a>Další postup

Pokud máte dotazy ke službě Azure Key Vault, navštivte [fóra služby Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
