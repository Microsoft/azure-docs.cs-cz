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
ms.openlocfilehash: 7e3e45d96839be06deec238deccdc9873492d68f
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84660245"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-a-resource-manager-template"></a>Rychlý Start: vytvoření Data Science Virtual Machine Ubuntu pomocí šablony Správce prostředků
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto rychlém startu se dozvíte, jak vytvořit Ubuntu 18,04 Data Science Virtual Machine pomocí šablony Azure Resource Manager. Data Virtual Machines pro datové vědy jsou cloudové virtuální počítače, které jsou předem nahrané pomocí sady datových vědy a architektur a nástrojů strojového učení. Při nasazení na výpočetní prostředky využívající GPU jsou všechny nástroje a knihovny nakonfigurované tak, aby používaly GPU. 

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://aka.ms/AMLFree) před tím, než začnete.

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí**, potřebujete [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/). Úplná šablona pro tento článek je moc dlouhá, takže se tady nedá zobrazit. Chcete-li zobrazit úplnou šablonu, přečtěte si téma [azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json). Část definující konkrétní DSVM se zobrazí zde:

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" range="235-276":::

V šabloně jsou definované následující prostředky:

* [Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Vytvořte cloudový virtuální počítač. V této šabloně je virtuální počítač nakonfigurovaný jako Data Science Virtual Machine se systémem Ubuntu 18,04.

### <a name="deploy-the-template"></a>Nasazení šablony 

Pokud chcete použít šablonu z Azure CLI, přihlaste se a vyberte předplatné (viz [přihlášení pomocí Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)). Potom následujícím příkazem:

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
1. Umístění Azure, ve kterém chcete provést nasazení
1. Typ ověřování, který chcete použít (zadejte řetězec `password` nebo `sshPublicKey` )
1. Přihlašovací jméno účtu správce (Tato hodnota nemusí být `admin` )
1. Hodnota hesla nebo veřejného klíče SSH pro účet

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Postup zobrazení Data Science Virtual Machine:

1. Přejděte na https://portal.azure.com. 
1. Přihlásit se 
1. Vyberte skupinu prostředků, kterou jste právě vytvořili.

Zobrazí se informace o skupině prostředků: 

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Snímek obrazovky se základní skupinou prostředků obsahující DSVM":::

Klikněte na prostředek virtuálního počítače a přejděte na jeho informační stránku. Tady najdete informace o virtuálním počítači, včetně podrobností o připojení. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tento virtuální počítač nechcete používat, odstraňte ho. Vzhledem k tomu, že DSVM je přidružen k jiným prostředkům, jako je například účet úložiště, pravděpodobně budete chtít odstranit celou vytvořenou skupinu prostředků. Skupinu prostředků můžete odstranit pomocí portálu kliknutím na tlačítko Odstranit a potvrzením. Případně můžete skupinu prostředků z CLI odstranit pomocí: 

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Data Science Virtual Machine ze šablony Azure Resource Manager. 

> [!div class="nextstepaction"]
> [Ukázkové programy & návody ML](dsvm-samples-and-walkthroughs.md)
