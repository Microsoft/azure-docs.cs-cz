---
title: Nasazení prostředků pomocí prostředí PowerShell a šablony | Microsoft Docs
description: Nasazení prostředky do Azure pomocí Azure Resource Manageru a prostředí Azure PowerShell. Prostředky jsou definovány v šabloně Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: tomfitz
ms.openlocfilehash: 714f5f2d4aa1635da8aa62139be5c80e7761ae27
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602886"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu

Tento článek vysvětluje, jak pomocí prostředí Azure PowerShell s Resource Manager šablony nasazení vašich prostředků Azure. Pokud nejste obeznámeni s koncepty nasazení a Správa řešení Azure najdete v části [přehled Azure Resource Manageru](resource-group-overview.md).

Šablony Resource Manageru, který nasazujete, může to být místní soubor na počítači, nebo externí soubor, který je umístěný v úložišti, jako je Githubu. Šablona nasazení v tomto článku je k dispozici v [Ukázka šablony](#sample-template) oddílu, nebo jako [Šablona účtu úložiště na webu GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

V případě potřeby nainstalujte modul Azure PowerShell podle pokynů, které najdete v [průvodci pro Azure PowerShell](/powershell/azure/overview), a pak se připojte k Azure spuštěním příkazu `Connect-AzureRmAccount`.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Nasazení šablony z místního počítače

Při nasazování prostředků do Azure, můžete:

1. Přihlaste se ke svému účtu Azure.
2. Vytvořte skupinu prostředků, která slouží jako kontejner pro nasazené prostředky. Název skupiny prostředků může obsahovat pouze alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může být až 90 znaků. Nemůže končit tečkou.
3. Nasazení do skupiny prostředků definující zdrojů pro vytvoření šablony

Šablonu může obsahovat parametry, které vám umožní přizpůsobit nasazení. Například můžete zadat hodnoty, které jsou přizpůsobené pro konkrétní prostředí (například vývoj, testování a provozním). Ukázka šablony definuje parametr pro účet úložiště SKU.

Následující příklad vytvoří skupinu prostředků a nasadí šablonu z místního počítače:

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Dokončení nasazení může trvat několik minut. Po dokončení zobrazí zprávu, která obsahuje výsledek:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Nasazení šablony z externího zdroje

Místo uložení šablony Resource Manageru na místním počítači, dáváte přednost uložit je do externího umístění. Šablony můžete uložit ve úložiště řízení zdrojů (například Githubu). Nebo byste je uložit v účtu úložiště Azure pro přístup ke sdílenému ve vaší organizaci.

Chcete-li nasadit externí šablonu, použijte **TemplateUri** parametr. Identifikátor URI v příkladu použijte k nasazení ukázkové šablony z Githubu.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

V předchozím příkladu vyžaduje veřejně přístupná identifikátor URI pro šablony, která funguje pro většinu scénářů, protože vaše šablona by neměla zahrnovat citlivá data. Pokud budete muset zadat citlivá data (např. heslo správce), předejte jako parametr zabezpečené tuto hodnotu. Ale pokud nechcete, aby vaše šablona veřejně přístupný, můžete chránit jeho uložením v kontejneru privátní úložiště. Informace o nasazení šablony, která vyžaduje token sdílený přístupový podpis (SAS) najdete v tématu [privátní šablony nasazení s tokenem SAS](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

V prostředí cloudu použijte následující příkazy:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Nasazení na více než jedné skupiny prostředků nebo předplatného

Zpravidla nasazujete, všechny prostředky ve vaší šablony jedna skupina prostředků. Existují však scénáře, ve které chcete nasadit sadu prostředků společně ale umístěte je v různých skupinách prostředků nebo předplatných. Můžete nasadit do pouze pět skupin prostředků v jednom nasazení. Další informace najdete v tématu [prostředky Azure nasazení na více než jedno předplatné nebo skupinu prostředků](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameter-files"></a>Soubory parametrů

Místo předávání parametrů jako vložené hodnoty ve vašem skriptu, možná bude jednodušší použít soubor JSON, který obsahuje hodnoty parametru. Soubor parametrů musí být v následujícím formátu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Všimněte si, že sekci parametrů obsahuje název parametru, který odpovídá parametru definované v šabloně (storageAccountType). Soubor parametrů obsahuje hodnotu pro parametr. Tato hodnota se automaticky předán do šablony během nasazení. Můžete vytvořit několik souborů parametr pro různé scénáře nasazení a pak předejte soubor odpovídající parametr. 

V předchozím příkladu zkopírujte a uložte ho jako soubor s názvem `storage.parameters.json`.

Chcete-li předat soubor místní parametrů, použijte **TemplateParameterFile** parametr:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Chcete-li předat soubor externí parametr, použijte **TemplateParameterUri** parametr:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

Můžete použít vložené parametry a soubor místní parametru v rámci jedné operace nasazení. Můžete například zadat některé hodnoty v souboru místní parametr a přidání dalších hodnot vloženého během nasazení. Když poskytujete hodnoty pro parametr v parametru místního souboru a vložené, hodnota vložené přednost.

Ale při použití souboru externí parametr nemůžete předat další hodnoty buď vložené nebo z místního souboru. Pokud zadáte soubor parametrů v **TemplateParameterUri** parametr, všechny vnořené parametry jsou ignorovány. Zadejte všechny hodnoty parametrů v externím souboru. Pokud vaše šablona obsahuje citlivé hodnotu, která nelze zahrnout do souboru parametrů, přidejte tuto hodnotu do trezoru klíčů, nebo dynamicky poskytovat všechny vnořené hodnoty parametru.

Pokud vaše šablona obsahuje parametr se stejným názvem jako jeden z parametrů v příkazu prostředí PowerShell, prostředí PowerShell nabídne parametru z šablony operátory **FromTemplate**. Například parametr s názvem **ResourceGroupName** na šablony v konfliktu s **ResourceGroupName** parametr ve [New-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) rutiny. Zobrazí se výzva k zadání hodnotu **ResourceGroupNameFromTemplate**. Obecně platí neměli byste toto nedorozuměním není pojmenováním parametry se stejným názvem jako parametry použité pro operace nasazení.

## <a name="test-a-template-deployment"></a>Testovací nasazení šablony

K otestování šablony a parametr hodnoty bez ve skutečnosti nasazení všechny prostředky, použijte [Test-AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Pokud nejsou zjištěny žádné chyby, dokončení příkazu bez odezvy. Pokud dojde k chybě, příkaz vrátí chybovou zprávu. Probíhá pokus o předání nesprávná hodnota pro účet úložiště SKU, například vrátí následující chybu:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Pokud vaše šablona obsahuje chybu syntaxe, příkaz vrátí chybu oznamující, že ho nebylo možné rozložit šablony. Zpráva označuje číslo řádku a pozice chybu analýzy.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Pokud chcete použít dokončení režimu, použijte `Mode` parametr:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>Ukázková šablona

Příklady v tomto článku se používá následující šablony. Zkopírujte a uložte ho jako soubor s názvem storage.json. Chcete-li pochopit, jak k vytvoření této šablony, přečtěte si téma [vytvoření vaší první šablony Azure Resource Manager](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Další postup
* V příkladech v tomto článku nasadit do skupiny prostředků v rámci vašeho předplatného výchozí prostředky. Použijte jiný odběr, najdete v tématu [spravovat víc předplatných Azure](/powershell/azure/manage-subscriptions-azureps).
* Pro dokončení ukázkový skript, který nasadí šablonu, najdete v části [skript nasazení šablony Resource Manageru](resource-manager-samples-powershell-deploy.md).
* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopit strukturu a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Tipy k řešení běžných chyb při nasazení, naleznete v části [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje tokenu SAS naleznete v tématu [privátní šablony nasazení s tokenem SAS](resource-manager-powershell-sas-token.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).

