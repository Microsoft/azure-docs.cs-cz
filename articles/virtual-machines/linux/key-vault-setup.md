---
title: Nastavení Azure Key Vault pro virtuální počítače s Linuxem | Microsoft Docs
description: Jak nastavit Key Vault pro použití s se virtuální počítač Azure Resource Manager pomocí rozhraní příkazového řádku 2.0.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: 6bd039225062ac6010d432b930f601fe4678ed2c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli-20"></a>Jak nastavit Key Vault pro virtuální počítače s 2.0 rozhraní příkazového řádku Azure

V zásobníku Azure Resource Manager tajných klíčů nebo certifikáty jsou modelovat jako prostředky, které jsou poskytovány Key Vault. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md) V pořadí pro Key Vault, který se má použít s virtuálními počítači Azure Resource Manager *EnabledForDeployment* vlastnost v Key Vault musí být nastavena na hodnotu true. Tento článek ukazuje, jak nastavit Key Vault pro použití s Azure virtuální počítače (VM) pomocí Azure CLI 2.0. K provedení těchto kroků můžete také využít [Azure CLI 1.0](key-vault-setup-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

K provedení těchto kroků, budete potřebovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login).

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů
Vytvoření trezoru klíčů a přiřaďte nasazení zásad s [vytvořit az keyvault](/cli/azure/keyvault#az_keyvault_create). Následující příklad vytvoří trezoru klíčů s názvem `myKeyVault` v `myResourceGroup` skupiny prostředků:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizace Key Vault pro použití s virtuálními počítači
Sada zásady nasazení na existující klíč trezoru s [az keyvault aktualizace](/cli/azure/keyvault#az_keyvault_update). Následující aktualizace trezoru klíčů s názvem `myKeyVault` v `myResourceGroup` skupiny prostředků:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Použití šablon nastavit Key Vault
Pokud použijete šablonu, je nutné nastavit `enabledForDeployment` vlastnost `true` pro klíč trezoru prostředků následujícím způsobem:

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

## <a name="next-steps"></a>Další postup
Další možnosti, které můžete konfigurovat při vytvoření trezoru klíč pomocí šablon, najdete v části [vytvoření trezoru klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
