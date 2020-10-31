---
title: Odebrání virtuálních počítačů z Azure Automation Change Tracking a inventáře
description: Tento článek popisuje, jak odebrat virtuální počítače z Change Tracking a inventáře.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 61ced7eda78d3ce56d78dfd6cc54e0e92d376e68
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100673"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Odebrání virtuálních počítačů z řešení Change Tracking a Inventory

Až budete hotovi sledovat změny virtuálních počítačů ve vašem prostředí, můžete je přestat spravovat pomocí funkce [Change Tracking a inventáře](overview.md) . Pokud je chcete přestat spravovat, upravte uložený vyhledávací dotaz `MicrosoftDefaultComputerGroup` v pracovním prostoru Log Analytics, který je propojený s vaším účtem Automation.

## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Odebrání virtuálních počítačů

1. V Azure Portal spusťte **Cloud Shell** z horní navigace Azure Portal. Pokud neznáte Azure Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](/azure/cloud-shell/overview).

2. Pomocí následujícího příkazu Identifikujte identifikátor UUID počítače, který chcete odebrat ze správy.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. V Azure Portal přejděte na **Log Analytics pracovní prostory** . V seznamu vyberte svůj pracovní prostor.

4. V pracovním prostoru Log Analytics vyberte možnost **protokoly** a pak zvolte možnost **Průzkumník dotazů** v nabídce Top Actions (akce).

5. V pravém podokně v **Průzkumníku dotazů** rozbalte položku **uložené Queries\Updates** a vyberte uložený vyhledávací dotaz, `MicrosoftDefaultComputerGroup` který chcete upravit.

6. V editoru dotazů zkontrolujte dotaz a vyhledejte UUID pro virtuální počítač. Odeberte UUID pro virtuální počítač a opakujte postup pro všechny ostatní virtuální počítače, které chcete odebrat.

7. Uložení uložených výsledků hledání po dokončení úprav výběrem možnosti **Uložit** v horním panelu.

>[!NOTE]
>Počítače se pořád zobrazují i po jejich registraci, protože hlásíme na všech počítačích vyhodnocených za posledních 24 hodin. Po odebrání počítače je třeba počkat 24 hodin, než budou nadále vypsány.

## <a name="next-steps"></a>Další kroky

Pokud chcete tuto funkci znovu povolit, přečtěte si téma [povolení Change Tracking a inventáře z účtu Automation](enable-from-automation-account.md), [Povolení Change Tracking a inventáře procházením Azure Portal](enable-from-portal.md), [povolením Change Tracking a inventáře z Runbooku](enable-from-runbook.md)nebo [povolením Change Tracking a inventáře z virtuálního počítače Azure](enable-from-vm.md).