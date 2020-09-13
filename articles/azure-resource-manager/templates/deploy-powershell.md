---
title: Nasazení prostředků pomocí PowerShellu a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure PowerShell. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: ef2ff71430f0dcaca660666bb9a6c015c923da3f
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536068"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon ARM a Azure PowerShell

Tento článek vysvětluje, jak používat Azure PowerShell se šablonami Azure Resource Manager (šablony ARM) k nasazení vašich prostředků do Azure. Pokud nejste obeznámeni s koncepty nasazení a správy řešení Azure, přečtěte si téma [Přehled nasazení šablony](overview.md).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo tenanta. Ve většině případů budete cílit na nasazení do skupiny prostředků. Chcete-li použít zásady a přiřazení rolí v rámci většího rozsahu, použijte předplatné, skupinu pro správu nebo nasazení klientů. Při nasazování do předplatného můžete vytvořit skupinu prostředků a nasadit do ní prostředky.

V závislosti na rozsahu nasazení použijete jiné příkazy.

* K nasazení do **skupiny prostředků**použijte [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

* K nasazení do **předplatného**použijte New-AzSubscriptionDeployment:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

* K nasazení do **skupiny pro správu**použijte [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

* K nasazení do **tenanta**použijte [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Další informace o nasazeních na úrovni tenanta najdete v tématu [vytvoření prostředků na úrovni tenanta](deploy-to-tenant.md).

Příklady v tomto článku používají nasazení skupin prostředků.

## <a name="prerequisites"></a>Požadavky

K nasazení budete potřebovat šablonu. Pokud ho ještě nemáte, Stáhněte si a uložte [ukázkovou šablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z úložiště šablon Azure pro rychlý Start. Místní název souboru použitý v tomto článku je **c:\MyTemplates\azuredeploy.js**.

Pokud nepoužíváte Azure Cloud Shell k nasazení šablon, musíte nainstalovat Azure PowerShell a připojit se k Azure:

- **Nainstalujte rutiny Azure PowerShell na místním počítači.** Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).
- **Připojte se k Azure pomocí [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Pokud máte více předplatných Azure, může být nutné spustit také rutinu [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Další informace najdete v tématu [použití více předplatných Azure](/powershell/azure/manage-subscriptions-azureps).

## <a name="deploy-local-template"></a>Nasadit místní šablonu

Následující příklad vytvoří skupinu prostředků a nasadí šablonu z místního počítače. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Nemůže končit tečkou.

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name ExampleDeployment `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Dokončení nasazení může trvat několik minut.

## <a name="deployment-name"></a>Název nasazení

V předchozím příkladu jste najmenovali nasazení `ExampleDeployment` . Pokud název nasazení nezadáte, použije se název souboru šablony. Například pokud nasadíte šablonu s názvem `azuredeploy.json` a nezadáte název nasazení, nasazení se pojmenuje `azuredeploy` .

Pokaždé, když spustíte nasazení, do historie nasazení skupiny prostředků se přidá záznam s názvem nasazení. Pokud spustíte jiné nasazení a přiřadíte mu stejný název, bude předchozí položka nahrazena aktuálním nasazením. Pokud chcete zachovat jedinečné položky v historii nasazení, udělte každému nasazení jedinečný název.

Chcete-li vytvořit jedinečný název, můžete přiřadit náhodné číslo.

```azurepowershell-interactive
$suffix = Get-Random -Maximum 1000
$deploymentName = "ExampleDeployment" + $suffix
```

Nebo přidejte hodnotu data.

```azurepowershell-interactive
$today=Get-Date -Format "MM-dd-yyyy"
$deploymentName="ExampleDeployment"+"$today"
```

Pokud spustíte souběžná nasazení do stejné skupiny prostředků se stejným názvem nasazení, bude dokončeno pouze poslední nasazení. Všechna nasazení se stejným názvem, která nebyla dokončena, budou nahrazena posledním nasazením. Pokud například spustíte nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage1` a zároveň spustíte jiné nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage2` , nasadíte jenom jeden účet úložiště. Výsledný účet úložiště je pojmenován `storage2` .

Pokud ale spustíte nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage1` , a hned po jeho dokončení spustíte jiné nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage2` , budete mít dva účty úložiště. Jedna má název `storage1` a druhá má název `storage2` . Ale v historii nasazení máte jenom jednu položku.

Pokud pro každé nasazení zadáte jedinečný název, můžete je spustit souběžně bez konfliktu. Pokud spustíte nasazení s názvem, `newStorage1` které nasadí účet úložiště s názvem `storage1` a zároveň spustíte jiné nasazení s názvem, `newStorage2` které nasadí účet úložiště s názvem `storage2` , budete mít dva účty úložiště a dvě položky v historii nasazení.

Aby nedocházelo ke konfliktům s souběžnými nasazeními a zajistili v historii nasazení jedinečné položky, udělte každé nasazení jedinečný název.

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

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

Místo nasazení místní nebo vzdálené šablony můžete vytvořit [specifikaci šablony](template-specs.md). Specifikace šablony je prostředek ve vašem předplatném Azure, který obsahuje šablonu ARM. Usnadňuje bezpečné sdílení šablony s uživateli ve vaší organizaci. Řízení přístupu na základě role (RBAC) slouží k udělení přístupu ke specifikaci šablony. Tato funkce je aktuálně ve verzi Preview.

Následující příklady ukazují, jak vytvořit a nasadit specifikace šablony. Tyto příkazy jsou k dispozici pouze v případě, že jste se [zaregistrovali ve verzi Preview](https://aka.ms/templateSpecOnboarding).

Nejprve vytvoříte specifikaci šablony zadáním šablony ARM.

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -Location westus2 -TemplateJsonFile ./mainTemplate.json
```

Potom získáte ID pro specifikaci šablony a nasadíte ji.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Další informace najdete v tématu [Azure Resource Manager specifikace šablon (Preview)](template-specs.md).

## <a name="preview-changes"></a>Zobrazit náhled změn

Před nasazením šablony můžete zobrazit náhled změn, které šablona provede pro vaše prostředí. Pomocí [operace citlivosti](template-deploy-what-if.md) ověřte, že šablona provádí očekávané změny. Co když zároveň ověří chyby v šabloně.

## <a name="deploy-from-azure-cloud-shell"></a>Nasazení z Azure Cloud Shell

K nasazení šablony můžete použít [Azure Cloud Shell](https://shell.azure.com) . Chcete-li nasadit externí šablonu, zadejte identifikátor URI šablony. Pokud chcete nasadit místní šablonu, musíte nejdřív načíst šablonu do účtu úložiště pro vaši Cloud Shell. Chcete-li odeslat soubory do prostředí, vyberte v okně prostředí ikonu nabídky **nahrát/stáhnout soubory** .

Chcete-li otevřít Cloud Shell, přejděte na adresu [https://shell.azure.com](https://shell.azure.com) nebo vyberte příkaz **try-it** z následujícího oddílu kódu:

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

## <a name="next-steps"></a>Další kroky

- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Informace o definování parametrů v šabloně najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
- Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
