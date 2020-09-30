---
title: Globální parametry
description: Nastavení globálních parametrů pro každé z vašich Azure Data Factorych prostředí
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.date: 08/31/2020
ms.openlocfilehash: a936fbec23a38c5b96c678b38b92eed9346b88bf
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91567532"
---
# <a name="global-parameters-in-azure-data-factory"></a>Globální parametry v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Globální parametry jsou konstanty v rámci datové továrny, které mohou být spotřebovány kanálem v libovolném výrazu. Jsou užitečné v případě, že máte více kanálů se stejnými názvy parametrů a hodnotami. Při zvýšení úrovně datové továrny pomocí procesu průběžné integrace a nasazování (CI/CD) můžete tyto parametry v každém prostředí přepsat. 

## <a name="creating-global-parameters"></a>Vytváření globálních parametrů

Globální parametr vytvoříte tak, že přejdete na kartu *globální parametry* v části *Spravovat* . Výběrem **nové** otevřete stranu vytvoření – navigace.

![Snímek obrazovky, který zvýrazní nové tlačítko, které vyberete k vytvoření globálních parametrů.](media/author-global-parameters/create-global-parameter-1.png)

Na straně vedlejší navigace zadejte název, vyberte datový typ a zadejte hodnotu parametru.

![Snímek obrazovky, který ukazuje, kde můžete přidat název, datový typ a hodnotu nového globálního parametru.](media/author-global-parameters/create-global-parameter-2.png)

Po vytvoření globálního parametru ho můžete upravit kliknutím na název parametru. Chcete-li změnit více parametrů najednou, vyberte možnost **Upravit vše**.

![Vytvoření globálních parametrů](media/author-global-parameters/create-global-parameter-3.png)

## <a name="using-global-parameters-in-a-pipeline"></a>Použití globálních parametrů v kanálu

Globální parametry lze použít v jakémkoli [výrazu kanálu](control-flow-expression-language-functions.md). Pokud kanál odkazuje na jiný prostředek, například datovou sadu nebo tok dat, můžete hodnotu globálního parametru předat prostřednictvím parametrů daného prostředku. Globální parametry jsou odkazovány jako `pipeline().globalParameters.<parameterName>` .

![Použití globálních parametrů](media/author-global-parameters/expression-global-parameters.png)

## <a name="global-parameters-in-cicd"></a><a name="cicd"></a> Globální parametry v CI/CD

Existují dva způsoby, jak integrovat globální parametry do řešení pro kontinuální integraci a nasazení:

* Zahrnutí globálních parametrů do šablony ARM
* Nasazení globálních parametrů prostřednictvím skriptu PowerShellu

Pro většinu případů použití se doporučuje zahrnout globální parametry do šablony ARM. Tato akce bude nativně integrována s řešením popsaným v [dokumentu CI/CD](continuous-integration-deployment.md). Globální parametry budou ve výchozím nastavení přidány jako parametr šablony ARM, protože se často mění z prostředí na prostředí. Můžete povolit zahrnutí globálních parametrů v šabloně ARM z centra pro správu.

![Zahrnout do šablony ARM](media/author-global-parameters/include-arm-template.png)

Přidáním globálních parametrů do šablony ARM přidáte nastavení na úrovni továrny, které může přepsat další nastavení na úrovni továrny, jako je například klíč spravovaný zákazníkem nebo konfigurace Gitu v jiných prostředích. Pokud máte tato nastavení povolená v prostředí se zvýšenými oprávněními, jako je UAT nebo PROD, je lepší nasadit globální parametry prostřednictvím skriptu PowerShellu v níže popsaném postupu.

### <a name="deploying-using-powershell"></a>Nasazení pomocí PowerShellu

Následující kroky popisují, jak nasadit globální parametry přes PowerShell. To je užitečné v případě, že váš cílový objekt factory má nastavení na úrovni továrny, jako je například klíč spravovaný zákazníkem.

Když publikujete objekt pro vytváření nebo exportujete šablonu ARM s globálními parametry, vytvoří se složka s názvem *globalParameters* se souborem s názvem *your-factory-name_GlobalParameters.js*. Tento soubor je objekt JSON, který obsahuje jednotlivé globální typy parametrů a hodnotu v publikované továrně.

![Publikování globálních parametrů](media/author-global-parameters/global-parameters-adf-publish.png)

Pokud nasazujete do nového prostředí, jako je například TEST nebo produkční prostředí, doporučujeme vytvořit kopii tohoto souboru globálních parametrů a přepsat příslušné hodnoty specifické pro dané prostředí. Při opětovném publikování původního souboru globálních parametrů se přepíší, ale kopie druhého prostředí se nedotkne.

Například pokud máte továrnu s názvem "ADF-DEV" a globální parametr typu String s názvem "prostředí" s hodnotou "dev", vygeneruje se při publikování souboru s názvem *ADF-DEV_GlobalParameters.js* . Pokud se nasazuje do testovací továrny s názvem ' ADF_TEST ', vytvořte kopii souboru JSON (například s názvem ADF-TEST_GlobalParameters.json) a nahraďte hodnoty parametrů hodnotami specifickými pro prostředí. Parametr Environment může mít nyní hodnotu test. 

![Nasazení globálních parametrů](media/author-global-parameters/powershell-task.png)

Pro zvýšení úrovně globálních parametrů na další prostředí použijte níže uvedený skript prostředí PowerShell. Přidejte úlohu Azure PowerShell DevOps před Template deployment ARM. V úloze DevOps je nutné zadat umístění nového souboru parametrů, cílovou skupinu prostředků a cílovou datovou továrnu.

> [!NOTE]
> Chcete-li nasadit globální parametry pomocí prostředí PowerShell, je nutné použít alespoň 4.4.0 verze modulu AZ Module.

```powershell
param
(
    [parameter(Mandatory = $true)] [String] $globalParametersFilePath,
    [parameter(Mandatory = $true)] [String] $resourceGroupName,
    [parameter(Mandatory = $true)] [String] $dataFactoryName
)

Import-Module Az.DataFactory

$newGlobalParameters = New-Object 'system.collections.generic.dictionary[string,Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification]'

Write-Host "Getting global parameters JSON from: " $globalParametersFilePath
$globalParametersJson = Get-Content $globalParametersFilePath

Write-Host "Parsing JSON..."
$globalParametersObject = [Newtonsoft.Json.Linq.JObject]::Parse($globalParametersJson)

foreach ($gp in $globalParametersObject.GetEnumerator()) {
    Write-Host "Adding global parameter:" $gp.Key
    $globalParameterValue = $gp.Value.ToObject([Microsoft.Azure.Management.DataFactory.Models.GlobalParameterSpecification])
    $newGlobalParameters.Add($gp.Key, $globalParameterValue)
}

$dataFactory = Get-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Name $dataFactoryName
$dataFactory.GlobalParameters = $newGlobalParameters

Write-Host "Updating" $newGlobalParameters.Count "global parameters."

Set-AzDataFactoryV2 -InputObject $dataFactory -Force
```

## <a name="next-steps"></a>Další kroky

* Další informace o [procesu průběžné integrace a nasazování](continuous-integration-deployment.md) Azure Data Factory
* Naučte se používat [Jazyk výrazu řízení toku](control-flow-expression-language-functions.md) dat.