---
title: Kurz – vytvoření pracovního prostoru Azure ML – Správce prostředků šablony
description: V tomto kurzu použijete šablonu Azure Resource Manager k rychlému nasazení pracovního prostoru Azure pro strojové učení.
services: machine-learning
author: lobrien
ms.author: laobri
ms.custom: subject-armqs
ms.date: 05/26/2020
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.openlocfilehash: 098d82e6521a4a355ac31809937b589f984816f2
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86027150"
---
# <a name="tutorial-deploy-an-azure-machine-learning-workspace-using-an-arm-template"></a>Kurz: nasazení pracovního prostoru Azure Machine Learning pomocí šablony ARM

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

V tomto kurzu se dozvíte, jak vytvořit pracovní prostor Azure Machine Learning pomocí šablony Azure Resource Manager (šablona ARM). Pracovní prostory Azure Machine Learning organizují všechny vaše prostředky strojového učení od datových sad směrného plánu až po nasazené modely. Pracovní prostory jsou jediné místo, kde můžete spolupracovat s kolegy při vytváření, spouštění a revizi experimentů, spravovat vaše školicí a Inferencing výpočetní prostředky a modely a monitorovat nasazené verze.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a Vy jste obeznámeni s používáním šablon ARM, vyberte tlačítko **nasadit do Azure** . Šablona se otevře v Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-machine-learning-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/services/machine-learning/), ještě než začnete.

* Pokud chcete v tomto dokumentu použít příkazy rozhraní příkazového řádku z vašeho **místního prostředí**, potřebujete [Azure CLI](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-machine-learning-create/).

:::code language="json" source="~/quickstart-templates/101-machine-learning-create/azuredeploy.json" range="1-258" highlight="224-254":::

V šabloně jsou definované následující prostředky:

* [Microsoft. MachineLearningServices/pracovní prostory](/azure/templates/microsoft.machinelearningservices/workspaces): Vytvořte pracovní prostor Azure ml. V této šabloně je umístění a název parametry, které může uživatel předat nebo interaktivně zadat.

## <a name="deploy-the-template"></a>Nasazení šablony

Pokud chcete použít šablonu z Azure CLI, přihlaste se a vyberte předplatné (viz [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli)). Potom následujícím příkazem:

```azurecli-interactive
read -p "Enter a project name that is used for generating resource names:" projectName &&
read -p "Enter the location (i.e. centralus):" location &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-machine-learning-create/azuredeploy.json" &&
resourceGroupName="${projectName}rg" &&
workspaceName="${projectName}ws" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters workspaceName=$workspaceName location=$location &&
echo "Press [ENTER] to continue ..." &&
read
```

Když spustíte výše uvedený příkaz, zadejte:

1. Název projektu, který bude tvořit základ názvů vytvořené skupiny prostředků a pracovního prostoru Azure ML.
1. Umístění Azure, ve kterém chcete provést nasazení.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Postup zobrazení pracovního prostoru Azure ML:

1. Přejděte do části https://portal.azure.com (Soubor > Nový > Jiné).
1. Přihlásit se.
1. Vyberte pracovní prostor, který jste právě vytvořili.

Uvidíte domovskou stránku Azure Machine Learning:

:::image type="content" source="media/tutorial-resource-manager-workspace/workspace-home.png" alt-text="Snímek obrazovky pracovního prostoru Azure ML":::

Pokud chcete zobrazit všechny prostředky přidružené k nasazení, klikněte na odkaz v levém horním rohu s názvem pracovního prostoru (na snímku obrazovky `my_templated_ws` ). Tento odkaz vás přesměruje do skupiny prostředků v Azure Portal. Název skupiny prostředků je `{projectName}rg` a pracovní prostor se jmenuje `{projectName}ws` .

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tento pracovní prostor nechcete používat, odstraňte ho. Vzhledem k tomu, že je pracovní prostor přidružený k jiným prostředkům, jako je například účet úložiště, budete pravděpodobně chtít odstranit celou vytvořenou skupinu prostředků. Skupinu prostředků můžete odstranit pomocí portálu kliknutím na tlačítko **Odstranit** a potvrzením. Případně můžete skupinu prostředků z CLI odstranit pomocí:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili Azure Machine Learning pracovní prostor ze šablony ARM. Pokud chcete prozkoumat Azure Machine Learning, pokračujte v tomto kurzu.

> [!div class="nextstepaction"]
> [Kurz: Začínáme s vytvářením prvního experimentu ML pomocí sady Python SDK](tutorial-1st-experiment-sdk-setup.md)
