---
title: Odebrání virtuálních počítačů z Azure Automation Update Management
description: Tento článek popisuje, jak odebrat virtuální počítače z Update Management.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836647"
---
# <a name="remove-vms-from-update-management"></a>Odebrání virtuálních počítačů z Update Managementu

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

* Pokud chcete pokračovat v práci s Update Management, přečtěte si téma [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
* Řešení obecných problémů s funkcemi najdete v tématu [řešení potíží s Update Management](troubleshoot/update-management.md).
* Problémy s nástrojem Windows Update Agent najdete v tématu [řešení potíží s agentem služby Windows Update](troubleshoot/update-agent-issues.md).
* Problémy s agentem aktualizace pro Linux najdete v tématu [řešení potíží s agentem aktualizací pro Linux](troubleshoot/update-agent-issues-linux.md).