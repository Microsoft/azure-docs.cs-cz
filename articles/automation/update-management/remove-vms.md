---
title: Odebrání virtuálních počítačů z Azure Automation Update Management
description: Tento článek popisuje, jak odebrat počítače spravované pomocí Update Management.
services: automation
ms.topic: conceptual
ms.date: 01/05/2021
ms.custom: mvc
ms.openlocfilehash: d0399aed9be8d81abb2aa55190225570ddcc1a4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97913188"
---
# <a name="remove-vms-from-update-management"></a>Odebrání virtuálních počítačů z Update Managementu

Až skončíte s správou aktualizací ve vašich virtuálních počítačích ve vašem prostředí, můžete zastavit správu virtuálních počítačů pomocí funkce [Update Management](overview.md) . Pokud je chcete přestat spravovat, upravte uložený vyhledávací dotaz `MicrosoftDefaultComputerGroup` v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation.

## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Odebrání virtuálních počítačů

1. V Azure Portal spusťte **Cloud Shell** z horní navigace Azure Portal. Pokud neznáte Azure Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](../../cloud-shell/overview.md).

2. Pomocí následujícího příkazu Identifikujte identifikátor UUID počítače, který chcete odebrat ze správy.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. V Azure Portal přejděte na **Log Analytics pracovní prostory**. V seznamu vyberte svůj pracovní prostor.

4. V pracovním prostoru Log Analytics v nabídce na levé straně vyberte **Upřesnit nastavení** a pak zvolte **skupiny počítačů** .

5. Ze **skupin počítačů** v pravém podokně vyberte **uložené skupiny**.

6. V tabulce pro aktualizace uložených dotazů hledání **: MicrosoftDefaultComputerGroup** kliknutím na ikonu **Zobrazit členy** spustíte a zobrazíte jeho členy.

7. V editoru dotazů zkontrolujte dotaz a vyhledejte UUID pro virtuální počítač. Odeberte UUID pro virtuální počítač a opakujte postup pro všechny ostatní virtuální počítače, které chcete odebrat.

8. Uložení uložených výsledků hledání po dokončení úprav výběrem možnosti **Uložit** v horním panelu. Po zobrazení výzvy zadejte následující:

    * **Název**: MicrosoftDefaultComputerGroup
    * **Uložit jako**: funkce
    * **Alias**: Updates__MicrosoftDefaultComputerGroup
    * **Kategorie**: aktualizace

>[!NOTE]
>Počítače se pořád zobrazují i po jejich registraci, protože hlásíme na všech počítačích vyhodnocených za posledních 24 hodin. Po odebrání počítače je třeba počkat 24 hodin, než budou nadále vypsány.

## <a name="next-steps"></a>Další kroky

Pokud chcete znovu povolit správu virtuálního počítače, přečtěte si téma [povolení Update Management procházením Azure Portal](enable-from-portal.md) nebo [povolením Update Management z virtuálního počítače Azure](enable-from-vm.md).