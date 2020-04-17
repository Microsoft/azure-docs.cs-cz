---
title: Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption
description: Tento článek obsahuje postup pro vytvoření a konfiguraci trezoru klíčů pro použití s azure disk encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 30fa6b910c0241621c2b2cdae9bb9a164f27cedb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454539"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných kódů disku.  Další informace o trezorech klíčů najdete [v tématu Začínáme s trezorem klíčů Azure](../key-vault/key-vault-get-started.md) a [Zabezpečte trezor klíčů](../key-vault/general/secure-your-key-vault.md).

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption zahrnuje tři kroky:

1. V případě potřeby vytvoříte skupinu prostředků.
2. Vytvoření trezoru klíčů. 
3. Nastavení pokročilých zásad přístupu trezoru klíčů.

Tyto kroky jsou znázorněny v následujících rychlých startech:

Pokud chcete, můžete také vygenerovat nebo importovat šifrovací klíč (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instalace nástrojů a připojení k Azure

Kroky v tomto článku můžete dokončit pomocí [rozhraní příkazového příkazu Azure ,](/cli/azure/) [modulu Azure PowerShell Az](/powershell/azure/overview)nebo [portálu Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Před použitím Azure CLI nebo Azure PowerShell, musíte se nejprve připojit k předplatnému Azure. Uděláte to tak, že [se přihlásíte pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [přihlásíte se pomocí Azure Powershellu](/powershell/azure/authenticate-azureps?view=azps-2.5.0)nebo po zobrazení výzvy zadáte svá pověření na portál Azure.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Další kroky

- [Azure Disk Encryption – přehled](disk-encryption-overview.md)
- [Šifrování škálovacích sad virtuálních strojů pomocí příkazového příkazového příkazu Azure](disk-encryption-cli.md)
- [Šifrování škálovacích sad virtuálních strojů pomocí Azure PowerShellu](disk-encryption-powershell.md)
