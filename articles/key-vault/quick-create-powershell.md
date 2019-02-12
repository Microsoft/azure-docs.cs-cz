---
title: Rychlý start Azure – Nastavení a načtení tajného klíče ze služby Key Vault pomocí PowerShellu | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 8f3045394afbfbc9ae4839069c85ed405477a423
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003869"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Rychlý start: Nastavení a načtení tajného klíče ze služby Azure Key Vault pomocí Powershellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů. Můžete bezpečně ukládat klíče, hesla, certifikáty a další tajné klíče. Další informace o službě Key Vault najdete v tématu [Přehled](key-vault-overview.md). V tomto rychlém startu vytvoříte trezor klíčů pomocí PowerShellu. Do nově vytvořeného trezoru pak uložíte tajný klíč.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, tento kurz vyžaduje modul Azure PowerShell verze 1.0.0 nebo novějším. Typ `$PSVersionTable.PSVersion` k vyhledání verze. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzAccount` pro vytvoření připojení k Azure.

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

* **Název trezoru**: V tomto příkladu je to **Contoso-Vault2**. Tento název budete používat pro další rutiny Key Vault.
* **Identifikátor URI trezoru**: V tomto příkladu, který je https://contosokeyvault.vault.azure.net/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

Po vytvoření trezoru bude jakékoli operace s tímto novým trezorem moct provádět pouze váš účet Azure.

![Výstup po dokončení příkazu pro vytvoření trezoru klíčů](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Přidání tajného klíče do služby Key Vault

Pokud chcete do trezoru přidat tajný klíč, stačí provést několik kroků. V tomto případě přidáte heslo, které může používat aplikace. Heslo se nazývá **ExamplePassword** a uloží hodnotu **hVFkk965BuUv** v ní.

Nejprve převeďte hodnotu **hVFkk965BuUv** na zabezpečený řetězec zadáním:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Zadáním následujících příkazů Powershellu k vytvoření tajného klíče ve službě Key Vault volá **ExamplePassword** s hodnotou **hVFkk965BuUv** :

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Pokud chcete zobrazit hodnotu v tajném kódu jako prostý text:

```azurepowershell-interactive
(Get-AzKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

Právě jste vytvořili službu Key Vault, uložili jste tajný klíč a načetli jste ho.

## <a name="clean-up-resources"></a>Vyčištění prostředků

 Další rychlé starty a kurzy v této kolekci vycházejí z tohoto rychlého startu. Pokud chcete pokračovat v práci s dalšími rychlými starty a kurzy, možná budete chtít tyto prostředky zachovat.

Pokud už je nepotřebujete, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, služby Key Vault a všech souvisejících prostředků.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili službu Key Vault a uložili do ní softwarový klíč. Další informace o službě Key Vault a jejím použití s vašimi aplikacemi najdete v kurzu pro webové aplikace pracující se službou Key Vault.

Informace o čtení tajného klíče ze služby Key Vault z webové aplikace s využitím spravované identity pro prostředky Azure, pokračujte v následujícím kurzu

> [!div class="nextstepaction"]
> [Konfigurace webové aplikace Azure pro čtení tajného klíče ze služby Key vault](quick-create-net.md).
