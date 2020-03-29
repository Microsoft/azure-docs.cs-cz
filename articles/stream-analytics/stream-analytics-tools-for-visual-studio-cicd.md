---
title: Použití balíčku NuGet služby Azure Stream Analytics CI/CD
description: Tento článek popisuje, jak pomocí balíčku NuGet služby Azure Stream Analytics CI/CD NuGet nastavit proces průběžné integrace a nasazování.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 11e68aaa7c70d4f888c0009bc28d9bb90f431f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354443"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Použití balíčku NuGet služby Azure Stream Analytics CI/CD pro integraci a vývoj 
Tento článek popisuje, jak použít balíček NuGet služby Azure Stream Analytics CI/CD k nastavení procesu průběžné integrace a nasazování.

Pomocí verze 2.3.0000.0 nebo vyšší nástroje [Stream Analytics pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) získat podporu pro MSBuild.

Balíček NuGet je k dispozici: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Poskytuje nástroje MSBuild, místní spuštění a nasazení, které podporují proces průběžné integrace a nasazování [projektů Aplikace Stream Analytics Visual Studio](stream-analytics-vs-tools.md). 
> [!NOTE]
> Balíček NuGet lze použít pouze s verzí 2.3.0000.0 nebo vyšší pro Nástroje Analýzy streamu pro Visual Studio. Pokud máte projekty vytvořené v předchozích verzích nástrojů sady Visual Studio, stačí je otevřít pomocí verze 2.3.0000.0 nebo vyšší a uložit. Poté jsou povoleny nové funkce. 

Další informace najdete v [tématu Nástroje Analýzy streamů pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Stejně jako standardní prostředí Visual Studio MSBuild, k vytvoření projektu máte dvě možnosti. Můžete kliknout pravým tlačítkem myši na projekt a pak zvolit **Sestavení**. Můžete také použít **MSBuild** v balíčku NuGet z příkazového řádku.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Když se projekt Vizuální hospodařící aplikace Stream Analytics, generuje následující dva soubory šablon Azure Resource Manager u složky Bin/[Debug/Retail]:When a Stream Analytics Visual Studio project builds successfully, it generates the following two Azure Resource Manager template files under the **bin/[Debug/Retail]/Deploy** folder: 

*  Soubor šablony Správce prostředků

       [ProjectName].JobTemplate.json 

*  Soubor parametrů Správce prostředků

       [ProjectName].JobTemplate.parameters.json   

Výchozí parametry v souboru parameters.json jsou z nastavení v projektu sady Visual Studio. Pokud chcete nasadit do jiného prostředí, odpovídajícím způsobem nahraďte parametry.

> [!NOTE]
> Pro všechna pověření jsou výchozí hodnoty nastaveny na hodnotu null. Před nasazením do cloudu je **nutné** nastavit hodnoty.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Přečtěte si další informace o tom, jak [nasadit se souborem šablony Správce prostředků a Azure PowerShellem.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy) Další informace o [použití objektu jako parametru v šabloně Správce prostředků](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Chcete-li použít spravovanou identitu pro Azure Data Lake Store Gen1 jako výstupní jímku, musíte před nasazením do Azure poskytnout přístup k instančnímu objektu pomocí PowerShellu pomocí PowerShellu. Přečtěte si další informace o tom, jak [nasadit ADLS Gen1 pomocí spravované identity se šablonou Správce prostředků](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Nástroj příkazového řádku

### <a name="build-the-project"></a>Sestavení projektu
Balíček NuGet má nástroj příkazového řádku s názvem **SA.exe**. Podporuje projektové sestavení a lokální testování na libovolném počítači, který můžete použít v procesu průběžné integrace a průběžného doručování. 

Soubory nasazení jsou ve výchozím nastavení umístěny pod aktuálním adresářem. Výstupní cestu můžete určit pomocí následujícího parametru -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testování skriptu místně

Pokud váš projekt zadal místní vstupní soubory v sadě Visual Studio, můžete spustit test automatického skriptu pomocí příkazu *localrun.* Výsledek výstupu je umístěn pod aktuálním adresářem.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generování souboru definice úlohy, který se použije s rozhraním API Prostředí PowerShell Stream Analytics

Příkaz *arm* přebírá jako vstup soubory parametrů šablony úlohy a šablony úlohy generované pomocí sestavení. Pak je zkombinuje do souboru JSON definice úlohy, který lze použít s rozhraním API Prostředí PowerShell Stream Analytics.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Příklad:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Další kroky

* [Úvodní příručka: Vytvoření cloudové úlohy Azure Stream Analytics ve Visual Studiu](stream-analytics-quick-create-vs.md)
* [Test Stream Analytics dotazy místně s Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Prozkoumejte úlohy Azure Stream Analytics pomocí Visual Studia](stream-analytics-vs-tools.md)
