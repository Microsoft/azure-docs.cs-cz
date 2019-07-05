---
title: Pomocí šablony Azure Resource Manageru k vytvoření pracovního prostoru
titleSuffix: Azure Machine Learning service
description: Zjistěte, jak vytvořit nový pracovní prostor služby Azure Machine Learning pomocí šablony Azure Resource Manageru.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 04/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 4e0af3b395ec640fd037a1e76365408c10613340
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477018"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Vytvořit pracovní prostor pro službu Azure Machine Learning pomocí šablony Azure Resource Manageru

V tomto článku se dozvíte několik způsobů, jak vytvořit pracovní prostor služby Azure Machine Learning pomocí šablon Azure Resource Manageru. Šablony Resource Manageru umožňuje snadno vytvářet prostředky jako jediné koordinované operace. Šablona je dokument JSON s definicí prostředků, které jsou potřebné pro nasazení. Kromě toho může určovat parametrů nasazení. Parametry se používají při použití šablona zadávat vstupní hodnoty.

Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manageru](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatného Azure**. Pokud nemáte, zkuste [bezplatné nebo placené verzi aplikace služby Azure Machine Learning](https://aka.ms/AMLFree).

* Použití šablony z rozhraní příkazového řádku, je nutné buď [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Šablona Resource Manageru

Následující šablony Resource Manageru je možné vytvořit pracovní prostor služby Azure Machine Learning service a související prostředky Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Tato šablona vytvoří následující služby Azure:

* Skupina prostředků Azure
* Azure Storage Account
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Pracovní prostor služby Azure Machine Learning

Skupina prostředků je kontejner, který obsahuje služby. Různé služby jsou vyžadované pracovního prostoru Azure Machine Learning.

Ukázková šablona má dva parametry:

* **Umístění** kde se vytvoří skupina prostředků a služeb.

    Šablona použije umístění, které jste vybrali pro většinu prostředků. Výjimkou je služba Application Insights, která není k dispozici ve všech umístěních, které jsou ostatním službám. Pokud vyberete umístění, kde není k dispozici, služba se vytvoří v umístění střed USA – jih.

* **Název pracovního prostoru**, což je popisný název pracovního prostoru Azure Machine Learning.

    Názvy jiných služeb, jsou generovány náhodně.

Další informace o šablonách najdete v následujících článcích:

* [Tvorba šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manageru](../../azure-resource-manager/resource-group-template-deploy.md)
* [Typy prostředků Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Postupujte podle kroků v [nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Při doručení na __úpravy šablony__ obrazovky, vložte do šablony z tohoto dokumentu.
1. Vyberte __Uložit__ použít šablonu. Zadejte následující informace a souhlas s uvedené podmínky a ujednání:

   * Předplatné: Vyberte předplatné Azure má použít pro tyto prostředky.
   * Skupina prostředků: Vyberte nebo vytvořte skupinu prostředků k obsáhnutí služeb.
   * Název pracovního prostoru: Název, který se má použít pro pracovní prostor Azure Machine Learning, která bude vytvořena. Název pracovního prostoru musí být dlouhý 3 až 33 znaků. Může obsahovat jenom alfanumerické znaky a "-".
   * Umístění: Vyberte umístění, kde se prostředky vytvoří.

     ![Parametry šablony na webu Azure Portal](media/how-to-create-workspace-template/template-parameters.png)

Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

Tento příklad předpokládá, že jste uložili šablony do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) a [nasazení privátní šablony Resource Manageru s tokenem SAS a prostředí Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Použití Azure CLI

Tento příklad předpokládá, že jste uložili šablony do souboru s názvem `azuredeploy.json` v aktuálním adresáři:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) a [nasazení privátní šablony Resource Manageru s tokenem SAS a rozhraní příkazového řádku Azure](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure zásad přístupu trezoru klíčů a šablon Azure Resource Manageru

Při použití šablony Azure Resource Manageru k vytvoření pracovního prostoru a přidružené prostředky (včetně služby Azure Key Vault), více než jednou. Například pomocí šablony více než jednou se stejnými parametry jako součást průběžné integrace a nasazení kanálu.

Většina operací vytváření prostředků prostřednictvím šablony jsou idempotentní, ale služby Key Vault vymaže zásady přístupu při každém použití této šablony. Vymazává se zásady konce přístupu ke službě Key Vault pro existující pracovní prostor, který jej používá. Například může selhat funkce Stop nebo vytvoření virtuálního počítače Azure, poznámkové bloky.  

K tomuto problému vyhnout, doporučujeme jednu z následujících postupů:

*  Nenasazujte šablony více než jednou pro stejné parametry. Nebo odstraňte existující prostředky před je znovu vytvořit pomocí šablony.
  
* Zkontrolujte zásady přístupu trezoru klíčů a potom nastavte vlastnost accessPolicies šablony pomocí těchto zásad.
* Zkontrolujte, jestli prostředek Key Vault už existuje. Pokud ano, nelze jej znovu vytvořit pomocí šablony. Například přidejte parametr, který umožňuje zakázat vytvoření prostředku služby Key Vault, pokud již existuje.

## <a name="next-steps"></a>Další postup

* [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní REST API Resource Manageru](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
