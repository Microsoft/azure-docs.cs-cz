---
title: Azure Key Vault přesunu trezoru do jiného předplatného | Microsoft Docs
description: Pokyny k přesunutí trezoru klíčů do jiného předplatného.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
ms.openlocfilehash: c23f961b8aeaae3e338f9c513a9f2b9d07b64abb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106056398"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Přesun služby Azure Key Vault do jiného předplatného

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Přehled

> [!IMPORTANT]
> **Přesunutí trezoru klíčů do jiného předplatného způsobí zásadní změnu prostředí.**
> Ujistěte se, že rozumíte dopadu této změny, a před tím, než se rozhodnete přesunout Trezor klíčů k novému předplatnému, pečlivě postupujte podle pokynů v tomto článku.
> Pokud používáte identity spravované služby (MSI), přečtěte si pokyny po přesunutí na konci dokumentu. 

[Azure Key Vault](overview.md) je automaticky svázán s výchozím ID klienta [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) pro předplatné, ve kterém je vytvořen. Pomocí této [příručky](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)můžete najít ID tenanta přidruženého k vašemu předplatnému. K tomuto ID tenanta se taky váže všechny položky zásad přístupu a přiřazení rolí.  Pokud přesunete předplatné Azure z tenanta A na tenanta B, stávající trezory klíčů budou pro objekty služby (uživatelé a aplikace) v tenantovi B nepřístupné. Chcete-li tento problém vyřešit, je třeba provést následující kroky:

* Změňte ID tenanta přidružené ke všem stávajícím trezorům klíčů v předplatném na tenanta B.
* Odeberte všechny stávající položky zásad přístupu.
* Přidejte nové položky zásad přístupu přidružené k tenantovi B.

Další informace o Azure Key Vault a Azure Active Directory najdete v tématu
- [Informace o službě Azure Key Vault](overview.md)
- [Co je Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Jak zjistit ID tenanta](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Omezení

> [!IMPORTANT]
> **Trezory klíčů použité pro šifrování disku nejde přesunout** . Pokud používáte Trezor klíčů s šifrováním disku pro virtuální počítač, Trezor klíčů nejde přesunout do jiné skupiny prostředků nebo předplatného, když je povolené šifrování disku. Před přesunutím trezoru klíčů do nové skupiny prostředků nebo předplatného je nutné zakázat šifrování disku. 

Některé objekty služby (uživatelé a aplikace) jsou vázány na konkrétního tenanta. Pokud přesunete Trezor klíčů do předplatného jiného tenanta, může se stát, že nebudete moct obnovit přístup k určitému instančnímu objektu. Ujistěte se, že v tenantovi existují všechny základní instanční objekty, u kterých přesouváte Trezor klíčů.

## <a name="prerequisites"></a>Požadavky

* Přístup na úrovni [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) nebo vyšší pro aktuální předplatné, ve kterém existuje Trezor klíčů. Roli můžete přiřadit pomocí [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)nebo [PowerShellu](../../role-based-access-control/role-assignments-powershell.md).
* Přístup na úrovni [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) nebo vyšší pro předplatné, ve kterém chcete Trezor klíčů přesunout. Roli můžete přiřadit pomocí [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)nebo [PowerShellu](../../role-based-access-control/role-assignments-powershell.md).
* Skupina prostředků v novém předplatném. Můžete ho vytvořit pomocí [Azure Portal](../../azure-resource-manager/management/manage-resource-groups-portal.md), [PowerShellu](../../azure-resource-manager/management/manage-resource-groups-powershell.md)nebo rozhraní příkazového [řádku Azure CLI](../../azure-resource-manager/management/manage-resource-groups-cli.md).

Existující role můžete kontrolovat pomocí [Azure Portal](../../role-based-access-control/role-assignments-list-portal.md), [PowerShellu](../../role-based-access-control/role-assignments-list-powershell.md), rozhraní příkazového [řádku Azure](../../role-based-access-control/role-assignments-list-cli.md)nebo [REST API](../../role-based-access-control/role-assignments-list-rest.md).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Přesun trezoru klíčů do nového předplatného

1. Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.
2. Přejděte do [trezoru klíčů](overview.md) .
3. Klikněte na kartu Přehled.
4. Vyberte tlačítko přesunout.
5. Z možností rozevíracího seznamu vyberte přesunout do jiného předplatného.
6. Vyberte skupinu prostředků, do které chcete přesunout Trezor klíčů.
7. Potvrzení upozornění týkající se přesunu prostředků
8. Vyberete OK.

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Další kroky, pokud je předplatné v novém tenantovi

Pokud jste Trezor klíčů přesunuli do předplatného v novém tenantovi, budete muset ručně aktualizovat ID tenanta a odebrat staré zásady přístupu a přiřazení rolí. Tady jsou kurzy pro tyto kroky v PowerShellu a Azure CLI. Pokud používáte PowerShell, možná budete muset spustit příkaz Clear-AzContext popsaný níže, který vám umožní zobrazit prostředky mimo aktuálně vybraný obor. 

### <a name="update-tenant-id-in-a-key-vault"></a>Aktualizace ID tenanta v trezoru klíčů

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Aktualizace zásad přístupu a přiřazení rolí

> [!NOTE]
> Pokud Key Vault používá model oprávnění [Azure RBAC](../../role-based-access-control/overview.md) . Musíte taky odebrat přiřazení rolí trezoru klíčů. Přiřazení rolí můžete odebrat pomocí webu [Azure Portal](../../role-based-access-control/role-assignments-portal.md), [Azure CLI](../../role-based-access-control/role-assignments-cli.md)nebo [PowerShellu](../../role-based-access-control/role-assignments-powershell.md). 

Teď, když je váš trezor přidružený ke správnému ID tenanta a staré položky zásad přístupu nebo přiřazení rolí se odeberou, nastavte nové položky zásad přístupu nebo přiřazení rolí.

Informace o přiřazování zásad najdete v tématech:
- [Přiřazení zásady přístupu pomocí portálu](assign-access-policy-portal.md)
- [Přiřazení zásady přístupu pomocí Azure CLI](assign-access-policy-cli.md)
- [Přiřazení zásady přístupu pomocí PowerShellu](assign-access-policy-powershell.md)

Pokud chcete přidat přiřazení rolí, přečtěte si:
- [Přiřazení rolí Azure pomocí Azure Portal](../../role-based-access-control/role-assignments-portal.md)
- [Přiřazení rolí Azure pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md)
- [Přiřazení rolí Azure pomocí PowerShellu](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Aktualizace spravovaných identit

Pokud přenášíte celé předplatné a pomocí spravované identity prostředků Azure, budete ho muset aktualizovat taky na nového tenanta Azure Active Directory. Další informace o spravovaných identitách najdete v [přehledu spravované identity](../../active-directory/managed-identities-azure-resources/overview.md).

Pokud používáte spravovanou identitu, musíte aktualizovat taky identitu, protože stará identita už nebude ve správném tenantovi Azure Active Directory. Tento problém můžete vyřešit v následujících dokumentech. 

* [Aktualizace MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Přenést předplatné do nového adresáře](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Další kroky

- Další informace o [klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md)
- Koncepční informace, včetně toho, jak interpretovat protokoly Key Vault, najdete v tématu [Key Vault Logging](logging.md) .
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
- [Zabezpečení trezoru klíčů](secure-your-key-vault.md)
- [Konfigurace Azure Key Vault bran firewall a virtuálních sítí](network-security.md)