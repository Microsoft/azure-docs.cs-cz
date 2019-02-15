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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 18dc82880830b6f8d14a7fc01930f75e9e61e5b0
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300546"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu

Zjistěte, jak nasadit prostředky do Azure pomocí šablon Resource Manageru pomocí prostředí Azure PowerShell. Další informace o konceptech, nasazení a správě řešení Azure najdete v tématu [přehled Azure Resource Manageru](resource-group-overview.md).

Pokud chcete nasadit šablonu, je obvykle třeba dva kroky:

1. Vytvořte skupinu prostředků. Skupina prostředků slouží jako kontejner pro nasazených prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může být až 90 znaků. Nesmí končit tečkou.
2. Nasazení šablony. Šablona definuje prostředky vytvořit.  Nasazení vytvoří ve skupině prostředků zadané prostředky.

V tomto článku se používá tato metoda nasazení ve dvou krocích.  Další možností je nasazení skupiny prostředků a prostředky ve stejnou dobu.  Další informace najdete v tématu [vytvořte skupinu prostředků a nasazení prostředků](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="prerequisites"></a>Požadavky

Pokud nechcete použít [Azure Cloud shell](#deploy-templates-from-azure-cloud-shell) k nasazení šablony, budete muset nainstalovat Azure PowerShell a připojte se k Azure:
- **Instalace rutin Powershellu pro Azure v místním počítači.** Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).
- **Připojení k Azure s využitím [připojit AZAccount](/powershell/module/az.accounts/connect-azaccount.md)**. Pokud máte více předplatných Azure, může být také potřeba spustit [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext.md). Další informace najdete v tématu [použít několik předplatných Azure](/powershell/azure/manage-subscriptions-azureps).
- * Stáhněte a uložte [šablonu pro rychlý Start](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) . Název místního souboru použitý v tomto článku je **c:\MyTemplates\azuredeploy.json**.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-templates-stored-locally"></a>Nasazení šablony uložené místně

Následující příklad vytvoří skupinu prostředků a nasadí šablony z místního počítače:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Poznámka: *c:\MyTemplates\azuredeploy.json* je šablonu pro rychlý start.  Viz [Požadavky](#prerequisites).

Dokončení nasazení může trvat několik minut.

## <a name="deploy-templates-stored-externally"></a>Nasazení šablon uložených externě

Místo uložení šablony Resource Manageru na místním počítači, můžete chtít uložit je do externího umístění. Šablony můžete uložit úložiště správy zdrojového kódu (např. GitHub). Nebo můžete je ukládat v účtu úložiště Azure pro zajištění sdíleného přístupu ve vaší organizaci.

Chcete-li nasadit externí šablony, použijte **TemplateUri** parametru. Použijte identifikátor URI v příkladu nasazení ukázkové šablony z Githubu.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

V předchozím příkladu vyžaduje veřejně přístupné identifikátor URI pro šablony, které lze použít pro většinu scénářů, protože šablony by neměl obsahovat citlivá data. Pokud je třeba zadat citlivá data (jako je zadání hesla správce), předáte tuto hodnotu jako zabezpečený parametr. Ale pokud nechcete, aby vaše šablona veřejně přístupný, budete ho chránit ukládáním do privátního úložiště kontejnerů. Informace o nasazení šablony, která se vyžaduje token sdíleného přístupového podpisu (SAS), najdete v části [nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md). Absolvovat kurz, naleznete v tématu [kurzu: Integrace Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md).

## <a name="deploy-templates-from-azure-cloud-shell"></a>Nasazení šablon ze služby Azure Cloud shell

Můžete použít [Azure Cloud Shell](https://shell.azure.com) k nasazení vaší šablony. Pokud chcete nasadit šablonu externí, zadejte identifikátor URI šablony. Pokud chcete nasadit šablonu místní, musí nejdřív načíst šablony do účtu úložiště pro službu Cloud Shell. Postup nahrání souborů do prostředí, vyberte **nahrávání a stahování souborů** ikonu nabídky v okně prostředí.

Otevřete Cloud shell, přejděte do [ https://shell.azure.com ](https://shell.azure.com), nebo vyberte **Try It** ze sekce kódu následujícího:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Vložte kód do prostředí, klikněte pravým tlačítkem uvnitř prostředí a potom vyberte **vložte**.

## <a name="deploy-to-multiple-resource-groups-or-subscriptions"></a>Nasazení do několika skupin prostředků nebo předplatných

Zpravidla nasazujete, všechny prostředky ve vaší šabloně do jedné skupiny prostředků. Existují ale scénáře, ve které chcete nasadit sadu prostředků společně, ale je umístit do různých skupin prostředků nebo předplatných. Můžete nasadit do pouze pět skupin prostředků v jednom nasazení. Další informace najdete v tématu [Azure nasadit prostředky do více skupin prostředků a předplatná](resource-manager-cross-resource-group-deployment.md).

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při nasazení se nezdaří

Pokud se nasazení nezdaří, můžete automaticky znovu nasadit starší a úspěšné nasazení z historie nasazení. Pokud chcete nastavit opětovné nasazení, použijte buď `-RollbackToLastDeployment` nebo `-RollBackDeploymentName` parametr v příkazu pro nasazení.

Tato možnost dala použít, vaše nasazení musí mít jedinečné názvy, tak je možné identifikovat v historii. Pokud nemáte jedinečné názvy, aktuální selhání nasazení může přepsat předchozí úspěšné nasazení v historii. Tuto možnost můžete použít pouze u kořenové úrovně nasazení. Nasazení z vnořené šablony nejsou k dispozici pro nové nasazení.

K opětovnému nasazení poslední úspěšné nasazení, přidejte `-RollbackToLastDeployment` parametr jako příznak.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Chcete-li znovu nasadit konkrétní nasazení, použijte `-RollBackDeploymentName` parametr a zadejte název nasazení.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

Zadané nasazení musí mít bylo úspěšné.

## <a name="pass-parameter-values"></a>Předání hodnot parametrů

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
  -TemplateFile c:\MyTemplates\azuredeploy.json `
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

## <a name="test-template-deployments"></a>Testovací šablony nasazení

Chcete-li otestovat šablonu a parametry hodnoty bez skutečného nasazení všechny prostředky, použijte [Test-AzureRmResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Pokud nejsou zjištěny žádné chyby, dokončení příkazu bez odezvy. Pokud dojde k chybě, příkaz vrátí chybovou zprávu. Nesprávná hodnota pro účet úložiště SKU, například vrátí následující chybu:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

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

- Bezpečně zavést vaše služba pro více než jedné oblasti, naleznete v tématu [Azure Deployment Manager](deployment-manager-overview.md).
- Chcete-li určit způsob zpracování prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manageru](deployment-modes.md).
- Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).
- Informace o nasazení šablony, která se vyžaduje SAS token najdete v tématu [nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md).
