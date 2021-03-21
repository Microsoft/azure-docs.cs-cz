---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: a87fbbb276fd8813492cc293bc08b958ee3d6b7b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99070249"
---
Pomocí příkazu Azure CLI [AZ klíčů trezor Create](/cli/azure/keyvault#az_keyvault_create) vytvořte Key Vault ve skupině prostředků z předchozího kroku. Budete muset zadat několik informací:

- Název trezoru klíčů: řetězec na 3 až 24 znaků, který může obsahovat jenom číslice (0-9), písmena (A-z, A-Z) a spojovníky (-).

  > [!Important]
  > Každý Trezor klíčů musí mít jedinečný název. V následujících příkladech nahraďte <název trezoru klíčů jedinečných> s názvem vašeho trezoru klíčů.

- Název skupiny prostředků: **myResourceGroup**.
- Umístění: **EastUS**.

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

Výstup tohoto příkazu zobrazuje vlastnosti nově vytvořeného trezoru klíčů. Poznamenejte si hodnoty dvou vlastností uvedených níže:

- **Název trezoru**: název, který jste zadali do parametru--name výše.
- **Identifikátor URI trezoru**: v tomto příkladu se jedná o https://a &lt; jedinečné úložiště klíčů – název &gt; . Vault.Azure.NET/. Aplikace, které používají váš trezor prostřednictvím REST API musí používat tento identifikátor URI.

V tuto chvíli je váš účet Azure jediným účtem s oprávněním provádět jakékoli operace s tímto novým trezorem.
