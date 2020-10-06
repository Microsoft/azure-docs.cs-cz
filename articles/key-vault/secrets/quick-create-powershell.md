---
title: 'Rychlý Start: nastavení & načtení tajného klíče z Key Vault pomocí prostředí PowerShell'
description: V tomto rychlém startu se dozvíte, jak vytvářet, načítat a odstraňovat tajné klíče z Azure Key Vault pomocí Azure PowerShell.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 6ef7b17efc1f18009edffbacb2578f94fcf40b1c
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743025"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí PowerShellu

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o Key Vault si můžete projít v [přehledu](../general/overview.md). V tomto rychlém startu vytvoříte trezor klíčů pomocí PowerShellu. Do nově vytvořeného trezoru pak uložíte tajný klíč.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell verze modulu 1.0.0 nebo novější. `$PSVersionTable.PSVersion`Pro nalezení verze zadejte. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů

Dále vytvoříte službu Key Vault. K provedení tohoto kroku potřebujete několik informací:

I když jako název naší Key Vault v rámci tohoto rychlého startu používáme contoso KeyVault2, musíte použít jedinečný název.

- **Název trezoru:** Contoso-Vault2
- **Název skupiny prostředků** ContosoResourceGroup.
- **Umístění** Východní USA.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořeného trezoru klíčů. Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru:** V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další rutiny Key Vault.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://Contoso-Vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Po vytvoření trezoru bude jakékoli operace s tímto novým trezorem moct provádět pouze váš účet Azure.

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Poskytněte uživatelskému účtu oprávnění ke správě tajných kódů v Key Vault

Pomocí rutiny Azure PowerShell set-AzKeyVaultAccessPolicy aktualizujte zásady přístupu Key Vault a udělte vám oprávnění k tajným datům pro svůj uživatelský účet.
```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName 'Contoso-Vault2' -UserPrincipalName 'user@domain.com' -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik kroků. V tomto případě přidáte heslo, které může používat aplikace. Heslo se nazývá **ExamplePassword** a ukládá do něj hodnotu **hVFkk965BuUv** .

Nejprve převeďte hodnotu **hVFkk965BuUv** na zabezpečený řetězec zadáním:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Potom zadejte níže uvedené příkazy PowerShellu pro vytvoření tajného klíče v Key Vault s názvem **ExamplePassword** s hodnotou **hVFkk965BuUv** :

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contoso-Vault2" -name "ExamplePassword").SecretValueText
```

Právě jste vytvořili službu Key Vault, uložili jste tajný klíč a načetli jste ho.

## <a name="clean-up-resources"></a>Vyčištění prostředků

 Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, Key Vault a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj tajný klíč. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si referenční informace pro [rutiny Azure PowerShell Key Vault](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
