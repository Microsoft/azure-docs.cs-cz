---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2917854da679816796a7f7748c3877c062995168
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87512790"
---
V tomto rychlém startu se používá předem vytvořený Trezor klíčů Azure. Trezor klíčů můžete vytvořit podle kroků v [rychlém startu Azure CLI](/azure/key-vault/general/quick-create-cli), [Azure PowerShell rychlý Start](/azure/key-vault/general/quick-create-powershell)nebo v [rychlém startu Azure Portal](/azure/key-vault/general/quick-create-portal). 

Případně můžete jednoduše spustit příkazy Azure CLI nebo Azure PowerShell níže.

> [!Important]
> Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```