---
title: Nastavení Azure Key Vault pro virtuální počítače se systémem Linux
description: Jak nastavit Key Vault pro použití s virtuálním počítačem s Azure Resource Manager pomocí Azure CLI.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/24/2017
ms.author: mimckitt
ms.custom: devx-track-azurecli
ms.openlocfilehash: fac9e9cb4bc6a4589e27efcf9ec18a378d7407d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501538"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak nastavit Key Vault pro virtuální počítače pomocí Azure CLI

V Azure Resource Managerovém zásobníku se tajné klíče a certifikáty modelují jako prostředky, které jsou k dispozici v Key Vault. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md) Aby bylo možné Key Vault použít s Azure Resource Manager virtuálními počítači, musí být vlastnost *EnabledForDeployment* v Key Vault nastavena na hodnotu true. V tomto článku se dozvíte, jak nastavit Key Vault pro použití s virtuálními počítači Azure pomocí Azure CLI. 

K provedení těchto kroků potřebujete mít nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásili se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů
Vytvořte Trezor klíčů a přiřaďte zásady nasazení pomocí [AZ klíčů Create](/cli/azure/keyvault). Následující příklad vytvoří Trezor klíčů s názvem `myKeyVault` ve `myResourceGroup` skupině prostředků:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizace Key Vault pro použití s virtuálními počítači
Nastavte zásady nasazení v existujícím trezoru klíčů pomocí [AZ Key trezor Update](/cli/azure/keyvault). Následující aktualizace trezoru klíčů s názvem `myKeyVault` ve `myResourceGroup` skupině prostředků:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Použití šablon k nastavení Key Vault
Když použijete šablonu, musíte nastavit `enabledForDeployment` vlastnost na `true` pro prostředek Key Vault následujícím způsobem:

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
Další možnosti, které můžete nakonfigurovat při vytváření Key Vault pomocí šablon, najdete v tématu [Vytvoření trezoru klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
