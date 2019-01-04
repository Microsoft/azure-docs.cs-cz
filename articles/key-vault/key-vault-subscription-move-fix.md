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
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: ambapat
ms.openlocfilehash: 185a1954b1dbc3f56864b75fdbb32bdf9f18bc1a
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002225"
---
# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Změna ID tenanta trezoru klíčů po přesunu předplatného

## <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>DOTAZ: Moje předplatné se přesunulo z tenanta A na tenanta B. Jak změním ID tenanta pro stávající trezor klíčů a nastavím správné seznamy ACL pro objekty zabezpečení v tenantovi B?

Když v rámci předplatného vytvoříte nový trezor klíčů, je automaticky vázán na výchozí ID tenanta služby Azure Active Directory pro dané předplatné. Zároveň jsou k tomuto ID tenanta vázány i všechny položky zásad přístupu. Když přesunete předplatné Azure z tenanta A na tenanta B, stávající trezory klíčů budou pro objekty zabezpečení (uživatelé a aplikace) v tenantu B nepřístupné. Oprava tohoto problému vyžaduje následující postup:

* Změňte ID tenanta přidružené ke všem stávajícím trezorům klíčů v tomto předplatném na tenanta B.
* Odeberte všechny stávající položky zásad přístupu.
* Přidejte nové položky zásad přístupu, které jsou přidružené k tenantu B.

Například pokud máte trezor klíčů s názvem „muj_trezor“ v předplatném, které bylo přesunuto z tenanta A na tenanta B, následujícím postupem změníte ID tenanta pro tento trezor klíčů a odeberete staré zásady přístupu.

<pre>
Select-AzureRmSubscription -SubscriptionId YourSubscriptionID
$vaultResourceId = (Get-AzureRmKeyVault -VaultName myvault).ResourceId
$vault = Get-AzureRmResource –ResourceId $vaultResourceId -ExpandProperties
$vault.Properties.TenantId = (Get-AzureRmContext).Tenant.TenantId
$vault.Properties.AccessPolicies = @()
Set-AzureRmResource -ResourceId $vaultResourceId -Properties $vault.Properties
</pre>

Protože byl tento trezor před přesunem v tenantu A, původní hodnota proměnné **$vault.Properties.TenantId** je tenant A, zatímco **(Get-AzureRmContext).Tenant.TenantId** je tenant B.

Teď když je trezor přidružený ke správnému ID tenanta a staré položky zásad přístupu jsou odebrány, nastavte nové položky zásad přístupu pomocí [Set-AzureRmKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy).

## <a name="next-steps"></a>Další postup

Pokud máte dotazy ke službě Azure Key Vault, navštivte [fóra služby Azure Key Vault](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
