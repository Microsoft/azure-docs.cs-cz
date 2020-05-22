---
title: Odebrání virtuálních počítačů z Azure Automation Change Tracking a inventáře
description: Tento článek popisuje, jak odebrat virtuální počítače z Change Tracking a inventáře.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 1bd94beaa40f6ff793b50e261138cc31453f7016
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749162"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Odebrání virtuálních počítačů z Change Tracking a inventáře

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

Až skončíte s nasazováním změn do virtuálních počítačů ve vašem prostředí, můžete je odebrat z funkce [Change Tracking a inventáře](change-tracking.md) .

1. Ve svém účtu Automation v části **Správa konfigurace**vyberte možnost **sledování změn** nebo **inventarizace** .

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

* [Přehled Change Tracking a inventáře](change-tracking.md)
* [Správa Change Tracking a inventáře](change-tracking-file-contents.md)
* [Zrušení propojení pracovního prostoru s účtem služby Automation pro Change Tracking a inventář](automation-unlink-workspace-change-tracking.md)
* [Povolení Change Tracking a inventáře z účtu Automation](automation-enable-changes-from-auto-acct.md)
* [Povolit Change Tracking a inventář z Azure Portal](automation-enable-changes-from-browse.md)
* [Povolení Change Tracking a inventáře z Runbooku](automation-enable-changes-from-runbook.md)
* [Povolení Change Tracking a inventáře z virtuálního počítače Azure](automation-enable-changes-from-vm.md)
* [Řešení potíží se změnami na virtuálním počítači Azure](automation-tutorial-troubleshoot-changes.md)
* [Řešení problémů s Change Tracking a inventářem](troubleshoot/change-tracking.md)