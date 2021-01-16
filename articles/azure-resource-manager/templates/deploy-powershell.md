---
title: Nasazení prostředků pomocí PowerShellu a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure PowerShell. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d895c6e029b0b4a70333dde987706549609c8bd3
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251012"
---
# <a name="deploy-resources-with-arm-templates-and-azure-powershell"></a>Nasazení prostředků pomocí šablon ARM a Azure PowerShell

Tento článek vysvětluje, jak používat Azure PowerShell se šablonami Azure Resource Manager (šablony ARM) k nasazení vašich prostředků do Azure. Pokud nejste obeznámeni s koncepty nasazení a správy řešení Azure, přečtěte si téma [Přehled nasazení šablony](overview.md).

## <a name="prerequisites"></a>Předpoklady

K nasazení budete potřebovat šablonu. Pokud ho ještě nemáte, Stáhněte si a uložte [ukázkovou šablonu](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) z úložiště šablon Azure pro rychlý Start. Místní název souboru použitý v tomto článku je _C:\MyTemplates\azuredeploy.js_.

Musíte nainstalovat Azure PowerShell a připojit se k Azure:

- **Nainstalujte rutiny Azure PowerShell na místním počítači.** Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).
- **Připojte se k Azure pomocí [Connect-AZAccount](/powershell/module/az.accounts/connect-azaccount)**. Pokud máte více předplatných Azure, může být nutné spustit také rutinu [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext). Další informace najdete v tématu [použití více předplatných Azure](/powershell/azure/manage-subscriptions-azureps).

Pokud nemáte nainstalovaný PowerShell, můžete použít Azure Cloud Shell. Další informace najdete v tématu [Nasazení šablon ARM z Azure Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo tenanta. V závislosti na rozsahu nasazení použijete jiné příkazy.

- K nasazení do **skupiny prostředků** použijte [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment):

  ```azurepowershell
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <path-to-template>
  ```

- K nasazení do **předplatného** použijte [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment) , což je alias `New-AzDeployment` rutiny:

  ```azurepowershell
  New-AzSubscriptionDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

- K nasazení do **skupiny pro správu** použijte [New-AzManagementGroupDeployment](/powershell/module/az.resources/New-AzManagementGroupDeployment).

  ```azurepowershell
  New-AzManagementGroupDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

- K nasazení do **tenanta** použijte [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

  ```azurepowershell
  New-AzTenantDeployment -Location <location> -TemplateFile <path-to-template>
  ```

  Další informace o nasazeních na úrovni tenanta najdete v tématu [vytvoření prostředků na úrovni tenanta](deploy-to-tenant.md).

Pro každý obor musí mít uživatel, který šablonu nasazuje, potřebná oprávnění k vytváření prostředků.

## <a name="deploy-local-template"></a>Nasazení místní šablony

Šablonu můžete nasadit z místního počítače nebo z nějakého, který je uložen externě. Tato část popisuje nasazení místní šablony.

Pokud provádíte nasazení do skupiny prostředků, která neexistuje, vytvořte skupinu prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Název nemůže končit tečkou.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

K nasazení místní šablony použijte `-TemplateFile` parametr v příkazu nasazení. Následující příklad také ukazuje, jak nastavit hodnotu parametru, který pochází ze šablony.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json
```

Dokončení nasazení může trvat několik minut.

## <a name="deploy-remote-template"></a>Nasadit vzdálenou šablonu

Místo uložení šablon ARM na místní počítač můžete chtít ukládat je do externího umístění. Šablony můžete uložit do úložiště pro správu zdrojového kódu (jako je GitHub). Nebo je můžete uložit do účtu úložiště v Azure, abyste k nim mohli v organizaci sdílet přístup.

Pokud provádíte nasazení do skupiny prostředků, která neexistuje, vytvořte skupinu prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Název nemůže končit tečkou.

```azurepowershell
New-AzResourceGroup -Name ExampleGroup -Location "Central US"
```

K nasazení externí šablony použijte parametr `-TemplateUri`.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Předchozí příklad vyžaduje pro šablonu veřejně přístupný identifikátor URI, který funguje ve většině scénářů, protože by šablona neměla obsahovat citlivá data. Pokud potřebujete zadat citlivá data (třeba heslo správce), předejte tuto hodnotu jako zabezpečený parametr. Pokud ale chcete spravovat přístup k šabloně, zvažte použití [specifikací šablon](#deploy-template-spec).

## <a name="deployment-name"></a>Název nasazení

Při nasazování šablony ARM můžete dát nasazení název. Tento název vám může pomáhat s načtením nasazení z historie nasazení. Pokud název nasazení nezadáte, použije se název souboru šablony. Například pokud nasadíte šablonu s názvem `azuredeploy.json` a nezadáte název nasazení, nasazení se pojmenuje `azuredeploy` .

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

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

Místo nasazení místní nebo vzdálené šablony můžete vytvořit [specifikaci šablony](template-specs.md). Specifikace šablony je prostředek ve vašem předplatném Azure, který obsahuje šablonu ARM. Usnadňuje bezpečné sdílení šablony s uživateli ve vaší organizaci. K udělení přístupu ke specifikaci šablony použijte řízení přístupu na základě role Azure (Azure RBAC). Tato funkce je aktuálně ve verzi Preview.

Následující příklady ukazují, jak vytvořit a nasadit specifikace šablony.

Nejdřív vytvořte specifikaci šablony tím, že poskytnete šablonu ARM.

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0 `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateJsonFile ./mainTemplate.json
```

Pak Získejte ID pro specifikaci šablony a nasaďte ji.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

Další informace najdete v tématu [Azure Resource Manager specifikace šablon (Preview)](template-specs.md).

## <a name="preview-changes"></a>Zobrazit náhled změn

Před nasazením šablony můžete zobrazit náhled změn, které šablona provede pro vaše prostředí. Pomocí [operace citlivosti](template-deploy-what-if.md) ověřte, že šablona provádí očekávané změny. Co když zároveň ověří chyby v šabloně.

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

Místo předávání parametrů v podobě hodnot vložených do skriptu pro vás možná bude jednodušší použít soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů může být místní soubor nebo externí soubor s přístupným identifikátorem URI.

Další informace o souboru parametrů najdete v tématu [Vytvoření souboru parametrů Resource Manageru](parameter-files.md).

Chcete-li předat místní soubor parametrů, použijte `TemplateParameterFile` parametr:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Chcete-li předat externí soubor parametrů, použijte `TemplateParameterUri` parametr:

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

## <a name="next-steps"></a>Další kroky

- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Informace o definování parametrů v šabloně najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
- Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony ARM pomocí tokenu SAS](secure-template-with-sas-token.md).
