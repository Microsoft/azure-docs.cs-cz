---
title: Nastavení Azure Key Vault pro virtuální počítače se systémem Linux
description: Jak nastavit Key Vault pro použití s virtuálním počítačem s Azure Resource Manager pomocí Azure CLI.
author: singhkays
manager: gwallace
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: kasing
ms.openlocfilehash: c9659c38e349bd62f22ed0d0839d97651b1b3b66
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2020
ms.locfileid: "78944824"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak nastavit Key Vault pro virtuální počítače pomocí Azure CLI

V Azure Resource Managerovém zásobníku se tajné klíče a certifikáty modelují jako prostředky, které jsou k dispozici v Key Vault. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-overview.md) Aby bylo možné Key Vault použít s Azure Resource Manager virtuálními počítači, musí být vlastnost *EnabledForDeployment* v Key Vault nastavena na hodnotu true. V tomto článku se dozvíte, jak nastavit Key Vault pro použití s virtuálními počítači Azure pomocí Azure CLI. 

K provedení těchto kroků potřebujete mít nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásili se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů
Vytvořte Trezor klíčů a přiřaďte zásady nasazení pomocí [AZ klíčů Create](/cli/azure/keyvault). Následující příklad vytvoří Trezor klíčů s názvem `myKeyVault` ve skupině prostředků `myResourceGroup`:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizace Key Vault pro použití s virtuálními počítači
Nastavte zásady nasazení v existujícím trezoru klíčů pomocí [AZ Key trezor Update](/cli/azure/keyvault). Následující aktualizace trezoru klíčů s názvem `myKeyVault` ve skupině prostředků `myResourceGroup`:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Použití šablon k nastavení Key Vault
Když použijete šablonu, musíte nastavit vlastnost `enabledForDeployment` na `true` pro prostředek Key Vault následujícím způsobem:

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
