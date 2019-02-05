---
title: Nastavení služby Azure Key Vault pro virtuální počítače s Linuxem | Dokumentace Microsoftu
description: Jak nastavit službu Key Vault pro použití s virtuálním počítači s Azure Resource Manageru pomocí rozhraní příkazového řádku Azure.
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
ms.openlocfilehash: c38d901be627d9ad2f18ebe708c7a1fcaa63cc15
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55726986"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak nastavit službu Key Vault pro virtuální počítače pomocí Azure CLI

V zásobníku Azure Resource Manageru jsou tajné kódy a certifikáty nemodelují jako prostředky, které jsou k dispozici ve službě Key Vault. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md) V pořadí pro službu Key Vault pro použití s virtuálními počítači Azure Resource Manageru *EnabledForDeployment* vlastnost o službě Key Vault musí být nastavena na hodnotu true. V tomto článku se dozvíte, jak nastavit službu Key Vault pro použití s Azure virtual machines (VM) pomocí Azure CLI. 

Pokud chcete tento postup, musíte na nejnovější verzi [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů
Vytvoření trezoru klíčů a přiřaďte zásady nasazení s [az keyvault vytvořit](/cli/azure/keyvault). Následující příklad vytvoří trezor klíčů s názvem `myKeyVault` v `myResourceGroup` skupina prostředků:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizace služby Key Vault pro použití s virtuálními počítači
Sada zásad nasazení na existující klíč trezoru pomocí [az keyvault update](/cli/azure/keyvault). Následující aktualizace trezoru klíčů s názvem `myKeyVault` v `myResourceGroup` skupina prostředků:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Použijte šablony k nastavení služby Key Vault
Při použití šablony, je nutné nastavit `enabledForDeployment` vlastnost `true` klíče trezoru prostředků následujícím způsobem:

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
Další možnosti, které můžete nakonfigurovat při vytváření služby Key Vault s použitím šablony, najdete v části [vytvořit trezor klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
