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
ms.openlocfilehash: 4284e94f8d8d0effd160c5048f54fcbede417e38
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86129749"
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

1. Klikněte na **Nasadit do Azure**.
2. Vyplňte požadovaná pole a pak vyjádřete souhlas s podmínkami a ujednáními.
3. Šablonu nasadíte kliknutím na **koupit** .

## <a name="next-steps"></a>Další kroky

- [Azure Disk Encryption pro Virtual Machine Scale Sets](disk-encryption-overview.md)
- [Šifrování sady škálování virtuálních počítačů pomocí Azure CLI](disk-encryption-cli.md)
- [Šifrování virtuálních počítačů pomocí Azure PowerShell](disk-encryption-powershell.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s pořadím rozšíření pro škálování sady virtuálních počítačů](disk-encryption-extension-sequencing.md)
