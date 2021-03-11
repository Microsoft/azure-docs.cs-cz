---
title: Vytvoření virtuálního počítače se systémem Linux v Azure ze šablony
description: Použití rozhraní příkazového řádku Azure k vytvoření virtuálního počítače se systémem Linux ze šablony Správce prostředků
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: a4e1bf56df52717255d2bae0a38186335d922ff1
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554679"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Postup vytvoření virtuálního počítače se systémem Linux pomocí šablon Azure Resource Manager

Naučte se vytvořit virtuální počítač se systémem Linux pomocí šablony Azure Resource Manager a Azure CLI z Azure Cloud Shell. Informace o vytvoření virtuálního počítače s Windows najdete v tématu [Vytvoření virtuálního počítače s Windows pomocí šablony Správce prostředků](../windows/ps-template.md).

Alternativou je nasazení šablony z Azure Portal. Pokud chcete otevřít šablonu na portálu, vyberte tlačítko **nasadit do Azure** .

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>Přehled šablon

Šablony Azure Resource Manager jsou soubory JSON, které definují infrastrukturu a konfiguraci řešení Azure. Pomocí šablony můžete řešení opakovaně nasadit v průběhu životního cyklu a mít přitom jistotu, že se prostředky nasadí konzistentně. Další informace o formátu šablony a způsobu jejich sestavení najdete v tématu [rychlý Start: vytvoření a nasazení Azure Resource Manager šablon pomocí Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Syntaxi JSON pro typy prostředků najdete v tématu [Definování prostředků v šablonách Azure Resource Manageru](/azure/templates/microsoft.compute/allversions).

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Vytvoření virtuálního počítače Azure obvykle zahrnuje dva kroky:

1. Vytvořte skupinu prostředků. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupina prostředků musí být vytvořená už před vytvořením virtuálního počítače.
1. Vytvoří virtuální počítač.

Následující příklad vytvoří virtuální počítač ze [šablony Azure pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Pro toto nasazení je povoleno pouze ověřování SSH. Po zobrazení výzvy zadejte hodnotu vlastního veřejného klíče SSH, jako je například obsah *~/.ssh/id_rsa. pub*. Pokud potřebujete vytvořit pár klíčů SSH, přečtěte si téma [Vytvoření a použití páru klíčů ssh pro virtuální počítače se systémem Linux v Azure](mac-create-ssh-keys.md). Tady je kopie šablony:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Pokud chcete spustit skript rozhraní příkazového řádku, vyberte **zkusit ho** a otevřete Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

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
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

Poslední příkaz Azure CLI zobrazuje veřejnou IP adresu nově vytvořeného virtuálního počítače. K připojení k virtuálnímu počítači budete potřebovat veřejnou IP adresu. Další informace najdete v další části tohoto článku.

V předchozím příkladu jste zadali šablonu uloženou v GitHubu. Můžete si také stáhnout nebo vytvořit šablonu a zadat místní cestu s `--template-file` parametrem.

Tady je několik dalších zdrojů:

- Další informace o vývoji šablon Správce prostředků najdete v [dokumentaci k Azure Resource Manager](../../azure-resource-manager/index.yml).
- Schémata virtuálních počítačů Azure najdete v tématu Referenční informace k [šablonám Azure](/azure/templates/microsoft.compute/allversions).
- Další ukázky šablon virtuálních počítačů najdete v tématu [šablony pro rychlý Start pro Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

## <a name="connect-to-virtual-machine"></a>Připojení k virtuálnímu počítači

Pak můžete k VIRTUÁLNÍmu počítači přes SSH jako normální. Zadejte vlastní veřejnou IP adresu z předchozího příkazu:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Další kroky

V tomto příkladu jste vytvořili základní virtuální počítač Linux. Další Správce prostředků šablon, které zahrnují aplikační architektury nebo vytváření složitějších prostředí, najdete v [šablonách pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular).

Další informace o vytváření šablon najdete v syntaxi a vlastnostech JSON pro typy prostředků, které jste nasadili:

- [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines)
