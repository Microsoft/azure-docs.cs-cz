---
title: Nasazení prostředků pomocí Powershellu a šablony | Dokumentace Microsoftu
description: Nasazení prostředků do Azure pomocí Azure Resource Manageru a Azure Powershellu. Prostředky jsou definovány v šabloně Resource Manageru.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tomfitz
ms.openlocfilehash: 63d729f19b0ef20d0e7a716d6857b4627095856b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476979"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu

Zjistěte, jak nasadit prostředky do Azure pomocí šablon Resource Manageru pomocí prostředí Azure PowerShell. Další informace o konceptech, nasazení a správě řešení Azure najdete v tématu [přehled Azure Resource Manageru](resource-group-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deployment-scope"></a>Rozsah nasazení

Můžete cílit na vaše nasazení předplatného Azure nebo skupinu prostředků v rámci předplatného. Ve většině případů cílíte nasazení do skupiny prostředků. Použití předplatného nasazení použít zásady a přiřazení rolí v rámci předplatného. Vytvořte skupinu prostředků a nasazení prostředků do ní také použijete nasazení předplatných. V závislosti na rozsahu nasazení můžete použít různé příkazy.

Nasazení do **skupiny prostředků**, použijte [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Nasazení do **předplatné**, použijte [New-AzDeployment](/powershell/module/az.resources/new-azdeployment):

```azurepowershell
New-AzDeployment -Location <location> -TemplateFile <path-to-template>
```

Nasazení skupiny pro správu se v současné době podporují jenom přes rozhraní REST API. Zobrazit [nasazení prostředků pomocí šablon Resource Manageru a rozhraní REST API Resource Manageru](resource-group-template-deploy-rest.md).

V příkladech v tomto článku se používá nasazení skupiny prostředků. Další informace o nasazení předplatných najdete v tématu [vytvoření skupiny prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

## <a name="prerequisites"></a>Požadavky

Budete potřebovat k nasazení šablony. Pokud již nemáte, stáhněte a uložte [Ukázková šablona](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z úložiště šablon Quickstart pro Azure. Název místního souboru použitý v tomto článku je **c:\MyTemplates\azuredeploy.json**.

Pokud používáte Azure Cloud shell k nasazení šablony, budete muset nainstalovat Azure PowerShell a připojte se k Azure:

- **Instalace rutin Powershellu pro Azure v místním počítači.** Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).
- **Připojení k Azure s využitím [připojit AZAccount](/powershell/module/az.accounts/connect-azaccount)** . Pokud máte více předplatných Azure, může být také potřeba spustit [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Další informace najdete v tématu [použít několik předplatných Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Nasazení místního šablony

Následující příklad vytvoří skupinu prostředků a nasadí šablony z místního počítače. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může být až 90 znaků. Nesmí končit tečkou.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Dokončení nasazení může trvat několik minut.

## <a name="deploy-remote-template"></a>Nasazení vzdálené šablony

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

## <a name="deploy-from-azure-cloud-shell"></a>Nasazení ze služby Azure Cloud shell

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

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při nasazení se nezdaří

Tato funkce se také označuje jako *vrácení zpět při chybě*. Pokud se nasazení nezdaří, můžete automaticky znovu nasadit starší a úspěšné nasazení z historie nasazení. Pokud chcete nastavit opětovné nasazení, použijte buď `-RollbackToLastDeployment` nebo `-RollBackDeploymentName` parametr v příkazu pro nasazení. Tato funkce je užitečná, pokud máte známého funkčního stavu pro nasazení infrastruktury a chcete se vrátit do tohoto stavu. Existuje několik omezení a omezení:

- Opětovné nasazení se spustí, přesně tak, jak byl dříve spuštěn se stejnými parametry. Nelze změnit parametry.
- Předchozí nasazení se spustí pomocí [úplný režim](./deployment-modes.md#complete-mode). Se odstraní všechny prostředky, které nejsou zahrnuty v předchozím nasazení a konfigurace všech prostředků jsou nastaveny do jejich předchozího stavu. Ujistěte se, že plně chápete [režimy nasazení](./deployment-modes.md).
- Opětovné nasazení má vliv pouze prostředky, všechny změny dat to nebude mít vliv.
- Tato funkce je podporována pouze na nasazení skupiny prostředků, ne předplatné úrovně nasazení. Další informace o nasazení na úrovni předplatného najdete v tématu [vytvoření skupiny prostředků a prostředků na úrovni předplatného](./deploy-to-subscription.md).

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

Pokud je potřeba předat pole objektů, vytvoření tabulky hash v Powershellu a přidat je do pole. Toto pole můžete předáte jako parametr během nasazení.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```


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
