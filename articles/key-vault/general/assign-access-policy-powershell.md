---
title: Přiřazení zásady přístupu Azure Key Vault
description: Jak pomocí Azure Portal, rozhraní příkazového řádku Azure nebo Azure PowerShell přiřadit zásady Key Vault přístupu k objektu zabezpečení nebo identitě aplikace.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 1c7c31f38d6a59f4ded17e1e1fd7e985ce59922a
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751413"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>Přiřazení zásady přístupu Key Vault pomocí Azure PowerShell

Zásada přístupu Key Vault určuje, jestli daný objekt zabezpečení, konkrétně uživatel, aplikace nebo skupina uživatelů, může provádět různé operace s Key Vault [tajné klíče](../secrets/index.yml), [klíče](../keys/index.yml)a [certifikáty](../certificates/index.yml). Zásady přístupu můžete přiřadit pomocí [Azure Portal](assign-access-policy-portal.md), rozhraní příkazového [řádku Azure](assign-access-policy-cli.md)nebo Azure PowerShell (Tento článek).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Další informace o vytváření skupin v Azure Active Directory pomocí Azure PowerShell najdete v tématech [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) a [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Konfigurace PowerShellu a přihlášení

1. Pokud chcete spouštět příkazy místně, nainstalujte [Azure PowerShell](/powershell/azure/) , pokud jste to ještě neudělali.

    Pokud chcete spouštět příkazy přímo v cloudu, použijte [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Pouze místní prostředí PowerShell:

    1. Nainstalujte [modul Azure Active Directory PowerShell](https://www.powershellgallery.com/packages/AzureAD).

    1. Přihlaste se k Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Získat ID objektu

Určete ID objektu aplikace, skupiny nebo uživatele, ke kterému chcete přiřadit zásady přístupu:

- Aplikace a další instanční objekty: pomocí rutiny [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) s `-SearchString` parametrem můžete filtrovat výsledky podle názvu požadovaného instančního objektu:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Skupiny: pomocí rutiny [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) s `-SearchString` parametrem můžete filtrovat výsledky podle názvu požadované skupiny:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    Ve výstupu je ID objektu uvedeno jako `Id` .

- Uživatelé: použijte rutinu [Get-AzADUser](/powershell/module/az.resources/get-azaduser) , která předá e-mailovou adresu uživatele k `-UserPrincipalName` parametru.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    Ve výstupu je ID objektu uvedeno jako `Id` .

## <a name="assign-the-access-policy"></a>Přiřazení zásad přístupu

K přiřazení zásad přístupu použijte rutinu [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) :

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Potřebujete pouze zahrnout `-PermissionsToSecrets` , `-PermissionsToKeys` a `-PermissionsToCertificates` při přiřazování oprávnění těmto konkrétním typům. Povolené hodnoty pro, a `<secret-permissions>` `<key-permissions>` `<certificate-permissions>` jsou uvedeny v dokumentaci [set-AzKeyVaultAccessPolicy-Parameters](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) .

## <a name="next-steps"></a>Další kroky

- [Azure Key Vault zabezpečení: Správa identit a přístupu](security-overview.md#identity-management)
- [Zabezpečte svůj Trezor klíčů](security-overview.md).
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)