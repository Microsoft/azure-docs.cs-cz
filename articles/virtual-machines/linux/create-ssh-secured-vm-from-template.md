---
title: Vytvoření virtuálního počítače s Linuxem v Azure ze šablony | Dokumentace Microsoftu
description: Jak používat rozhraní příkazového řádku Azure k vytvoření virtuálního počítače s Linuxem ze šablony Resource Manageru
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 721b8378-9e47-411e-842c-ec3276d3256a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cf213bec0b4d9e9662a9b554be72102f11e8d2d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67295459"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak vytvořit virtuální počítač s Linuxem pomocí šablony Azure Resource Manageru

Zjistěte, jak vytvořit virtuální počítač (VM) s Linuxem pomocí šablony Azure Resource Manageru a Azure CLI ze služby Azure Cloud shell. Vytvoření virtuálního počítače s Windows, najdete v článku [vytvořit virtuální počítač s Windows pomocí šablony Resource Manageru](../windows/ps-template.md).

## <a name="templates-overview"></a>Přehled šablon

Šablony Azure Resource Manageru jsou soubory JSON, které definují infrastrukturu a konfiguraci vašeho řešení Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. Další informace o formátu šablony a způsobu jejího sestavení najdete v tématu [rychlý start: Vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md). Syntaxi JSON pro typy prostředků najdete v tématu [Definování prostředků v šablonách Azure Resource Manageru](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure obvykle zahrnuje dva kroky:

1. Vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače.
1. Vytvoří virtuální počítač.

Následující příklad vytvoří virtuální počítač ze [šablona Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Pro toto nasazení je povolené jenom ověřování SSH. Po zobrazení výzvy zadejte hodnotu vlastní veřejný klíč SSH, jako je například obsah *~/.ssh/id_rsa.pub*. Pokud potřebujete k vytvoření páru klíčů SSH, přečtěte si [postup vytvoření a použití páru klíčů SSH pro virtuální počítače s Linuxem v Azure](mac-create-ssh-keys.md). Tady je kopie šablony:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Chcete-li spustit skript rozhraní příkazového řádku, vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az group deployment create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Poslední příkaz rozhraní příkazového řádku Azure zobrazí veřejnou IP adresu nově vytvořenému virtuálnímu počítači. Budete potřebovat veřejnou IP adresu pro připojení k virtuálnímu počítači. Další části tohoto článku.

V předchozím příkladu jste zadali šablony uložené na Githubu. Můžete také stáhnout nebo vytvořit šablonu a zadejte do místní cesty `--template-file` parametru.

Tady jsou některé další prostředky:

- Zjistěte, jak vyvíjet šablony Resource Manageru, najdete v článku [dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/).
- Schémata virtuálních počítačů Azure najdete v tématu [referenčními informacemi k šablonám Azure](/azure/templates/microsoft.compute/allversions).
- Další ukázkové šablony virtuálního počítače najdete v tématu [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Pak můžete SSH k virtuálnímu počítači jako za normálních okolností. Zadejte vlastní veřejnou IP adresu z předchozího příkazu:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Další postup

V tomto příkladu jste vytvořili základní virtuální počítač s Linuxem. Další šablony Resource Manageru, které zahrnují aplikačních architektur nebo vytvářet složitější prostředí, přejděte [šablon rychlého startu Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Další informace o vytváření šablon, zobrazí se syntaxe JSON a vlastnosti pro typy prostředků, které jste nasadili:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
