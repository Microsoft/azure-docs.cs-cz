---
title: 'Rychlý Start: nastavení & načtení tajného klíče z Key Vault pomocí prostředí PowerShell'
description: V tomto rychlém startu se dozvíte, jak vytvářet, načítat a odstraňovat tajné klíče z trezoru klíčů Azure pomocí PowerShellu.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 1e2c4db66046b09b354753b7aaee617095bba552
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77151441"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](key-vault-overview.md). V tomto rychlém startu vytvoříte trezor klíčů pomocí PowerShellu. Do nově vytvořeného trezoru pak uložíte tajný klíč.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, vyžaduje tento kurz Azure PowerShell verze modulu 1.0.0 nebo novější. Zadejte `$PSVersionTable.PSVersion` k nalezení verze. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

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

Přestože v tomto rychlém startu jako název služby Key Vault používáme „Contoso KeyVault2“, musíte použít jedinečný název.

- **Název trezoru:** Contoso-Vault2
- **Název skupiny prostředků:** ContosoResourceGroup
- **Umístění:** Východní USA

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořeného trezoru klíčů. Poznamenejte si hodnoty dvou vlastností uvedených níže:

* **Název trezoru:** V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další rutiny Key Vault.
* **Identifikátor URI trezoru:** V tomto příkladu je to https://Contoso-Vault2.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Po vytvoření trezoru bude jakékoli operace s tímto novým trezorem moct provádět pouze váš účet Azure.

![Výstup po dokončení příkazu pro vytvoření trezoru klíčů](./media/quick-create-powershell/output-after-creating-keyvault.png)

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

- Přečtěte si [přehled Azure Key Vault](key-vault-overview.md)
- Přečtěte si referenční informace pro [rutiny Azure PowerShell Key Vault](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)
- Další informace o [klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [Azure Key Vault osvědčených postupů](key-vault-best-practices.md)
