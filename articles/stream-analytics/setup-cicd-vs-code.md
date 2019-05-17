---
title: Průběžnou integraci a vývoj s využitím Azure Stream Analytics CI/CD balíčků npm
description: Tento článek popisuje, jak nastavit průběžnou integraci a proces nasazení pomocí Azure Stream Analytics CI/CD balíčků npm.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 7fe46712d610d881c21653461d12e4f8efecb468
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827872"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>Průběžnou integraci a vývoj s použitím balíčku npm Stream Analytics CI/CD
Tento článek popisuje, jak nastavit průběžnou integraci a proces nasazení pomocí balíčku npm Azure Stream Analytics CI/CD.

## <a name="build-the-vs-code-project"></a>Sestavte projekt VS Code

Můžete povolit průběžnou integraci a nasazování pro úlohy Azure Stream Analytics pomocí **Azure Stream Analytics. streamanalytics cicd** balíčku npm. Poskytuje nástroje pro generování šablon Azure Resource Manageru z balíčku npm [projekty Stream Analytics Visual Studio Code](quick-create-vs-code.md). Můžete použít ve Windows, macOS a Linuxu bez instalace Visual Studio Code.

Jakmile budete mít [stáhli balíček](https://www.npmjs.com/package/azure-streamanalytics-cicd), použijte následující příkaz pro výstup šablony Azure Resource Manageru. Pokud **outputPath** neurčíte, šablony se umístí do **nasadit** ve složce projektu **bin** složky.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

Když projekt Stream Analytics Visual Studio Code sestavena úspěšně, generuje následující dvě šablony soubory Azure Resource Manageru v rámci **bin / [ladění/maloobchodní] / Deploy** složky: 

*  Soubor šablony Resource Manageru

       [ProjectName].JobTemplate.json 

*  Soubor parametrů Resource Manageru

       [ProjectName].JobTemplate.parameters.json   

Výchozí parametry v souboru parameters.JSON tímto se z nastavení projektu sady Visual Studio. Pokud chcete nasadit do jiného prostředí, nahraďte parametry odpovídajícím způsobem.

> [!NOTE]
> Pro všechny přihlašovací údaje, výchozí hodnoty jsou nastaveny na hodnotu null. Jste **požadované** nastavit hodnoty před nasazením do cloudu.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Další informace o tom, jak [nasadit pomocí souboru šablony Resource Manageru a Azure Powershellu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Další informace o tom, jak [použití objektu jako parametr v šabloně Resource Manageru](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Použití spravované Identity jako výstupní jímku pro Azure Data Lake Store Gen1, budete muset poskytnout přístup ke službě instančního objektu pomocí prostředí PowerShell před nasazením do Azure. Další informace o tom, jak [nasazení Gen1 ADLS s využitím Identity spravované pomocí šablony Resource Manageru](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Vytvořit úlohu Azure Stream Analytics cloudu ve Visual Studio Code (Preview)](quick-create-vs-code.md)
* [Testování dotazů Stream Analytics místně pomocí Visual Studio Code (Preview)](vscode-local-run.md)
* [Prozkoumejte službu Azure Stream Analytics pomocí sady Visual Studio Code (Preview)](vscode-explore-jobs.md)
