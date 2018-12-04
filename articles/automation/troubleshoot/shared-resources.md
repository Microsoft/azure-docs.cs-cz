---
title: Řešení potíží s Azure Automation sdílené prostředky
description: Zjistěte, jak řešit potíže s prostředky Azure Automation, které jsou sdílené s
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 12/3/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: ce78c86cdae9a06100fd17d00e0229805e42983b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848455"
---
# <a name="troubleshoot-errors-with-shared-resources"></a>Řešení potíží s chybami se sdílenými prostředky

Tento článek popisuje řešení Chcete-li vyřešit problémy, které můžete spouštět napříč při používání sdílených prostředků ve službě Azure Automation.

## <a name="modules"></a>Moduly

### <a name="module-stuck-importing"></a>Scénář: Modul se zasekne importu

#### <a name="issue"></a>Problém

Modul se zasekla v automatickém **import** stavu při importu nebo aktualizovat moduly ve službě Azure automation.

#### <a name="cause"></a>Příčina

Import modulů Powershellu je komplexní vícefázový proces. Tento proces představuje možnost Modul není správně importu. Pokud k tomuto problému dochází, můžete modul, který importujete zaseknout v přechodovém stavu. Další informace o tomto procesu najdete v tématu [importování modulu prostředí PowerShell]( /powershell/developer/module/importing-a-powershell-module#the-importing-process).

#### <a name="resolution"></a>Řešení

Chcete-li vyřešit tento problém, musíte odebrat modul, který se zasekla v automatickém **import** stavu pomocí [Remove-AzureRmAutomationModule](/powershell/module/azurerm.automation/remove-azurermautomationmodule) rutiny. Můžete pak opakujte import modulu.

```azurepowershell-interactive
Remove-AzureRmAutomationModule -Name ModuleName -ResourceGroupName ExampleResourceGroup -AutomationAccountName ExampleAutomationAccount -Force
```

## <a name="run-as-accounts"></a>Účty spustit jako

### <a name="unable-create-update"></a>Scénář: Nemůžete vytvořit nebo aktualizovat účet Spustit jako

#### <a name="issue"></a>Problém

Při pokusu o vytvoření nebo aktualizace účtu spustit jako, zobrazí se chybová zpráva podobná následující chybová zpráva:

```error
You do not have permissions to create…
```

#### <a name="cause"></a>Příčina

Na úrovni skupiny prostředků nemáte příslušná oprávnění, které je potřeba vytvořit nebo aktualizovat účet Spustit jako nebo prostředek je uzamčený.

#### <a name="resolution"></a>Řešení

Vytvořit nebo aktualizovat účet Spustit jako, musí mít příslušná oprávnění k různým prostředkům používá účet Spustit jako. Další informace o oprávnění potřebná k vytvoření nebo aktualizace účtu spustit jako najdete v tématu [oprávnění účtu spustit jako](../manage-runas-account.md#permissions).

Pokud je problém způsobený zámek, ověřte, zda je zámek ok můžete odebrat a přejít na prostředek, který je uzamčen, klikněte pravým tlačítkem na zámek a zvolte **odstranit** odebrat zámek.

## <a name="next-steps"></a>Další postup

Pokud nezobrazila váš problém nebo nelze vyřešit vaše potíže, navštíví některý z následujících kanálů pro další podporu:

* Získejte odpovědi od odborníků na Azure prostřednictvím [fór Azure](https://azure.microsoft.com/support/forums/).
* Spojte se s [@AzureSupport](https://twitter.com/azuresupport). Tento oficiální účet Microsoft Azure pomáhá vylepšovat uživatelské prostředí tím, že propojuje komunitu Azure s vhodnými zdroji: odpověďmi, podporou a odborníky.
* Pokud potřebujete další pomoc, můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu**.