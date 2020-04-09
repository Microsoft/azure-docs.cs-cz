---
title: Nastavení Azure Key Vault pro virtuální počítače s Linuxem
description: Jak nastavit trezor klíčů pro použití s virtuálním počítačem Azure Resource Manager s Azure CLI.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 59b11d22f054a98fe176e4393843606bd01cc872
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879475"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak nastavit trezor klíčů pro virtuální počítače pomocí azure CLI

V zásobníku Azure Resource Manager jsou tajné klíče nebo certifikáty modelovány jako prostředky poskytované trezorem klíčů. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../../key-vault/key-vault-overview.md) Aby se trezor klíčů používal s virtuálními počítači Azure Resource Manager, musí být vlastnost *EnabledForDeployment* v trezoru klíčů nastavena na hodnotu true. Tento článek ukazuje, jak nastavit trezor klíčů pro použití s virtuálními počítači Azure (VM) pomocí azure CLI. 

K provedení těchto kroků potřebujete nejnovější [azure cli](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů
Vytvořte trezor klíčů a přiřaďte zásady nasazení pomocí [vytvoření trezoru az keyvault](/cli/azure/keyvault). Následující příklad vytvoří trezor `myKeyVault` klíčů `myResourceGroup` pojmenovaný ve skupině prostředků:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizace trezoru klíčů pro použití s virtuálními aplikacemi
Nastavte zásady nasazení na existující trezor klíčů s [aktualizací az keyvault](/cli/azure/keyvault). Následující aktualizace trezoru `myKeyVault` klíčů `myResourceGroup` pojmenované ve skupině prostředků:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Nastavení trezoru klíčů pomocí šablon
Při použití šablony je třeba nastavit `enabledForDeployment` vlastnost `true` pro prostředek trezoru klíčů takto:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Další kroky
Další možnosti, které můžete nakonfigurovat při vytváření trezoru klíčů pomocí šablon, naleznete [v tématu Vytvoření trezoru klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
