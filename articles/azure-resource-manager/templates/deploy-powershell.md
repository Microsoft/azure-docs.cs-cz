---
title: Nasazení prostředků pomocí PowerShellu a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure PowerShell. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153263"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon ARM a Azure PowerShellu

Zjistěte, jak používat Azure PowerShell se šablonami Azure Resource Manager (ARM) k nasazení prostředků do Azure. Další informace o konceptech nasazení a správy řešení Azure najdete v [tématu přehled nasazení šablon](overview.md).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo klienta. Ve většině případů se zaměříte na nasazení do skupiny prostředků. Chcete-li použít zásady a přiřazení rolí napříč větším oborem, použijte předplatné, skupinu pro správu nebo nasazení tenanta. Při nasazování do předplatného můžete vytvořit skupinu prostředků a nasadit do ní prostředky.

V závislosti na rozsahu nasazení můžete použít různé příkazy.

Chcete-li nasadit do **skupiny prostředků**, použijte [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

Chcete-li nasadit do **předplatného**, použijte New-AzSubscriptionDeployment:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Další informace o nasazení na úrovni předplatného naleznete v [tématu Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

Chcete-li nasadit do **skupiny pro správu**, použijte [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Další informace o nasazení na úrovni skupiny pro správu naleznete [v tématu Vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

Chcete-li nasadit do **klienta**, použijte [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Další informace o nasazení na úrovni klienta najdete v tématu [Vytváření prostředků na úrovni klienta](deploy-to-tenant.md).

Příklady v tomto článku používají nasazení skupiny prostředků.

## <a name="prerequisites"></a>Požadavky

Potřebujete šablonu k nasazení. Pokud ještě nemáte, stáhněte a uložte [ukázkovou šablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z repo šablon Azure QuickStart. Místní název souboru použitý v tomto článku je **c:\MyTemplates\azuredeploy.json**.

Pokud k nasazení šablon nepoužijete Azure Cloud Shell, musíte nainstalovat Azure PowerShell a připojit se k Azure:

- **Nainstalujte rutiny Prostředí Azure PowerShell do místního počítače.** Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).
- **Připojte se k Azure pomocí [connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Pokud máte více předplatných Azure, možná budete muset spustit [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Další informace najdete v tématu [Použití více předplatných Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Nasazení místní šablony

Následující příklad vytvoří skupinu prostředků a nasadí šablonu z místního počítače. Název skupiny prostředků může obsahovat pouze alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může to být až 90 znaků. Nemůže to skončit v určité době.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Dokončení nasazení může trvat několik minut.

## <a name="deploy-remote-template"></a>Nasazení vzdálené šablony

Místo ukládání arm šablony na místním počítači, můžete raději uložit do externího umístění. Šablony můžete ukládat do úložiště správy zdrojového kódu (například GitHub). Nebo je můžete uložit do účtu úložiště Azure pro sdílený přístup ve vaší organizaci.

Chcete-li nasadit externí šablonu, použijte parametr **TemplateUri.** Použití IDENTIFIKÁTORU URI v příkladu k nasazení ukázkové šablony z GitHubu.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Předchozí příklad vyžaduje veřejně přístupný identifikátor URI pro šablonu, který funguje pro většinu scénářů, protože šablona by neměla obsahovat citlivá data. Pokud potřebujete zadat citlivá data (například heslo správce), předajte tuto hodnotu jako zabezpečený parametr. Pokud však nechcete, aby byla šablona veřejně přístupná, můžete ji chránit uložením do kontejneru soukromého úložiště. Informace o nasazení šablony, která vyžaduje token sdíleného přístupového podpisu (SAS), naleznete [v tématu Nasazení soukromé šablony pomocí tokenu SAS](secure-template-with-sas-token.md). Chcete-li projít kurz, [najdete v tématu Kurz: Integrace trezoru klíčů Azure v nasazení šablony ARM](template-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Nasazení z Cloud shellu Azure

Azure Cloud [Shell](https://shell.azure.com) můžete použít k nasazení šablony. Chcete-li nasadit externí šablonu, zadejte identifikátor URI šablony. Chcete-li nasadit místní šablonu, musíte nejprve načíst šablonu do účtu úložiště pro cloudové prostředí. Chcete-li nahrát soubory do prostředí, vyberte ikonu nabídky **Nahrát/Stáhnout soubory** z okna prostředí.

Chcete-li otevřít prostředí [https://shell.azure.com](https://shell.azure.com)Cloud, přejděte na položku nebo vyberte **try-it** z následující části kódu:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Chcete-li kód vložit do prostředí, klepněte pravým tlačítkem myši do prostředí a pak vyberte **Vložit**.

## <a name="pass-parameter-values"></a>Předat hodnoty parametrů

Chcete-li předat hodnoty parametrů, můžete použít buď vložky, nebo soubor parametrů.

### <a name="inline-parameters"></a>Vsazené parametry

Chcete-li předat vřadné parametry, zadejte názvy parametru s příkazem. `New-AzResourceGroupDeployment` Chcete-li například předat řetězec a pole šabloně, použijte:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Můžete také získat obsah souboru a poskytnout tento obsah jako vsazený parametr.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Získání hodnoty parametru ze souboru je užitečné, když potřebujete zadat hodnoty konfigurace. Můžete například zadat [hodnoty cloud-init pro virtuální počítač S IP](../../virtual-machines/linux/using-cloud-init.md).

Pokud potřebujete předat v poli objektů, vytvořte tabulky hash v prostředí PowerShell a přidejte je do pole. Předajto pole jako parametr během nasazení.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Soubory parametrů

Spíše než předávání parametrů jako vložkové hodnoty ve skriptu, může být jednodušší použít soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů může být místní soubor nebo externí soubor s přístupným identifikátorem URI.

Další informace o souboru parametrů naleznete v tématu [Vytvoření souboru parametrů Správce prostředků](parameter-files.md).

Chcete-li předat soubor místního parametru, použijte parametr **TemplateParameterFile:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Chcete-li předat soubor externích parametrů, použijte parametr **TemplateParameterUri:**

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Testovací nasazení šablon

Chcete-li otestovat hodnoty šablony a parametrů bez skutečného nasazení prostředků, použijte [test-azResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Pokud nejsou zjištěny žádné chyby, příkaz skončí bez odpovědi. Pokud je zjištěna chyba, příkaz vrátí chybovou zprávu. Například předání nesprávné hodnoty pro skladovou položku účtu úložiště vrátí následující chybu:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Pokud má šablona chybu syntaxe, příkaz vrátí chybu označující, že nemohla šablonu analyzovat. Zpráva označuje číslo řádku a umístění chyby analýzy.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Další kroky

- Pokud chcete vrátit se k úspěšnému nasazení, když se zobrazí chyba, přečtěte si informace [o chybě vrácení zpět k úspěšnému nasazení](rollback-on-error.md).
- Pokud chcete určit, jak zpracovat prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, najdete v [tématu režimy nasazení Správce prostředků Azure](deployment-modes.md).
- Informace o tom, jak definovat parametry v šabloně, naleznete [v tématu Principy struktury a syntaxe šablon ARM](template-syntax.md).
- Informace o nasazení šablony, která vyžaduje token SAS, naleznete v [tématu Nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
