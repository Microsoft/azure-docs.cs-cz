---
title: Odebrání virtuálních počítačů z Azure Automation Update Management
description: Tento článek popisuje, jak odebrat počítače spravované pomocí Update Management.
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610043"
---
# <a name="remove-vms-from-update-management"></a>Odebrání virtuálních počítačů z Update Managementu

Až skončíte s nasazováním aktualizací do virtuálních počítačů ve vašem prostředí, můžete je odebrat z funkce [Update Management](automation-update-management.md) .

## <a name="to-remove-your-vms"></a>Odebrání virtuálních počítačů

1. Z účtu Automation v části **Správa aktualizací**vyberte **Správa aktualizací** .

2. Pomocí následujícího příkazu Identifikujte identifikátor UUID počítače, který chcete odebrat ze správy.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. V pracovním prostoru Log Analytics v části **Obecné**Získejte přístup k uloženým hledáním pro konfiguraci oboru `MicrosoftDefaultScopeConfig-Updates` .

4. V případě uloženého hledání `MicrosoftDefaultComputerGroup` klikněte na tlačítko se třemi tečkami vpravo a vyberte **Upravit**.

5. Odeberte UUID pro virtuální počítač.

6. Opakujte postup pro všechny ostatní virtuální počítače, které chcete odebrat.

7. Uložte uložené hledání, až budete hotovi s jeho úpravou.

>[!NOTE]
>Počítače se pořád zobrazují i po jejich registraci, protože hlásíme na všech počítačích vyhodnocených za posledních 24 hodin. Po odpojení počítače musíte počkat 24 hodin, než se dostanou do seznamu.

## <a name="next-steps"></a>Další kroky

* Pokud chcete pokračovat v práci s Update Management, přečtěte si téma [Správa aktualizací a oprav pro virtuální počítače Azure](automation-tutorial-update-management.md).
* Řešení obecných problémů s funkcemi najdete v tématu [řešení potíží s Update Management](troubleshoot/update-management.md).
* Problémy s nástrojem Windows Update Agent najdete v tématu [řešení potíží s agentem služby Windows Update](troubleshoot/update-agent-issues.md).
* Problémy s agentem aktualizace pro Linux najdete v tématu [řešení potíží s agentem aktualizací pro Linux](troubleshoot/update-agent-issues-linux.md).