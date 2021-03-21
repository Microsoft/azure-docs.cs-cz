---
title: Nastavení Key Vault pomocí prostředí PowerShell
description: Jak nastavit Key Vault pro použití s virtuálním počítačem pomocí prostředí PowerShell.
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 124ab121186ff7c9047515f2ee1ff51cee1114b8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557484"
---
# <a name="set-up-key-vault-for-virtual-machines-using-azure-powershell"></a>Nastavení Key Vault pro virtuální počítače pomocí Azure PowerShell

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

V Azure Resource Managerovém zásobníku se tajné klíče a certifikáty modelují jako prostředky poskytované poskytovatelem prostředků Key Vault. Další informace o Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Aby bylo možné Key Vault použít s Azure Resource Manager virtuálními počítači, musí být vlastnost **EnabledForDeployment** v Key Vault nastavena na hodnotu true. To můžete provést v různých klientech.
> 2. Key Vault se musí vytvořit ve stejném předplatném a umístění jako virtuální počítač.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Použití PowerShellu k nastavení Key Vault
Pokud chcete vytvořit Trezor klíčů pomocí PowerShellu, přečtěte si téma [nastavení a načtení tajného klíče z Azure Key Vault pomocí PowerShellu](../../key-vault/secrets/quick-create-powershell.md).

Pro nové trezory klíčů můžete použít tuto rutinu PowerShellu:

```azurepowershell
New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment
```

Pro existující trezory klíčů můžete použít tuto rutinu PowerShellu:

```azurepowershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment
```

## <a name="use-cli-to-set-up-key-vault"></a>K nastavení Key Vault použijte CLI.
Informace o vytvoření trezoru klíčů pomocí rozhraní příkazového řádku (CLI) najdete v tématu [správa Key Vault pomocí](../../key-vault/general/manage-with-cli2.md#create-a-key-vault)rozhraní příkazového řádku (CLI).

Pro rozhraní příkazového řádku je nutné před přiřazením zásad nasazení vytvořit Trezor klíčů. Můžete to provést pomocí následujícího příkazu:

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
```

Pak pokud chcete povolit Key Vault pro použití s nasazením šablony, spusťte následující příkaz:

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
```

## <a name="use-templates-to-set-up-key-vault"></a>Použití šablon k nastavení Key Vault
Při použití šablony je nutné nastavit `enabledForDeployment` vlastnost na hodnotu `true` pro prostředek Key Vault.

```config
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

Další možnosti, které můžete nakonfigurovat při vytváření trezoru klíčů pomocí šablon, najdete v tématu [Vytvoření trezoru klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
