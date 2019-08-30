---
title: Použití balíčku Azure Stream Analytics CI/CD npm
description: Tento článek popisuje, jak pomocí Azure Stream Analytics balíčku CI/CD npm nastavit proces průběžné integrace a nasazování.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: df9afaaeeb7e41c111fe6bd053047095a9cb9349
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173350"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Použití balíčku Stream Analytics CI/CD npm
Tento článek popisuje, jak pomocí balíčku Azure Stream Analytics CI/CD npm nastavit proces průběžné integrace a nasazování.

## <a name="build-the-vs-code-project"></a>Sestavení projektu VS Code

Můžete povolit průběžnou integraci a nasazení pro Azure Stream Analytics úlohy pomocí balíčku npm **ASA-streamanalytics-cicd** . Balíček npm poskytuje nástroje pro generování Azure Resource Manager šablon [Stream Analytics projektů Visual Studio Code](quick-create-vs-code.md). Dá se použít na Windows, macOS a Linux bez instalace Visual Studio Code.

Po [Stažení balíčku](https://www.npmjs.com/package/azure-streamanalytics-cicd)použijte následující příkaz pro výstup šablon Azure Resource Manager. Argument **ScriptPath** je absolutní cesta k souboru **asaql** v projektu. Ujistěte se, že soubory asaproj. JSON a JobConfig. JSON jsou ve stejné složce se souborem skriptu. Pokud není zadán parametr **outputPath** , šablony budou umístěny do složky **Deploy** ve složce **bin** projektu.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Příklad (na macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Po úspěšném sestavení Stream Analytics Visual Studio Code vygeneruje následující dva Azure Resource Manager soubory šablon ve složce **bin/[Debug/Retail]/Deploy** : 

*  Soubor šablony Správce prostředků

       [ProjectName].JobTemplate.json 

*  Soubor parametrů Správce prostředků

       [ProjectName].JobTemplate.parameters.json   

Výchozí parametry v souboru Parameters. JSON jsou z nastavení v projektu Visual Studio Code. Pokud chcete nasazení nasadit do jiného prostředí, nahraďte příslušné parametry odpovídajícím způsobem.

> [!NOTE]
> U všech přihlašovacích údajů jsou výchozí hodnoty nastavené na hodnotu null. Před nasazením do cloudu je **nutné** nastavit hodnoty.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Přečtěte si další informace o tom, jak [nasadit pomocí souboru šablony Správce prostředků a Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Přečtěte si další informace o [použití objektu jako parametru v šabloně správce prostředků](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Pokud chcete použít spravovanou identitu pro Azure Data Lake Store Gen1 jako výstupní jímku, musíte před nasazením do Azure poskytnout přístup k instančnímu objektu pomocí PowerShellu. Přečtěte si další informace o [nasazení adls Gen1 se správou identity pomocí šablony Správce prostředků](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvoření cloudové úlohy Azure Stream Analytics v Visual Studio Code (Preview)](quick-create-vs-code.md)
* [Test Stream Analytics dotazy místně pomocí Visual Studio Code (Preview)](vscode-local-run.md)
* [Prozkoumat Azure Stream Analytics s využitím Visual Studio Code (Preview)](vscode-explore-jobs.md)
