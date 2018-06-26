---
title: Vytvoření virtuálního počítače s Linuxem v Azure ze šablony | Microsoft Docs
description: Jak používat rozhraní příkazového řádku Azure k vytvoření virtuálního počítače s Linuxem ze šablony Resource Manageru
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 05/30/2018
ms.author: iainfou
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fc2b4dde9796336112d6c8a68d16d0b3006b3fee
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36936410"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak vytvořit virtuální počítač s Linuxem pomocí šablony Azure Resource Manager
Tento článek ukazuje, jak rychle nasadit virtuální počítač (VM) s Linuxem pomocí šablony Azure Resource Manager a rozhraní příkazového řádku Azure. 


## <a name="templates-overview"></a>Přehled šablon
Šablony Azure Resource Manageru jsou soubory JSON, které definují, infrastruktury a konfigurace řešení Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. Další informace o formátu šablony a jak vytvořit, najdete v části [vytvoření vaší první šablony Azure Resource Manager](../../azure-resource-manager/resource-manager-create-first-template.md). Syntaxi JSON pro typy prostředků najdete v tématu [Definování prostředků v šablonách Azure Resource Manageru](/azure/templates/).


## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače. Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroupVM* v *eastus* oblasti:

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače
Následující příklad vytvoří virtuální počítač z [této šablony Azure Resource Manageru](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json) s [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create). Je povoleno pouze ověřování SSH. Po zobrazení výzvy zadejte hodnotu vlastní veřejný klíč SSH, jako je například obsah *~/.ssh/id_rsa.pub*. Pokud potřebujete vytvořit dvojici klíčů SSH, přečtěte si [postup vytvoření a používání dvojici klíčů SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md).

```azurecli
az group deployment create \
    --resource-group myResourceGroup \
    --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

V předchozím příkladu zadat šablonu uložené v Githubu. Můžete také stáhnout nebo vytvořit šablonu a zadejte místní cestu s `--template-file` parametr.


## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači
Chcete-li SSH k virtuálnímu počítači, získat veřejnou IP adresu s [az virtuálních počítačů zobrazit](/cli/azure/vm#az-vm-show):

```azurecli
az vm show \
    --resource-group myResourceGroup \
    --name sshvm \
    --show-details \
    --query publicIps \
    --output tsv
```

Pak můžete SSH pro virtuální počítač jako normální. Zadejte vlastní veřejnou IP adresu z předchozí příkaz:

```bash
ssh azureuser@<ipAddress>
```

## <a name="next-steps"></a>Další postup
V tomto příkladu jste vytvořili základní virtuální počítač s Linuxem. Pro další šablony správce prostředků, které zahrnují aplikační architektury nebo vytvořit složitější prostředí, přejděte [galerii šablon Azure rychlý Start](https://azure.microsoft.com/documentation/templates/).