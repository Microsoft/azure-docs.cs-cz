---
title: Vytvoření a šifrování sady škálování virtuálních počítačů pomocí Azure Resource Manager šablon
description: V tomto rychlém startu se dozvíte, jak pomocí šablon Azure Resource Manager vytvořit a zašifrovat sadu škálování virtuálního počítače.
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: c2b49a7e7e14bfbefcca64133ff23fdfabe53e7b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198427"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Zašifrujte Virtual Machine Scale Sets pomocí Azure Resource Manager

Pomocí šablon Azure Resource Manager můžete šifrovat nebo dešifrovat virtuální počítače se systémem Linux Virtual Machine Scale Sets.

## <a name="deploying-templates"></a>Nasazení šablon

Nejprve vyberte šablonu, která odpovídá vašemu scénáři.

- [Povolit šifrování disku na běžící sadě virtuálních počítačů se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Povolení šifrování disku ve spuštěné sadě škálování virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Nasazení sady virtuálních počítačů se systémem Linux s využitím škálování virtuálního počítače s JumpBox a povolení šifrování v systémech Linux Virtual Machine Scale Sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Nasazení sady virtuálních počítačů s virtuálním počítačem s JumpBox a povolením šifrování ve virtuálních počítačích s Windows VM Scale Sets](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Zakázat šifrování disku na běžící sadě virtuálních počítačů se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Zakázat šifrování disku ve spuštěné sadě škálování virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Potom postupujte podle těchto kroků:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Další kroky

- [Azure Disk Encryption pro Virtual Machine Scale Sets](disk-encryption-overview.md)
- [Šifrování sady škálování virtuálních počítačů pomocí Azure CLI](disk-encryption-cli.md)
- [Šifrování virtuálních počítačů pomocí Azure PowerShell](disk-encryption-powershell.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s pořadím rozšíření pro škálování sady virtuálních počítačů](disk-encryption-extension-sequencing.md)
