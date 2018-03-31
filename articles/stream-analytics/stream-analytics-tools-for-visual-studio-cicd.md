---
title: Nastavit průběžnou integraci a nasazení procesu pomocí nástrojů Stream Analytics Visual Studio | Microsoft Docs
description: Kurz pro vytvoření průběžnou integraci a nasazení procesu pomocí nástrojů Stream Analytics Visual Studio
keywords: Visual studio, NuGet, DevOps, CI/CD
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 14bb15f19b517b55281959f0de970e3f5e0d360b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-a-continuous-integration-and-deployment-process"></a>Nastavit průběžnou integraci a nasazení procesu pomocí nástrojů Stream Analytics Visual Studio
V tomto kurzu dozvíte, jak nastavit průběžnou integraci a proces nasazení pomocí nástroje Azure Stream Analytics Visual Studio.

Nejnovější verzi (2.3.0000.0 nebo vyšší) z [Stream Analytics tools pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) přidává podporu pro nástroje MSBuild.

Je také nově vydané balíčku NuGet, [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Poskytuje nástroje MSBuild, místní spuštění a nástroje pro nasazení, které podporují průběžnou integraci a nasazení proces projektů sady Visual Studio Stream Analytics. 
> [!NOTE] 
Balíček NuGet můžete použít pouze s 2.3.0000.0 nebo vyšší verzi Stream Analytics Tools pro sadu Visual Studio. Pokud máte projektů vytvořených v předchozích verzích nástroje sady Visual Studio, stačí je otevřít s 2.3.0000.0 nebo vyšší verze a uložte. Nové funkce, které jsou poté povoleny. 

Další informace o použití [Stream Analytics tools pro Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

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


