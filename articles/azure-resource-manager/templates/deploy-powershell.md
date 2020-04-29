---
title: Nasazení prostředků pomocí PowerShellu a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure PowerShell. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e595aa8f86a24e59c8e00d24ea8e9dcb0875a8f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153263"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon ARM a Azure PowerShell

Naučte se používat Azure PowerShell se šablonami Azure Resource Manager (ARM) k nasazení prostředků do Azure. Další informace o konceptech nasazení a správy řešení Azure najdete v tématu [Přehled nasazení šablony](overview.md).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo tenanta. Ve většině případů budete cílit na nasazení do skupiny prostředků. Chcete-li použít zásady a přiřazení rolí v rámci většího rozsahu, použijte předplatné, skupinu pro správu nebo nasazení klientů. Při nasazování do předplatného můžete vytvořit skupinu prostředků a nasadit do ní prostředky.

V závislosti na rozsahu nasazení použijete jiné příkazy.

K nasazení do **skupiny prostředků**použijte [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
```

K nasazení do **předplatného**použijte New-AzSubscriptionDeployment:

```azurepowershell
New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
```

Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

K nasazení do **skupiny pro správu**použijte [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

```azurepowershell
New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
```

Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

K nasazení do **tenanta**použijte [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell
New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
```

Další informace o nasazeních na úrovni tenanta najdete v tématu [vytvoření prostředků na úrovni tenanta](deploy-to-tenant.md).

Příklady v tomto článku používají nasazení skupin prostředků.

## <a name="prerequisites"></a>Požadavky

K nasazení budete potřebovat šablonu. Pokud ho ještě nemáte, Stáhněte si a uložte [ukázkovou šablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z úložiště šablon Azure pro rychlý Start. Místní název souboru, který se používá v tomto článku, je **c:\MyTemplates\azuredeploy.JSON**.

Pokud nepoužíváte Azure Cloud Shell k nasazení šablon, musíte nainstalovat Azure PowerShell a připojit se k Azure:

- **Nainstalujte rutiny Azure PowerShell na místním počítači.** Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).
- **Připojte se k Azure pomocí [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Pokud máte více předplatných Azure, může být nutné spustit také rutinu [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Další informace najdete v tématu [použití více předplatných Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Nasadit místní šablonu

Následující příklad vytvoří skupinu prostředků a nasadí šablonu z místního počítače. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Nemůže končit tečkou.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Dokončení nasazení může trvat několik minut.

## <a name="deploy-remote-template"></a>Nasadit vzdálenou šablonu

Místo uložení šablon ARM na místní počítač můžete chtít ukládat je do externího umístění. Šablony můžete ukládat do úložiště správy zdrojového kódu (například GitHub). Nebo je můžete uložit do účtu služby Azure Storage pro sdílený přístup ve vaší organizaci.

Pokud chcete nasadit externí šablonu, použijte parametr **TemplateUri** . Pomocí identifikátoru URI v příkladu nasaďte ukázkovou šablonu z GitHubu.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Předchozí příklad vyžaduje pro šablonu veřejně přístupný identifikátor URI, který funguje ve většině scénářů, protože by šablona neměla obsahovat citlivá data. Pokud potřebujete zadat citlivá data (třeba heslo správce), předejte tuto hodnotu jako zabezpečený parametr. Pokud ale nechcete, aby byla šablona veřejně přístupná, můžete ji chránit uložením do privátního kontejneru úložiště. Informace o nasazení šablony, která vyžaduje token sdíleného přístupového podpisu (SAS), najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md). Kurz najdete v tématu [kurz: integrace Azure Key Vault v nasazení šablony ARM](template-tutorial-use-key-vault.md).

## <a name="deploy-from-azure-cloud-shell"></a>Nasazení z Azure Cloud Shell

K nasazení šablony můžete použít [Azure Cloud Shell](https://shell.azure.com) . Chcete-li nasadit externí šablonu, zadejte identifikátor URI šablony. Pokud chcete nasadit místní šablonu, musíte nejdřív načíst šablonu do účtu úložiště pro vaši Cloud Shell. Chcete-li odeslat soubory do prostředí, vyberte v okně prostředí ikonu nabídky **nahrát/stáhnout soubory** .

Cloud Shell otevřete tak, že přejdete [https://shell.azure.com](https://shell.azure.com)na nebo vyberete příkaz **try-it** z následujícího oddílu kódu:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Pokud chcete vložit kód do prostředí, klikněte pravým tlačítkem myši do prostředí a pak vyberte **Vložit**.

## <a name="pass-parameter-values"></a>Předat hodnoty parametrů

K předání hodnot parametrů můžete použít buď vložené parametry, nebo soubor parametrů.

### <a name="inline-parameters"></a>Vložené parametry

Chcete-li předat vložené parametry, zadejte název parametru pomocí `New-AzResourceGroupDeployment` příkazu. Například pro předání řetězce a pole do šablony použijte:

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString "inline string" `
  -exampleArray $arrayParam
```

Obsah souboru můžete také získat a poskytnout ho jako vložený parametr.

```powershell
$arrayParam = "value1", "value2"
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleString $(Get-Content -Path c:\MyTemplates\stringcontent.txt -Raw) `
  -exampleArray $arrayParam
```

Získání hodnoty parametru ze souboru je užitečné v případě, že potřebujete zadat konfigurační hodnoty. Můžete například zadat [hodnoty Cloud-init pro virtuální počítač se systémem Linux](../../virtual-machines/linux/using-cloud-init.md).

Pokud potřebujete předat pole objektů, vytvořte v PowerShellu zatřiďovací tabulky a přidejte je do pole. Předat toto pole jako parametr během nasazování.

```powershell
$hash1 = @{ Name = "firstSubnet"; AddressPrefix = "10.0.0.0/24"}
$hash2 = @{ Name = "secondSubnet"; AddressPrefix = "10.0.1.0/24"}
$subnetArray = $hash1, $hash2
New-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\demotemplate.json `
  -exampleArray $subnetArray
```

### <a name="parameter-files"></a>Soubory parametrů

Místo předání parametrů jako vložených hodnot do skriptu může být snazší použít soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů může být místní soubor nebo externí soubor s přístupným identifikátorem URI.

Další informace o souboru parametrů naleznete v tématu [Create správce prostředků Parameter File](parameter-files.md).

Chcete-li předat místní soubor parametrů, použijte parametr **TemplateParameterFile** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Chcete-li předat externí soubor parametrů, použijte parametr **TemplateParameterUri** :

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="test-template-deployments"></a>Nasazení šablon testů

K otestování svých šablon a hodnot parametrů bez skutečného nasazení jakýchkoli prostředků použijte [test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment). 

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType Standard_GRS
```

Pokud se nezjistí žádné chyby, příkaz se dokončí bez odpovědi. Pokud se zjistí chyba, vrátí příkaz chybovou zprávu. Například předáním nesprávné hodnoty SKU účtu úložiště vrátí následující chybu:

```powershell
Test-AzResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Pokud má vaše šablona syntaktickou chybu, příkaz vrátí chybu oznamující, že nešlo analyzovat šablonu. Zpráva indikuje číslo řádku a umístění chyby analýzy.

```powershell
Test-AzResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

## <a name="next-steps"></a>Další kroky

- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Informace o definování parametrů v šabloně najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
- Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
