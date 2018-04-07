---
title: Nepřetržitě integrovat a vývoj pomocí nástroje Stream Analytics
description: Tento článek popisuje, jak používat Visual Studio tools pro Azure Stream Analytics nastavit průběžnou integraci a proces nasazení.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 9/27/2017
ms.openlocfilehash: e4e831c602255df66f4c86ffa17336f51d2b52f7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Nepřetržitě integrovat a vývoj pomocí nástroje Stream Analytics
Tento článek popisuje, jak k používání Azure Stream Analytics tools pro Visual Studio vytvoření průběžnou integraci a nasazení procesu.

Použijte verzi 2.3.0000.0 nebo novější z [Stream Analytics tools pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) získat podporu pro nástroje MSBuild.

Balíček NuGet je k dispozici: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Poskytuje nástroje MSBuild, místní spuštění a nástroje pro nasazení, které podporují průběžnou integraci a nasazení proces projektů sady Visual Studio Stream Analytics. 
> [!NOTE] 
Balíček NuGet můžete použít pouze s 2.3.0000.0 nebo vyšší verzi Stream Analytics Tools pro sadu Visual Studio. Pokud máte projektů vytvořených v předchozích verzích nástroje sady Visual Studio, stačí je otevřít s 2.3.0000.0 nebo vyšší verze a uložte. Nové funkce, které jsou poté povoleny. 

Další informace najdete v tématu [Stream Analytics tools pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>Nástroje MSBuild
Jako standardní prostředí Visual Studio MSBuild pro sestavení projektu, máte dvě možnosti. Klikněte pravým tlačítkem na projekt a zvolte **sestavení**. Můžete taky použít **MSBuild** v balíčku NuGet z příkazového řádku.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Při projekt sady Stream Analytics Visual Studio sestavení úspěšně, vygeneruje následující dvě šablony soubory Azure Resource Manager v **bin / [ladění nebo prodejní] / nasazení** složky: 

*  Soubor šablony Resource Manageru

       [ProjectName].JobTemplate.json 

*  Soubor parametrů Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Výchozí parametry v souboru parameters.JSON tímto kódem jsou z nastavení v projektu sady Visual Studio. Pokud chcete nasadit do jiného prostředí, nahraďte parametry odpovídajícím způsobem.

> [!NOTE] 
Pro všechny přihlašovací údaje, výchozí hodnoty jsou nastaveny na hodnotu null. Jste *požadované* nastavit hodnoty před nasazením do cloudu.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Další informace o tom, jak [nasazení s soubor šablony Resource Manageru a prostředí Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Další informace o tom, jak [použít jako parametr v šabloně Resource Manager objekt](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).


## <a name="command-line-tool"></a>Nástroj příkazového řádku

### <a name="build-the-project"></a>Sestavení projektu
Balíček NuGet je nástroj příkazového řádku názvem **SA.exe**. Podporuje sestavení projektu a místní testování na libovolný počítač, který můžete použít v průběžnou integraci a nastavené průběžné doručování procesu. 

Soubory nasazení jsou umístěny v adresáři aktuální ve výchozím nastavení. Výstupní cesta můžete určit pomocí parametru - OutputPath následující:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Skript lokálně otestovat

Pokud váš projekt má zadanou místní vstupní soubory v sadě Visual Studio, můžete spustit test automatizované skriptu pomocí *localrun* příkaz. Výstup výsledků je umístěn v adresáři aktuální.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Vygenerujte soubor definice úlohy pro použití s rozhraní API prostředí PowerShell Stream Analytics

*Arm* příkaz má úloha šablony a soubory parametrů šablony úlohy generované prostřednictvím sestavení jako vstup. Pak je kombinací je do souboru JSON definice úlohy, které je možné pomocí rozhraní API prostředí PowerShell Stream Analytics.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Příklad:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


