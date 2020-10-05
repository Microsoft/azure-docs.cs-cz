---
title: Vytvoření a načtení atributů klíče v Azure Key Vault – Azure PowerShell
description: Rychlý Start ukazující, jak nastavit a načíst klíč z Azure Key Vault pomocí Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 4ebd3cc605b396f72d063f3fc506df9020ec3a5f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87061000"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Rychlý Start: nastavení a načtení klíče z Azure Key Vault pomocí Azure PowerShell

V tomto rychlém startu vytvoříte Trezor klíčů v Azure Key Vault s Azure PowerShell. Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných kódů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](../general/overview.md). Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí příkazů nebo skriptů. Po dokončení této akce uložíte klíč.

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

## <a name="add-a-key-to-key-vault"></a>Přidat klíč pro Key Vault

Pokud chcete do trezoru přidat klíč, stačí provést několik dalších kroků. Tento klíč může používat aplikace. 

Zadáním následujících příkazů vytvořte s názvem **ExampleKey** :

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

Nyní můžete odkazovat na tento klíč, který jste přidali do Azure Key Vault pomocí jeho identifikátoru URI. Použijte **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** k získání aktuální verze. 

Postup zobrazení dříve uloženého klíče:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

Nyní jste vytvořili Key Vault, uložili klíč a načetli jste ho.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.
Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Prostředky můžete odstranit následujícím způsobem:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a uložili do něj certifikát. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](../general/overview.md)
- Přečtěte si referenční informace pro [rutiny Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Kontrola [Azure Key Vault osvědčených postupů](../general/best-practices.md)
