---
title: Odebrat funkci Azure Automation Change Tracking a inventáře
description: Tento článek popisuje, jak ukončit používání Change Tracking a inventáře a zrušit propojení účtu Automation s pracovním prostorem Log Analytics.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92209816"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Odebrání Change Tracking a inventáře z účtu Automation

Po povolení správy virtuálních počítačů pomocí Azure Automation Change Tracking a inventáře se můžete rozhodnout ji přestat používat a odebrat konfiguraci z účtu a propojeného pracovního prostoru Log Analytics. V tomto článku se dozvíte, jak úplně odebrat Change Tracking a inventář ze spravovaných virtuálních počítačů, účtu Automation a pracovního prostoru Log Analytics.

## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Odebrání správy virtuálních počítačů

Před odebráním Change Tracking a inventáře je nutné nejprve zastavit správu virtuálních počítačů. V tématu [Odebrání virtuálních počítačů z Change Tracking zrušte](remove-vms-from-change-tracking.md) jejich registraci ve funkci.

## <a name="remove-changetracking-solution"></a>Odebrat řešení sledování změn ve

Než budete moct zrušit propojení účtu Automation s pracovním prostorem, je potřeba pomocí těchto kroků úplně odebrat Change Tracking a inventář. Odeberete řešení **sledování změn ve** z pracovního prostoru.

1. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Po zahájení psaní seznam vyfiltruje návrhy na základě vašeho vstupu. Vyberte **Log Analytics**.

2. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste zvolili při povolování Change Tracking a inventáře.

3. Na levé straně vyberte **řešení**.  

4. V seznamu řešení vyberte **sledování změn ve (název pracovního prostoru)**. Na stránce **Přehled** pro řešení vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **Ano**.

## <a name="unlink-workspace-from-automation-account"></a>Odpojení pracovního prostoru od účtu Automation

1. V Azure Portal vyberte **účty Automation**.

2. Otevřete účet Automation a v části **související prostředky** na levé straně vyberte **propojený pracovní prostor** .

3. Na stránce zrušit **propojení pracovního prostoru** vyberte zrušit **propojení pracovního prostoru** a reagovat na výzvy.

   ![Zrušit propojení stránky pracovního prostoru](media/remove-feature/automation-unlink-workspace-blade.png)

Při pokusu o odpojení pracovního prostoru Log Analytics můžete sledovat průběh v nabídce **oznámení** .

Případně můžete zrušit propojení pracovního prostoru Log Analytics z účtu Automation v rámci pracovního prostoru:

1. Na webu Azure Portal vyberte **Log Analytics**.

2. V pracovním prostoru vyberte **účet Automation** v části **související prostředky**.

3. Na stránce účet Automation vyberte zrušit **propojení účtu**.

Při pokusu o odpojení účtu Automation můžete sledovat průběh v nabídce **oznámení** .

## <a name="next-steps"></a>Další kroky

Pokud chcete tuto funkci znovu povolit, přečtěte si téma [povolení Change Tracking a inventáře z účtu Automation](enable-from-automation-account.md), [Povolení Change Tracking a inventáře procházením Azure Portal](enable-from-portal.md), [povolením Change Tracking a inventáře z Runbooku](enable-from-runbook.md)nebo [povolením Change Tracking a inventáře z virtuálního počítače Azure](enable-from-vm.md).
