---
title: Nastavit Key Vault pro Windows virtuálních počítačů v Azure Resource Manageru | Dokumentace Microsoftu
description: Jak nastavit službu Key Vault pro použití s virtuálním počítači s Azure Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: kasing
ms.openlocfilehash: f5aa04da40c6681385f0debc3999b94a1f507f05
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978982"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Nastavení služby Key Vault pro virtuální počítače v Azure Resource Manageru

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

V zásobníku Azure Resource Manageru jsou tajné kódy a certifikáty nemodelují jako prostředky, které jsou k dispozici přes poskytovatele prostředků služby Key Vault. Další informace o službě Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. V pořadí pro službu Key Vault pro použití s virtuálními počítači Azure Resource Manageru **EnabledForDeployment** vlastnost o službě Key Vault musí být nastavena na hodnotu true. Můžete to provést v různých klientech.
> 2. Key Vault je potřeba vytvořit ve stejném předplatném a umístění jako virtuální počítač.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Nastavení služby Key Vault pomocí Powershellu
Vytvoření služby key vault pomocí Powershellu najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Pro nové trezory klíčů můžete použít tuto rutinu Powershellu:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pro stávající trezory klíčů můžete použít tuto rutinu Powershellu:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Nastavení služby Key Vault pomocí rozhraní příkazového řádku
Vytvoření služby key vault pomocí rozhraní příkazového řádku (CLI), najdete v tématu [Správa služby Key Vault pomocí rozhraní příkazového řádku](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Pro rozhraní příkazového řádku budete muset vytvořit trezor klíčů, než přiřadíte zásady nasazení. Můžete to provést pomocí následujícího příkazu:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Použijte šablony k nastavení služby Key Vault
Při použití šablony, je nutné nastavit `enabledForDeployment` vlastnost `true` prostředku služby Key Vault.

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

Další možnosti, které můžete konfigurovat při vytvoření trezoru klíčů pomocí šablon najdete v tématu [vytvořit trezor klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
