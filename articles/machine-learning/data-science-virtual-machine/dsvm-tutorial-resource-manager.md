---
title: 'Rychlý Start: Vytvoření šablony Data Science VM-Správce prostředků'
titleSuffix: Azure Data Science Virtual Machine
description: V tomto rychlém startu použijete šablonu Azure Resource Manager k rychlému nasazení Data Science Virtual Machine
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 06/10/2020
ms.service: machine-learning
ms.subservice: data-science-vm
ms.topic: quickstart
ms.openlocfilehash: 8f83cdb09b94fb7eda1f8f700390be8b2c2147e0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90883159"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Rychlý Start: vytvoření Data Science Virtual Machine Ubuntu pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak vytvořit Ubuntu 18,04 Data Science Virtual Machine pomocí šablony Azure Resource Manager (šablony ARM). Data Virtual Machines pro datové vědy jsou cloudové virtuální počítače, které jsou předem nahrané pomocí sady datových vědy a architektur a nástrojů strojového učení. Při nasazení na výpočetní prostředky využívající GPU jsou všechny nástroje a knihovny nakonfigurované tak, aby používaly GPU.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/services/machine-learning/).

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí**, potřebujete [Azure CLI](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

V šabloně jsou definované následující prostředky:

* [Microsoft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft. Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft. Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Vytvořte cloudový virtuální počítač. V této šabloně je virtuální počítač nakonfigurovaný jako Data Science Virtual Machine se systémem Ubuntu 18,04.

## <a name="deploy-the-template"></a>Nasazení šablony

Pokud chcete použít šablonu z Azure CLI, přihlaste se a vyberte předplatné (viz [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli)). Potom následujícím příkazem:

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

Když spustíte výše uvedený příkaz, zadejte:

1. Název skupiny prostředků, kterou chcete vytvořit, aby obsahovala DSVM a přidružené prostředky.
1. Umístění Azure, ve kterém chcete provést nasazení.
1. Typ ověřování, který chcete použít (zadejte řetězec `password` nebo `sshPublicKey` ).
1. Přihlašovací jméno účtu správce (Tato hodnota nemusí být `admin` ).
1. Hodnota hesla nebo veřejného klíče SSH pro účet.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Postup zobrazení Data Science Virtual Machine:

1. Přejděte na web [Azure Portal](https://portal.azure.com).
1. Přihlaste se.
1. Vyberte skupinu prostředků, kterou jste právě vytvořili.

Zobrazí se informace o skupině prostředků:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Snímek obrazovky se základní skupinou prostředků obsahující DSVM":::

Klikněte na prostředek virtuálního počítače a přejděte na jeho informační stránku. Tady najdete informace o virtuálním počítači, včetně podrobností o připojení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tento virtuální počítač nechcete používat, odstraňte ho. Vzhledem k tomu, že DSVM je přidružen k jiným prostředkům, jako je například účet úložiště, pravděpodobně budete chtít odstranit celou vytvořenou skupinu prostředků. Skupinu prostředků můžete odstranit pomocí portálu kliknutím na tlačítko **Odstranit** a potvrzením. Případně můžete skupinu prostředků z CLI odstranit pomocí:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Data Science Virtual Machine ze šablony ARM.

> [!div class="nextstepaction"]
> [Ukázkové programy & návody ML](dsvm-samples-and-walkthroughs.md)
