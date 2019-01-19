---
title: Průběžnou integraci a vývoj s použitím Stream Analytics tools
description: Tento článek popisuje, jak nastavit průběžnou integraci a proces nasazení pomocí Visual Studio tools pro Azure Stream Analytics.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2017
ms.openlocfilehash: 85cb23d865133f2ee769874aa5150ab13e04cd69
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54410547"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Průběžnou integraci a vývoj s použitím Stream Analytics tools
Tento článek popisuje, jak můžete nastavit průběžnou integraci a nasazování procesu pomocí nástroje Azure Stream Analytics pro Visual Studio.

Použijte verzi 2.3.0000.0 nebo novější z [nástroje Stream Analytics pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) jak získat podporu pro MSBuild.

Balíček NuGet je k dispozici: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Poskytuje nástroj MSBuild, místní spuštění a nástroje pro nasazení, které podporují kontinuální integraci a proces nasazení projektů Stream Analytics v sadě Visual Studio. 
> [!NOTE] 
Balíček NuGet lze použít pouze 2.3.0000.0 nebo vyšší než verze nástrojů pro Stream Analytics pro Visual Studio. Pokud máte projekty vytvořené v předchozích verzích sady Visual Studio tools, stačí je otevřít 2.3.0000.0 nebo vyšší než verze a uložit. Nové funkce jsou povoleny. 

Další informace najdete v tématu [nástroje Stream Analytics pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Podobně jako standardní prostředí Visual Studio MSBuild k sestavení projektu máte dvě možnosti. Klikněte pravým tlačítkem na projekt a klikněte na tlačítko **sestavení**. Můžete také použít **MSBuild** v balíčku NuGet z příkazového řádku.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Když projekt sady Visual Studio pro Stream Analytics sestavena úspěšně, generuje následující dvě šablony soubory Azure Resource Manageru v rámci **bin / [ladění/maloobchodní] / Deploy** složky: 

*  Soubor šablony Resource Manageru

       [ProjectName].JobTemplate.json 

*  Soubor parametrů Resource Manageru

       [ProjectName].JobTemplate.parameters.json   

Výchozí parametry v souboru parameters.JSON tímto se z nastavení projektu sady Visual Studio. Pokud chcete nasadit do jiného prostředí, nahraďte parametry odpovídajícím způsobem.

> [!NOTE] 
Pro všechny přihlašovací údaje, výchozí hodnoty jsou nastaveny na hodnotu null. Jste *požadované* nastavit hodnoty před nasazením do cloudu.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Další informace o tom, jak [nasadit pomocí souboru šablony Resource Manageru a Azure Powershellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Další informace o tom, jak [použití objektu jako parametr v šabloně Resource Manageru](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Použití spravované Identity jako výstupní jímku pro Azure Data Lake Store Gen1, budete muset poskytnout přístup ke službě instančního objektu pomocí prostředí PowerShell před nasazením do Azure. Další informace o tom, jak [nasazení Gen1 ADLS s využitím Identity spravované pomocí šablony Resource Manageru](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Nástroj příkazového řádku

### <a name="build-the-project"></a>Sestavení projektu
Balíček NuGet je nástroj příkazového řádku, který volá **SA.exe**. Podporuje sestavování projektu a místní testování na libovolný počítač, který můžete použít v průběžnou integraci a průběžné doručování procesu. 

Ve výchozím nastavení jsou soubory nasazení umístěny v aktuálním adresáři. Výstupní cestu můžete zadat pomocí parametru - OutputPath následující:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testování skriptu místně

Pokud váš projekt má zadanou místní vstupní soubory v sadě Visual Studio, můžete spustit test automatického skriptu s použitím *localrun* příkazu. Výstup výsledku je umístěn v aktuálním adresáři.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generovat soubor s definicí úlohy pomocí rozhraní API prostředí PowerShell pro Stream Analytics

*Arm* příkaz má úloha šablony a soubory parametrů šablony úloh generovány pomocí sestavení jako vstup. Potom kombinuje je do souboru JSON definice úlohy, který je možné pomocí rozhraní API prostředí PowerShell pro Stream Analytics.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Příklad:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


