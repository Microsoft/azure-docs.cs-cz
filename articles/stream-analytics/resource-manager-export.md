---
title: Export šablony Azure Stream Analytics úlohy Azure Resource Manager
description: Tento článek popisuje, jak exportovat šablonu Azure Resource Manager pro úlohu Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: c421c730321213a9adbf0a0043874a21ba26bb05
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906240"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Export šablony Azure Stream Analytics úlohy Azure Resource Manager

[Šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) umožňují implementovat infrastrukturu jako kód. Šablona je soubor JavaScript Object Notation (JSON), který definuje infrastrukturu a konfiguraci pro vaše prostředky. Zadejte prostředky, které chcete nasadit, a vlastnosti těchto prostředků.

Azure Stream Analytics úlohu můžete znovu nasadit tak, že exportujete šablonu Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Otevřete úlohu v VS Code

Než budete moct Exportovat šablonu, musíte nejdřív v Visual Studio Code otevřít existující úlohu Stream Analytics. 

Chcete-li exportovat úlohu do místního projektu, vyhledejte úlohu, kterou chcete exportovat, v **průzkumníkovi Stream Analytics** v Azure Portal. Na stránce **dotazu** vyberte **otevřít v aplikaci Visual Studio**. Pak vyberte **Visual Studio Code**.

![Otevřít úlohu Stream Analytics v Visual Studio Code](./media/resource-manager-export/open-job-vs-code.png)

Další informace o použití Visual Studio Code ke správě úloh Stream Analytics najdete v tématu [rychlý Start pro Visual Studio Code](quick-create-visual-studio-code.md).

## <a name="compile-the-script"></a>Zkompilujte skript 

Dalším krokem je kompilace skriptu úlohy do šablony Azure Resource Manager. Před kompilací skriptu se ujistěte, že má vaše úloha nakonfigurované aspoň jeden vstup a jeden výstup. Pokud není nakonfigurovaný žádný vstup nebo výstup, musíte nejdřív nakonfigurovat vstup a výstup.

1. V Visual Studio Code přejděte do souboru *transformace. asaql* vaší úlohy.

   ![Transformační soubor. asaql v Visual Studio Code](./media/resource-manager-export/transformation-asaql.png)

1. Klikněte pravým tlačítkem na soubor *Transformation. asaql* a vyberte **ASA: kompilovat skript** z nabídky.

1. Všimněte si, že se ve vašem pracovním prostoru úlohy Stream Analytics zobrazí složka pro **nasazení** .

1. Prozkoumejte *JobTemplate.jsv* souboru, což je šablona správy prostředků Azure, která se používá k nasazení.

## <a name="complete-the-parameters-file"></a>Dokončete soubor parametrů

Potom dokončete soubor parametrů šablony správy prostředků Azure.

1. Otevřete *JobTemplate.parameters.js* v souboru, který se nachází ve složce **Deploy (nasadit** ) pracovního prostoru úlohy Stream Analytics v Visual Studio Code.

1. Všimněte si, že vstupní a výstupní klíče mají hodnotu null. Nahraďte hodnoty null skutečnými přístupovými klíči pro vstupní a výstupní prostředky.

1. Uložte soubor parametrů.

## <a name="deploy-using-templates"></a>Nasazení pomocí šablon

Jste připraveni nasadit úlohu Azure Stream Analytics pomocí šablon Azure Resource Manager, které jste vygenerovali v předchozí části.

V okně PowerShellu spusťte následující příkaz. Ujistěte se, že jste reaplce *ResourceGroupName*, *TemplateFile*a *TemplateParameterFile* s vaším skutečným názvem skupiny prostředků a kompletními cestami k souboru *JobTemplate.jszapnuté* a *JobTemplate.parameters.js* se soubory ve **složce Deploy (nasadit** ) pracovního prostoru úlohy.

Pokud nemáte Azure PowerShell nakonfigurovaná, postupujte podle pokynů v části [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Další kroky

* [Testování Azure Stream Analytics úloh místně pomocí živého vstupu pomocí Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Prozkoumejte Azure Stream Analytics úlohy pomocí Visual Studio Code (Preview).](visual-studio-code-explore-jobs.md)