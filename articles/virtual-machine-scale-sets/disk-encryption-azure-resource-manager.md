---
title: Vytvoření a šifrování sady škálování virtuálních počítačů pomocí Azure Resource Manager šablon
description: V tomto rychlém startu se dozvíte, jak pomocí šablon Azure Resource Manager vytvořit a zašifrovat sadu škálování virtuálního počítače.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "72529997"
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