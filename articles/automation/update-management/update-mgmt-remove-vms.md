---
title: Odebrání virtuálních počítačů z Azure Automation Update Management
description: Tento článek popisuje, jak odebrat počítače spravované pomocí Update Management.
services: automation
ms.topic: conceptual
ms.date: 09/09/2020
ms.custom: mvc
ms.openlocfilehash: 66631adbb56a98431e70f956f3e860b16e8f7ea2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89648642"
---
# <a name="remove-vms-from-update-management"></a>Odebrání virtuálních počítačů z Update Managementu

Až skončíte s správou aktualizací ve vašich virtuálních počítačích ve vašem prostředí, můžete zastavit správu virtuálních počítačů pomocí funkce [Update Management](update-mgmt-overview.md) . Pokud je chcete přestat spravovat, upravte uložený vyhledávací dotaz `MicrosoftDefaultComputerGroup` v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation.

## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Odebrání virtuálních počítačů

1. V Azure Portal spusťte **Cloud Shell** z horní navigace Azure Portal. Pokud neznáte Azure Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](../../cloud-shell/overview.md).

2. Pomocí následujícího příkazu Identifikujte identifikátor UUID počítače, který chcete odebrat ze správy.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. V Azure Portal přejděte na **Log Analytics pracovní prostory**. V seznamu vyberte svůj pracovní prostor.

4. V pracovním prostoru Log Analytics vyberte možnost **protokoly** a pak zvolte možnost **Průzkumník dotazů** v nabídce Top Actions (akce).

5. V pravém podokně v **Průzkumníku dotazů** rozbalte položku **uložené Queries\Updates** a vyberte uložený vyhledávací dotaz, `MicrosoftDefaultComputerGroup` který chcete upravit.

6. V editoru dotazů zkontrolujte dotaz a vyhledejte UUID pro virtuální počítač. Odeberte UUID pro virtuální počítač a opakujte postup pro všechny ostatní virtuální počítače, které chcete odebrat.

7. Uložení uložených výsledků hledání po dokončení úprav výběrem možnosti **Uložit** v horním panelu.

>[!NOTE]
>Počítače se pořád zobrazují i po jejich registraci, protože hlásíme na všech počítačích vyhodnocených za posledních 24 hodin. Po odebrání počítače je třeba počkat 24 hodin, než budou nadále vypsány.

## <a name="next-steps"></a>Další kroky

Pokud chcete znovu povolit správu virtuálního počítače, přečtěte si téma [povolení Update Management procházením Azure Portal](update-mgmt-enable-portal.md) nebo [povolením Update Management z virtuálního počítače Azure](update-mgmt-enable-vm.md).