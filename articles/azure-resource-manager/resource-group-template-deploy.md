---
title: Nasazení prostředků pomocí Powershellu a šablony | Dokumentace Microsoftu
description: Nasazení prostředků do Azure pomocí Azure Resource Manageru a Azure Powershellu. Prostředky jsou definovány v šabloně Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2018
ms.author: tomfitz
ms.openlocfilehash: 6e6cd7f1677d8ae11f05c2a2bca4233603a29408
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2019
ms.locfileid: "55075665"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu

Tento článek vysvětluje, jak nasadit prostředky do Azure pomocí šablon Resource Manageru pomocí prostředí Azure PowerShell. Pokud nejsou dobře známé koncepty nasazení a správou řešení Azure, najdete v článku [přehled Azure Resource Manageru](resource-group-overview.md).

Šablony Resource Manageru, které nasadíte, může být místní soubor na svůj počítač nebo externí soubor, který se nachází v úložišti, jako je GitHub. Šablona nasazení v tomto článku je k dispozici jako [Šablona účtu úložiště v Githubu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

V případě potřeby nainstalujte modul Azure PowerShell podle pokynů, které najdete v [průvodci pro Azure PowerShell](/powershell/azure/overview), a pak se připojte k Azure spuštěním příkazu `Connect-AzAccount`.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Nasazení šablony z místního počítače

Při nasazování prostředků do Azure, můžete:

1. Přihlášení k účtu Azure
2. Vytvořte skupinu prostředků, která slouží jako kontejner pro nasazených prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může být až 90 znaků. Nesmí končit tečkou.
3. Nasazení do skupiny prostředků, který definuje prostředky k vytvoření šablony

Šablona může obsahovat parametry, které vám umožní přizpůsobit nasazení. Například můžete zadat hodnoty, které jsou přizpůsobené pro konkrétní prostředí (jako je vývoj, testování a produkce). Ukázková šablona definuje parametr pro SKU účtu úložiště.

Následující příklad vytvoří skupinu prostředků a nasadí šablony z místního počítače:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zprávu, která obsahuje výsledek:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Nasazení šablony z externího zdroje

Místo uložení šablony Resource Manageru na místním počítači, můžete chtít uložit je do externího umístění. Šablony můžete uložit úložiště správy zdrojového kódu (např. GitHub). Nebo můžete je ukládat v účtu úložiště Azure pro zajištění sdíleného přístupu ve vaší organizaci.

Chcete-li nasadit externí šablony, použijte **TemplateUri** parametru. Použijte identifikátor URI v příkladu nasazení ukázkové šablony z Githubu.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

V předchozím příkladu vyžaduje veřejně přístupné identifikátor URI pro šablony, které lze použít pro většinu scénářů, protože šablony by neměl obsahovat citlivá data. Pokud je třeba zadat citlivá data (jako je zadání hesla správce), předáte tuto hodnotu jako zabezpečený parametr. Ale pokud nechcete, aby vaše šablona veřejně přístupný, budete ho chránit ukládáním do privátního úložiště kontejnerů. Informace o nasazení šablony, která se vyžaduje token sdíleného přístupového podpisu (SAS), najdete v části [nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Ve službě Cloud Shell použijte následující příkazy:

```powershell
New-AzResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Nasazení do více než jedné skupiny prostředků nebo předplatného

Zpravidla nasazujete, všechny prostředky ve vaší šabloně do jedné skupiny prostředků. Existují ale scénáře, ve které chcete nasadit sadu prostředků společně, ale je umístit do různých skupin prostředků nebo předplatných. Můžete nasadit do pouze pět skupin prostředků v jednom nasazení. Další informace najdete v tématu [Azure nasadit prostředky do více než jedno předplatné nebo skupinu prostředků](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameters"></a>Parametry

Účelem předání hodnot parametrů, můžete použít parametry vložené nebo soubor parametru. Předchozí příklady v tomto článku ukazují vložených parametrů.

### <a name="inline-parameters"></a>Vložené parametry

Pro předání parametrů vložený, zadejte názvy parametrů s `New-AzResourceGroupDeployment` příkazu. Například předávání řetězce a pole do šablony, použijte:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Můžete také získat obsah souboru a poskytují tento obsah jako parametr vložené.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Získávání hodnotu parametru ze souboru je užitečné, když je potřeba zadat hodnoty konfigurace. Například můžete zadat [cloud-init hodnoty pro virtuální počítač s Linuxem](../virtual-machines/linux/using-cloud-init.md).

### <a name="parameter-files"></a>Soubory parametrů

Místo předání parametrů jako hodnoty vložená ve skriptu, možná bude snadněji používá soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů může být místní soubor nebo externí soubor s identifikátorem URI přístupné.

Soubor parametrů musí být v následujícím formátu:

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

Všimněte si, že sekci parametrů obsahuje název parametru, který odpovídá parametru definovaného v šabloně (storageAccountType). Soubor parametrů obsahuje hodnotu pro parametr. Tato hodnota je automaticky předávaných do šablony během nasazení. Můžete vytvořit více než jeden soubor s parametry a poté předejte soubor odpovídající parametr pro scénář. 

Předchozí příklad zkopírujte a uložte ho jako soubor s názvem `storage.parameters.json`.

Chcete-li předat parametr místní soubor, použijte **TemplateParameterFile** parametr:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Chcete-li předat soubor externí parametrů, použijte **TemplateParameterUri** parametr:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

### <a name="parameter-precedence"></a>Parametr prioritu

Můžete použít parametry vložené a místní parametr souboru v rámci jedné operace nasazení. Můžete například zadat některé hodnoty v souboru parametrů místní a přidat další hodnoty vložený během nasazení. Pokud zadáte hodnoty pro parametr v parametru místního souboru a vložené, hodnota vložené přednost.

Ale při použití souboru externí parametr nemůžete předat další hodnoty buď jako vložené nebo z místního souboru. Pokud zadáte soubor parametrů v **TemplateParameterUri** parametr, parametry budou ignorovány všechny vložené. Zadejte všechny hodnoty parametrů v externím souboru. Pokud šablona obsahuje citlivé hodnotu, která není možné zahrnout v souboru parametrů, přidejte tuto hodnotu do trezoru klíčů, nebo dynamicky poskytovat všechny vložené hodnoty parametru.

### <a name="parameter-name-conflicts"></a>Parametr název je v konfliktu

Pokud šablona obsahuje parametr se stejným názvem jako jeden z parametrů v příkazu Powershellu, prostředí PowerShell nabídne parametr z šablony Příponové **FromTemplate**. Například parametr s názvem **ResourceGroupName** ve vaší šabloně je v konfliktu s **ResourceGroupName** parametr [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) rutiny. Zobrazí výzva k zadání hodnoty pro **ResourceGroupNameFromTemplate**. Obecně byste se měli vyhnout této záměně není pojmenováním parametry se stejným názvem jako parametrů použitých pro operace nasazení.

## <a name="test-a-template-deployment"></a>Test šablony nasazení

Chcete-li otestovat šablonu a parametry hodnoty bez skutečného nasazení všechny prostředky, použijte [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Pokud nejsou zjištěny žádné chyby, dokončení příkazu bez odezvy. Pokud dojde k chybě, příkaz vrátí chybovou zprávu. Nesprávná hodnota pro účet úložiště SKU, například vrátí následující chybu:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Pokud vaše šablona obsahuje chybu syntaxe, příkaz vrátí chybu s informacemi, že ho nebylo možné rozložit šablony. Zpráva číslo řádku a pozice chybu analýzy.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Další postup
* V příkladech v tomto článku nasazení prostředků do skupiny prostředků ve vašem výchozím předplatném. Pokud chcete použít jiné předplatné, naleznete v tématu [Správa několika předplatných Azure](/powershell/azure/manage-subscriptions-azureps).
* Chcete-li určit způsob zpracování prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manageru](deployment-modes.md).
* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Tipy pro řešení běžných chyb při nasazení, najdete v části [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která se vyžaduje SAS token najdete v tématu [nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md).
* Bezpečně zavést vaše služba pro více než jedné oblasti, naleznete v tématu [Azure Deployment Manager](deployment-manager-overview.md).
