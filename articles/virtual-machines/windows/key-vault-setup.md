---
title: Nastavit klíč trezoru pro virtuální počítače Windows ve službě Správce prostředků Azure | Microsoft Docs
description: Jak nastavit Key Vault pro použití s se virtuální počítač Azure Resource Manager.
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
ms.openlocfilehash: f8f094bfb0f304123cbdf719bec22185431aca5a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918383"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Nastavení pro virtuální počítače ve službě Správce prostředků Azure Key Vault

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

V zásobníku Azure Resource Manager tajných klíčů nebo certifikáty jsou modelovat jako prostředky, které jsou poskytovány zprostředkovatele prostředku Key Vault. Další informace o Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md)

> [!NOTE]
> 1. V pořadí pro Key Vault pro použití s Azure Resource Manager virtuální počítače **EnabledForDeployment** vlastnost v Key Vault musí být nastavena na hodnotu true. To provedete v různých klientech.
> 2. Key Vault je potřeba vytvořit ve stejném předplatném a umístění jako virtuální počítač.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Nastavit Key Vault pomocí prostředí PowerShell
Vytvoření trezoru klíčů pomocí prostředí PowerShell naleznete v tématu [Začínáme s Azure Key Vault](../../key-vault/key-vault-get-started.md#vault).

Pro nové trezorů klíčů můžete použít tuto rutinu prostředí PowerShell:

    New-AzureRmKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

Pro existující trezorů klíčů můžete použít tuto rutinu prostředí PowerShell:

    Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="us-cli-to-set-up-key-vault"></a>Nám rozhraní příkazového řádku pro nastavení Key Vault
Vytvoření trezoru klíčů pomocí rozhraní příkazového řádku (CLI), naleznete v části [Key Vault spravovat pomocí rozhraní příkazového řádku](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Pro rozhraní příkazového řádku musíte vytvořit trezor klíčů, před přiřazením zásady nasazení. Můžete to provést pomocí následujícího příkazu:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Použití šablon nastavit Key Vault
Když použijete šablonu, je nutné nastavit `enabledForDeployment` vlastnost `true` prostředku Key Vault.

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

Jiné možnosti, které můžete konfigurovat při vytvoření trezoru klíčů pomocí šablon najdete v tématu [vytvoření trezoru klíčů](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
