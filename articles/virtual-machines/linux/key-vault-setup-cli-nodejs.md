---
title: Nastavení pro virtuální počítače s Linuxem se 1.0 rozhraní příkazového řádku Azure Key Vault | Microsoft Docs
description: Jak nastavit Key Vault pro použití s virtuálním počítačem správce prostředků Azure pomocí Azure CLI 1.0.
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: bccdd5ab-5ccf-4760-9039-92c6eafb15bd
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/24/2017
ms.author: singhkay
ms.openlocfilehash: a9225429e878415334b0c8a66777902395606d63
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager-with-the-azure-cli-10"></a>Nastavení pro virtuální počítače ve Správci prostředků pomocí Azure CLI 1.0 Azure Key Vault
V zásobníku Azure Resource Manager tajných klíčů nebo certifikáty jsou modelovat jako prostředky, které jsou poskytovány zprostředkovatele prostředku Key Vault. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../../key-vault/key-vault-whatis.md) V pořadí pro Key Vault pro použití s Azure Resource Manager virtuální počítače *EnabledForDeployment* vlastnost v Key Vault musí být nastavena na hodnotu true. To provedete v různých klientech. Tento článek ukazuje, jak nastavit Key Vault pro použití s virtuálními počítači Azure.

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
Můžete dokončit úlohu pomocí jedné z následujících verzí rozhraní příkazového řádku

- [Azure CLI 1.0](#quick-commands) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](../windows/key-vault-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků

## <a name="use-cli-10-to-set-up-key-vault"></a>Nastavit Key Vault pomocí rozhraní příkazového řádku 1.0
Vytvoření trezoru klíčů pomocí rozhraní příkazového řádku (CLI), naleznete v části [Key Vault spravovat pomocí rozhraní příkazového řádku](../../key-vault/key-vault-manage-with-cli2.md#create-a-key-vault).

Pro rozhraní příkazového řádku 1.0 budete muset vytvořit trezor klíčů, před přiřazením zásady nasazení. Pak můžete přiřadit zásady pomocí následujícího příkazu:

    azure keyvault set-policy ContosoKeyVault –enabled-for-deployment true

## <a name="use-templates-to-set-up-key-vault"></a>Použití šablon nastavit Key Vault
Pokud použijete šablonu, je nutné nastavit `enabledForDeployment` vlastnost `true` prostředku Key Vault.

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
