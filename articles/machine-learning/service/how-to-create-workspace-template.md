---
title: Použití šablony Azure Resource Manager k vytvoření pracovního prostoru
titleSuffix: Azure Machine Learning service
description: Naučte se, jak pomocí šablony Azure Resource Manager vytvořit nový pracovní prostor služby Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 89c24512e50dedbf10d145088ec77c2e6e303d1e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873150"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Použití šablony Azure Resource Manager k vytvoření pracovního prostoru pro službu Azure Machine Learning

V tomto článku se dozvíte několik způsobů, jak vytvořit pracovní prostor služby Azure Machine Learning pomocí šablon Azure Resource Manager. Šablona Správce prostředků usnadňuje vytváření prostředků jako jediné koordinované operace. Šablona je dokument JSON, který definuje prostředky, které jsou potřebné pro nasazení. Může také specifikovat parametry nasazení. Parametry slouží k poskytnutí vstupních hodnot při použití šablony.

Další informace najdete v tématu [nasazení aplikace pomocí šablony Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Požadavky

* **Předplatného Azure**. Pokud ho nemáte, vyzkoušejte [bezplatnou nebo placená verzi služby Azure Machine Learning](https://aka.ms/AMLFree).

* Pokud chcete použít šablonu z CLI, potřebujete buď [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) , nebo rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="resource-manager-template"></a>Šablona Resource Manageru

Následující šablonu Správce prostředků lze použít k vytvoření pracovního prostoru služby Azure Machine Learning a přidružených prostředků Azure:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Tato šablona vytvoří následující služby Azure:

* Skupina prostředků Azure
* Azure Storage Account
* Azure Key Vault
* Azure Application Insights
* Registr kontejneru Azure
* Pracovní prostor služby Azure Machine Learning

Skupina prostředků je kontejner, který obsahuje služby. Jednotlivé služby jsou vyžadovány pracovním prostorem Azure Machine Learning.

Příklad šablony má dva parametry:

* **Umístění** , kde se vytvoří skupina prostředků a služby.

    Šablona bude používat umístění, které jste vybrali pro většinu prostředků. Výjimkou je služba Application Insights, která není dostupná ve všech umístěních, ve kterých jsou jiné služby. Pokud vyberete umístění, kde není k dispozici, bude služba vytvořena v umístění Střed USA – jih.

* **Název pracovního prostoru**, což je popisný název pracovního prostoru Azure Machine Learning.

    Názvy ostatních služeb jsou vygenerovány náhodně.

Další informace o šablonách najdete v následujících článcích:

* [Vytváření šablon Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Nasazení aplikace pomocí šablon Azure Resource Manager](../../azure-resource-manager/resource-group-template-deploy.md)
* [Typy prostředků Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Postupujte podle kroků v části [nasazení prostředků z vlastní šablony](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po přijetí na obrazovku __Upravit šablonu__ vložte do šablony z tohoto dokumentu šablonu.
1. Vyberte __Uložit__ a použijte šablonu. Zadejte následující informace a vyjádřete souhlas s uvedenými podmínkami a ujednáními:

   * Předplatné: Vyberte předplatné Azure, které chcete použít pro tyto prostředky.
   * Skupina prostředků: Vyberte nebo vytvořte skupinu prostředků, která bude obsahovat služby.
   * Název pracovního prostoru: Název, který se má použít pro pracovní prostor Azure Machine Learning, který se má vytvořit. Název pracovního prostoru musí být dlouhý 3 až 33 znaků. Může obsahovat pouze alfanumerické znaky a znak "-".
   * Oblasti Vyberte umístění, kde se budou prostředky vytvářet.

Další informace najdete v tématu [nasazení prostředků z vlastní šablony](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Použití Azure Powershell

V tomto příkladu se předpokládá, že jste šablonu uložili do souboru s `azuredeploy.json` názvem v aktuálním adresáři:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) a [nasazení privátní šablony Správce prostředků pomocí tokenu SAS a Azure PowerShell](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Použití Azure CLI

V tomto příkladu se předpokládá, že jste šablonu uložili do souboru s `azuredeploy.json` názvem v aktuálním adresáři:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) a [nasazení privátních správce prostředků šablony s tokenem SAS a](../../azure-resource-manager/resource-manager-cli-sas-token.md)rozhraním příkazového řádku Azure CLI.

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zásady přístupu a šablony Azure Resource Manager

Když použijete šablonu Azure Resource Manager k vytvoření pracovního prostoru a přidružených prostředků (včetně Azure Key Vault), několikrát. Například použití šablony několikrát se stejnými parametry jako součást kanálu průběžné integrace a nasazení.

Většina operací vytváření prostředků prostřednictvím šablon je idempotentní, ale Key Vault neodstraní zásady přístupu pokaždé, když se šablona používá. Vymazání zásad přístupu přeruší přístup k Key Vault pro libovolný existující pracovní prostor, který ho používá. Například zastavení/vytvoření funkcí Azure Notebooks virtuálního počítače může selhat.  

Chcete-li se tomuto problému vyhnout, doporučujeme jeden z následujících přístupů:

*  Nesaďte šablonu více než jednou pro stejné parametry. Nebo odstraňte existující prostředky, abyste je mohli znovu vytvořit pomocí šablony.
  
* Zkontrolujte zásady přístupu Key Vault a pak pomocí těchto zásad nastavte vlastnost accessPolicies šablony.
* Ověřte, zda prostředek Key Vault již existuje. Pokud tomu tak není, nevytvářejte ho znovu prostřednictvím šablony. Například přidejte parametr, který umožňuje zakázat vytvoření prostředku Key Vault, pokud již existuje.

## <a name="next-steps"></a>Další postup

* [Nasazení prostředků pomocí Správce prostředků šablon a Správce prostředků REST API](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).
