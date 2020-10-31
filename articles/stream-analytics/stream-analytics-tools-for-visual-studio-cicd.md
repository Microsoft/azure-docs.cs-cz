---
title: Použití balíčku NuGet pro Azure Stream Analytics CI/CD
description: Tento článek popisuje, jak pomocí Azure Stream Analytics balíčku CI/CD NuGet nastavit proces průběžné integrace a nasazování.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: c05db2d9ba184da89665a236994c851355cc2644
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127429"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Použití balíčku NuGet Azure Stream Analytics CI/CD pro integraci a vývoj 
Tento článek popisuje, jak použít balíček NuGet Azure Stream Analytics CI/CD, abyste nastavili proces průběžné integrace a nasazování.

K získání podpory pro MSBuild použijte verzi 2.3.0000.0 nebo novější z [Stream Analytics nástrojů pro Visual Studio](./stream-analytics-quick-create-vs.md) .

K dispozici je balíček NuGet: [Microsoft. Azure. Stream Analytics. CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Poskytuje nástroje pro MSBuild, místní běh a nasazení, které podporují proces průběžné integrace a nasazování [Stream Analytics projektů sady Visual Studio](stream-analytics-vs-tools.md). 
> [!NOTE]
> Balíček NuGet se dá použít jenom s 2.3.0000.0 nebo novější verzí Stream Analytics nástrojů pro Visual Studio. Pokud máte projekty vytvořené v předchozích verzích nástrojů sady Visual Studio, stačí je otevřít pomocí 2.3.0000.0 nebo novější verze a uložit. Nové funkce jsou pak povoleny. 

Další informace naleznete v tématu [Stream Analytics Tools for Visual Studio](./stream-analytics-quick-create-vs.md).

## <a name="msbuild"></a>MSBuild
Podobně jako standardní prostředí Visual Studio MSBuild pro sestavení projektu máte dvě možnosti. Můžete kliknout pravým tlačítkem na projekt a pak zvolit **sestavit** . Nástroj **MSBuild** můžete také použít v balíčku NuGet z příkazového řádku.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Po úspěšném sestavení Stream Analytics projektu sady Visual Studio vygeneruje následující dva soubory šablon Azure Resource Manager ve složce **bin/[Debug/Retail]/Deploy** : 

* Soubor šablony Správce prostředků

   `[ProjectName].JobTemplate.json`

* Soubor parametrů Správce prostředků
   
   `[ProjectName].JobTemplate.parameters.json`

Výchozí parametry v souboru parameters.json jsou z nastavení v projektu sady Visual Studio. Pokud chcete nasazení nasadit do jiného prostředí, nahraďte příslušné parametry odpovídajícím způsobem.

> [!NOTE]
> U všech přihlašovacích údajů jsou výchozí hodnoty nastavené na hodnotu null. Před nasazením do cloudu je **nutné** nastavit hodnoty.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Přečtěte si další informace o tom, jak [nasadit pomocí souboru šablony Správce prostředků a Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Přečtěte si další informace o [použití objektu jako parametru v šabloně správce prostředků](/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Pokud chcete použít spravovanou identitu pro Azure Data Lake Store Gen1 jako výstupní jímku, musíte před nasazením do Azure poskytnout přístup k instančnímu objektu pomocí PowerShellu. Přečtěte si další informace o [nasazení adls Gen1 se správou identity pomocí šablony Správce prostředků](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Nástroj pro příkazový řádek

### <a name="build-the-project"></a>Sestavení projektu
Balíček NuGet má nástroj příkazového řádku s názvem **SA.exe** . Podporuje sestavení projektu a místní testování na libovolném počítači, který můžete použít v procesu kontinuální integrace a průběžného doručování. 

Ve výchozím nastavení jsou soubory nasazení umístěny do aktuálního adresáře. Výstupní cestu můžete zadat pomocí následujícího parametru-OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Místní otestování skriptu

Pokud váš projekt má v aplikaci Visual Studio určené místní vstupní soubory, můžete spustit automatický test skriptu pomocí příkazu *localrun* . Výsledek výstupu je umístěn v rámci aktuálního adresáře.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generování souboru definice úlohy pro použití s rozhraním API Stream Analytics PowerShell

Příkaz *ARM* vezme šablonu úlohy a soubory parametrů šablony úlohy vygenerované pomocí Build jako Input. Pak je zkombinuje do souboru JSON definice úlohy, který se dá použít s rozhraním API Stream Analytics PowerShellu.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Příklad:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Další kroky

* [Rychlý Start: vytvoření cloudové úlohy Azure Stream Analytics v aplikaci Visual Studio](stream-analytics-quick-create-vs.md)
* [Testování Stream Analytics dotazů v místním prostředí se sadou Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Prozkoumejte Azure Stream Analytics úlohy pomocí sady Visual Studio](stream-analytics-vs-tools.md)