---
title: Export úlohy Azure Stream Analytics Azure Resource Manager šablony
description: Tento článek popisuje, jak exportovat šablonu Azure Resource Manager pro úlohu Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 52ea7b45d0dcdb3ae16b8212557ba6ab3344ff15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968949"
---
# <a name="export-an-azure-stream-analytics-job-azure-resource-manager-template"></a>Export úlohy Azure Stream Analytics Azure Resource Manager šablony

[Šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) umožnou implementovat infrastrukturu jako kód. Šablona je soubor Zápisu objektu JavaScriptu (JSON), který definuje infrastrukturu a konfiguraci vašich prostředků. Zadáte prostředky k nasazení a vlastnosti pro tyto prostředky.

Úlohu Azure Stream Analytics můžete znovu nasadit exportem šablony Azure Resource Manager.

## <a name="open-a-job-in-vs-code"></a>Otevření úlohy v kódu VS

Před exportem šablony musíte nejprve otevřít existující úlohu Stream Analytics v kódu Visual Studia. 

Pokud chcete exportovat úlohu do místního projektu, vyhledejte úlohu, kterou chcete exportovat v **Průzkumníku analýzy streamu** na webu Azure Portal. Na stránce **Dotaz** vyberte **Otevřít v sadě Visual Studio**. Pak vyberte **Visual Studio Code**.

![Úloha Open Stream Analytics v kódu Visual Studia](./media/resource-manager-export/open-job-vs-code.png)

Další informace o použití kódu sady Visual Studio ke správě úloh Služby Stream Analytics najdete v [tématu Rychlý start kódu sady Visual Studio](quick-create-vs-code.md).

## <a name="compile-the-script"></a>Kompilace skriptu 

Dalším krokem je kompilace skriptu úlohy do šablony Azure Resource Manager. Před kompilací skriptu se ujistěte, že vaše úloha má alespoň jeden vstup a jeden výstup nakonfigurován. Pokud není nakonfigurován žádný vstup nebo výstup, je třeba nejprve nakonfigurovat vstup a výstup.

1. V kódu sady Visual Studio přejděte do souboru *Transform.asaql* vaší úlohy.

   ![Soubor Transformation.asaql v kódu sady Visual Studio](./media/resource-manager-export/transformation-asaql.png)

1. Klepněte pravým tlačítkem myši na soubor *Transformation.asaql* a z nabídky vyberte **příkaz ASA: Kompilovat skript.**

1. Všimněte si, že se složka **Deploy** zobrazí v pracovním prostoru úloh YV Stream Analytics.

1. Prozkoumejte soubor *JobTemplate.json,* což je šablona Azure Resource Management používaná k nasazení.

## <a name="complete-the-parameters-file"></a>Dokončení souboru parametrů

Dále dokončete soubor parametrů šablony Azure Resource Management.

1. Otevřete soubor *JobTemplate.parameters.json* umístěný ve složce **Deploy** pracovního prostoru úlohy Stream Analytics v kódu sady Visual Studio.

1. Všimněte si, že vstupní a výstupní klíče jsou null. Nahraďte hodnoty null skutečnými přístupovými klíči pro vstupní a výstupní prostředky.

1. Uložte soubor parametrů.

## <a name="deploy-using-templates"></a>Nasazení pomocí šablon

Jste připraveni nasadit úlohu Azure Stream Analytics pomocí šablon Azure Resource Manager, které jste vygenerovali v předchozí části.

V okně Prostředí PowerShell spusťte následující příkaz. Nezapomeňte reaplce *ResourceGroupName*, *TemplateFile*a *TemplateParameterFile* s názvem skutečné skupiny prostředků a úplné cesty k souborům *JobTemplate.json* a *JobTemplate.parameters.json* v **pracovním** prostoru Nasazení úlohy.

Pokud nemáte nakonfigurovaný Azure PowerShell, postupujte podle pokynů v [modulu Instalace Prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName "<your resource group>" -TemplateFile "<path to JobTemplate.json>" -TemplateParameterFile "<path to JobTemplate.parameters.json>"
```

## <a name="next-steps"></a>Další kroky

* [Testování úloh Azure Stream Analytics místně pomocí živého vstupu pomocí kódu Visual Studia](visual-studio-code-local-run-live-input.md)

* [Prozkoumejte úlohy Azure Stream Analytics pomocí kódu Visual Studia (Preview)](visual-studio-code-explore-jobs.md)