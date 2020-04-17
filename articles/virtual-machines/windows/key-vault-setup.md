---
title: Nastavení trezoru klíčů pro virtuální počítače s Windows ve Správci prostředků Azure
description: Jak nastavit trezor klíčů pro použití s virtuálním počítačem Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 098d4df3938f591cb72b3bfd59b7a5549469785c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451733"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Nastavení trezoru klíčů pro virtuální počítače ve Správci prostředků Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

V zásobníku Azure Resource Manager jsou tajné klíče nebo certifikáty modelovány jako prostředky, které poskytuje poskytovatel prostředků trezoru klíčů. Další informace o trezoru klíčů najdete v tématu [Co je Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Aby se trezor klíčů používal s virtuálními počítači Azure Resource Manager, musí být vlastnost **EnabledForDeployment** v trezoru klíčů nastavena na hodnotu true. Můžete to udělat v různých klientech.
> 2. Trezor klíčů je třeba vytvořit ve stejném předplatném a umístění jako virtuální počítač.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Nastavení trezoru klíčů pomocí PowerShellu
Pokud chcete vytvořit trezor klíčů pomocí PowerShellu, přečtěte si, že [najdete informace o nastavení a načtení tajného klíče z Azure Key Vault pomocí PowerShellu](../../key-vault/secrets/quick-create-powershell.md).

Pro nové trezory klíčů můžete použít tuto rutinu prostředí PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pro existující trezory klíčů můžete použít tuto rutinu prostředí PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Nastavení trezoru klíčů pomocí zapisování zapisování pomocí zapisování zapisování
Chcete-li vytvořit trezor klíčů pomocí rozhraní příkazového řádku (CLI), přečtěte si informace [o správě trezoru klíčů pomocí rozhraní příkazového řádku](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

Pro příkazové příkazové příkazové příkazy je třeba vytvořit trezor klíčů před přiřazením zásad nasazení. Můžete to provést pomocí následujícího příkazu:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Chcete-li povolit trezor klíčů pro použití s nasazením šablony, spusťte následující příkaz:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Nastavení trezoru klíčů pomocí šablon
Při použití šablony je třeba nastavit `enabledForDeployment` vlastnost `true` pro prostředek trezoru klíčů.

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

Další možnosti, které můžete nakonfigurovat při vytváření trezoru klíčů pomocí šablon, naleznete [v tématu Vytvoření trezoru klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
