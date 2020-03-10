---
title: Nastavení Key Vault pro virtuální počítače s Windows v Azure Resource Manager
description: Jak nastavit Key Vault pro použití s virtuálním počítačem s Azure Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: a64163da1dee2bceb567436dc18ba0fa5274cfcb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387263"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Nastavení Key Vault pro virtuální počítače v Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

V Azure Resource Managerovém zásobníku se tajné klíče a certifikáty modelují jako prostředky poskytované poskytovatelem prostředků Key Vault. Další informace o Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-overview.md)

> [!NOTE]
> 1. Aby bylo možné Key Vault použít s Azure Resource Manager virtuálními počítači, musí být vlastnost **EnabledForDeployment** v Key Vault nastavena na hodnotu true. To můžete provést v různých klientech.
> 2. Key Vault se musí vytvořit ve stejném předplatném a umístění jako virtuální počítač.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Použití PowerShellu k nastavení Key Vault
Pokud chcete vytvořit Trezor klíčů pomocí PowerShellu, přečtěte si téma [nastavení a načtení tajného klíče z Azure Key Vault pomocí PowerShellu](../../key-vault/quick-create-powershell.md).

Pro nové trezory klíčů můžete použít tuto rutinu PowerShellu:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pro existující trezory klíčů můžete použít tuto rutinu PowerShellu:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>K nastavení Key Vault použijte CLI.
Informace o vytvoření trezoru klíčů pomocí rozhraní příkazového řádku (CLI) najdete v tématu [správa Key Vault pomocí](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault)rozhraní příkazového řádku (CLI).

Pro rozhraní příkazového řádku je nutné před přiřazením zásad nasazení vytvořit Trezor klíčů. Můžete to provést pomocí následujícího příkazu:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Pak pokud chcete povolit Key Vault pro použití s nasazením šablony, spusťte následující příkaz:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Použití šablon k nastavení Key Vault
Když použijete šablonu, musíte nastavit vlastnost `enabledForDeployment` na `true` pro prostředek Key Vault.

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

Další možnosti, které můžete nakonfigurovat při vytváření trezoru klíčů pomocí šablon, najdete v tématu [Vytvoření trezoru klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
