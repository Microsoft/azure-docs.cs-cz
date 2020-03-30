---
title: Vytvoření virtuálního počítače s Linuxem v Azure ze šablony
description: Jak pomocí azure cli vytvořit virtuální počítač s Linuxem ze šablony Správce prostředků
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: 581eadc60835b758f67ae616d4413800f1d6d718
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969528"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Jak vytvořit virtuální počítač S Linuxem se šablonami Azure Resource Manageru

Zjistěte, jak vytvořit virtuální počítač (VM) Linuxpomocí šablony Azure Resource Manager a Azure CLI z prostředí Azure Cloud. Pokud chcete vytvořit virtuální počítač s Windows, přečtěte si informace [o vytvoření virtuálního počítače s Windows ze šablony Správce prostředků](../windows/ps-template.md).

## <a name="templates-overview"></a>Přehled šablon

Šablony Azure Resource Manageru jsou soubory JSON, které definují infrastrukturu a konfiguraci vašeho řešení Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. Další informace o formátu šablony a způsobu jeho vytváření najdete v [tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Syntaxi JSON pro typy prostředků najdete v tématu [Definování prostředků v šablonách Azure Resource Manageru](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure obvykle zahrnuje dva kroky:

1. Vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače.
1. Vytvoří virtuální počítač.

Následující příklad vytvoří virtuální počítač ze [šablony Azure Quickstart](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Pro toto nasazení je povoleno pouze ověřování SSH. Po zobrazení výzvy zadejte hodnotu vlastního veřejného klíče SSH, například obsah *~/.ssh/id_rsa.pub*. Pokud potřebujete vytvořit dvojici klíčů SSH, přečtěte [si, jak vytvořit a použít dvojici klíčů SSH pro virtuální počítače SSH v Azure](mac-create-ssh-keys.md). Zde je kopie šablony:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Chcete-li spustit skript příkazového příkazového příkazu, vyberte **try s** otevřením prostředí Azure Cloud. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a pak vyberte **Vložit**:

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

Poslední příkaz Azure CLI zobrazuje veřejnou IP adresu nově vytvořeného virtuálního počítače. Potřebujete veřejnou IP adresu pro připojení k virtuálnímu počítači. Podívejte se na další část tohoto článku.

V předchozím příkladu jste zadali šablonu uloženou v GitHubu. Můžete také stáhnout nebo vytvořit šablonu a `--template-file` určit místní cestu s parametrem.

Tady je několik dalších zdrojů:

- Informace o vývoji šablon Správce prostředků najdete v [dokumentaci ke Správci prostředků Azure](/azure/azure-resource-manager/).
- Pokud jde o schémata virtuálních strojů Azure, přečtěte si informace [o odkazech na šablonu Azure](/azure/templates/microsoft.compute/allversions).
- Další ukázky šablon virtuálních strojů najdete v [tématu Šablony rychlých startů Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Potom můžete SSH do virtuálního počítače jako normální. Zadejte vlastní veřejnou IP adresu z předchozího příkazu:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Další kroky

V tomto příkladu jste vytvořili základní virtuální počítač s Linuxem. Další šablony Správce prostředků, které obsahují aplikační architektury nebo vytvářejí složitější prostředí, najdete [v šablonách Azure QuickStart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Další informace o vytváření šablon naleznete v syntaxi JSON a vlastnostech typů prostředků, které jste nasadili:

- [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAdresy](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
