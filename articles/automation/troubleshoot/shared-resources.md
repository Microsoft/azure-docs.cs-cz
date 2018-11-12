---
title: Řešení potíží s Azure Automation sdílené prostředky
description: Zjistěte, jak řešit potíže s prostředky Azure Automation, které jsou sdílené s
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 11/05/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 385d2969e65647ab0b5c5e21c07b127104587e7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51263558"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Řešení potíží s chybami se sdílenými prostředky

Tento článek popisuje řešení Chcete-li vyřešit problémy, které můžete spouštět napříč při používání sdílených prostředků ve službě Azure Automation.

## <a name="modules"></a>Moduly

### <a name="module-stuck-importing"></a>Scénář: Modul se zasekne importu

#### <a name="issue"></a>Problém

Při importu nebo aktualizovat moduly ve službě Azure automation, vyhledejte modul, který se zasekla v automatickém **import** stavu.

#### <a name="error"></a>Chyba

Import modulů Powershellu je komplexní vícefázový proces. Tento proces představuje možnost Modul není správně importu. Pokud k tomu dojde, můžete modul, který importujete zaseknout v přechodovém stavu. Další informace o tomto procesu najdete v tématu [importování modulu prostředí PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Řešení

Chcete-li vyřešit tento problém, musíte odebrat modul, který se zasekla v automatickém **import** stavu pomocí [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) rutiny. Můžete pak opakujte import modulu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.