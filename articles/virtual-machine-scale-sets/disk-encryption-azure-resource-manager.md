---
title: Vytvoření a šifrování škálovací sady virtuálních strojů pomocí šablon Azure Resource Manager
description: V tomto rychlém startu se dozvíte, jak pomocí šablon Azure Resource Manager vytvořit a zašifrovat škálovací sadu virtuálních strojů.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "72529997"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Šifrování škálovacích sad virtuálních strojů pomocí Azure Resource Manageru

Škálovací sady virtuálních strojů Linuxu můžete šifrovat nebo dešifrovat pomocí šablon Azure Resource Manager.

## <a name="deploying-templates"></a>Nasazení šablon

Nejprve vyberte šablonu, která odpovídá vašemu scénáři.

- [Povolení šifrování disku ve spuštěné škálovací sadě virtuálních počítačů s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Povolení šifrování disku ve spuštěné škálovací sadě virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Nasazení škálovací sady virtuálních strojů virtuálních virtuálních počítačů se jumpboxem a umožňuje šifrování na škálovacích sadách virtuálních strojů pro Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Nasazení škálovací sady virtuálních počítačů virtuálních počítačů se systémem Windows pomocí funkce jumpbox a umožňuje šifrování ve škálovacích sadách virtuálních strojů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Zakázání šifrování disku ve spuštěné škálovací sadě virtuálních počítačů s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Zakázání šifrování disku ve spuštěné škálovací sadě virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Potom postupujte podle těchto kroků:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Další kroky

- [Šifrování disku Azure pro škálovací sady virtuálních počítačů](disk-encryption-overview.md)
- [Šifrování škálovacích sad virtuálních strojů pomocí příkazového příkazového příkazu Azure](disk-encryption-cli.md)
- [Šifrování škálovacích sad virtuálních strojů pomocí Azure PowerShellu](disk-encryption-powershell.md)
- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure](disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s řazením rozšíření škálovací sady virtuálních počítačů](disk-encryption-extension-sequencing.md)