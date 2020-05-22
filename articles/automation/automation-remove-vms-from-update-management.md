---
title: Odebrání virtuálních počítačů z Azure Automation Update Management
description: Tento článek popisuje, jak odebrat virtuální počítače z Update Management.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 29d6edd5463de78bba039e4ed6219575d924ac10
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749141"
---
# <a name="remove-vms-from-update-management"></a>Odebrat virtuální počítače z Update Management

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Až skončíte s nasazováním aktualizací do virtuálních počítačů ve vašem prostředí, můžete je odebrat z funkce [Update Management](automation-update-management.md) .

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Pomocí následujícího příkazu Identifikujte identifikátor UUID virtuálního počítače, který chcete odebrat ze správy.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. V pracovním prostoru Log Analytics v části **Obecné**Získejte přístup k uloženým hledáním.

4. V případě uloženého hledání `MicrosoftDefaultComputerGroup` klikněte na tlačítko se třemi tečkami vpravo a vyberte **Upravit**. 

5. Odeberte UUID pro virtuální počítač.

6. Opakujte postup pro všechny ostatní virtuální počítače, které chcete odebrat.

7. Uložte uložené hledání, až budete hotovi s jeho úpravou. 

## <a name="next-steps"></a>Další kroky

* [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md)
* [Zrušit propojení pracovního prostoru s účtem služby Automation pro Update Management](automation-unlink-workspace-update-management.md)
* [Povolení Update Management z účtu Automation](automation-onboard-solutions-from-automation-account.md)
* [Povolit Update Management z Azure Portal](automation-onboard-solutions-from-browse.md)
* [Povolení Update Management ze sady Runbook](automation-onboard-solutions.md)
* [Povolení Update Management z virtuálního počítače Azure](automation-onboard-solutions-from-vm.md)
* [Řešení potíží s Update Management](troubleshoot/update-management.md)
* [Řešení potíží s agentem Windows Update](troubleshoot/update-agent-issues.md)
* [Řešení potíží s agentem aktualizace pro Linux](troubleshoot/update-agent-issues-linux.md)
