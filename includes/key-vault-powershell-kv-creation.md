---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 13d4bb94d3efb574895df6cfdf240cdeec77e539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070179"
---
Pomocí rutiny Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault) vytvořte ve skupině prostředků v předchozím kroku Key Vault. Budete muset zadat několik informací:

- Název trezoru klíčů: řetězec na 3 až 24 znaků, který může obsahovat jenom číslice (0-9), písmena (A-z, A-Z) a spojovníky (-).

  > [!Important]
  > Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.

- Název skupiny prostředků: **myResourceGroup**.
- Umístění: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "ContosoResourceGroup" -Location "East US"
```

Výstup této rutiny zobrazuje vlastnosti nově vytvořeného trezoru klíčů. Poznamenejte si hodnoty dvou vlastností uvedených níže:

- **Název trezoru**: název, který jste zadali do parametru--name výše.
- **Identifikátor URI trezoru**: v tomto příkladu se jedná o https://a &lt; jedinečné úložiště klíčů – název &gt; . Vault.Azure.NET/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.
