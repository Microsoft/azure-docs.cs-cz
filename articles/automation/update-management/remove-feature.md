---
title: Odebrat funkci Azure Automation Update Management
description: V tomto článku se dozvíte, jak ukončit používání Update Management a zrušit propojení účtu Automation s pracovním prostorem Log Analytics.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 1d83f859fce33b9499d01c4b58e69f56fdbbb293
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222241"
---
# <a name="remove-update-management-from-automation-account"></a>Odebrat Update Management z účtu Automation

Po povolení správy aktualizací na virtuálních počítačích pomocí Azure Automation Update Management se můžete rozhodnout ji přestat používat a odebrat konfiguraci z účtu a propojeného pracovního prostoru Log Analytics.  V tomto článku se dozvíte, jak úplně odebrat Update Management ze spravovaných virtuálních počítačů, účtu Automation a pracovního prostoru Log Analytics.

## <a name="sign-into-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Odebrání správy virtuálních počítačů

Před odebráním Update Management musíte nejdřív přestat spravovat vaše virtuální počítače. V tématu [Odebrání virtuálních počítačů z Update Management zrušte](remove-vms.md) jejich registraci ve funkci.

## <a name="remove-updatemanagement-solution"></a>Odebrat řešení UpdateManagement

Než budete moct zrušit propojení účtu Automation s pracovním prostorem, je potřeba pomocí těchto kroků úplně odebrat Update Management. Odeberete řešení **aktualizace** z pracovního prostoru.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

2. V Azure Portal vyberte **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Po zahájení psaní seznam vyfiltruje návrhy na základě vašeho vstupu. Vyberte **Log Analytics**.

3. V seznamu pracovních prostorů Log Analytics vyberte pracovní prostor, který jste zvolili, když jste povolili Update Management.

4. Na levé straně vyberte **řešení**.  

5. V seznamu řešení vyberte **aktualizace (název pracovního prostoru)**. Na stránce **Přehled** pro řešení vyberte **Odstranit**. Po zobrazení výzvy k potvrzení vyberte **Ano**.

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

## <a name="cleanup-automation-account"></a>Vyčištění účtu Automation

Pokud byla Update Management nakonfigurovaná tak, aby podporovala starší verze monitorování Azure SQL, mohla by instalace této funkce vytvořit prostředky automatizace, které byste měli odebrat. V případě Update Management možná budete chtít odebrat následující položky, které už nepotřebujete:

   * Plány aktualizací – každý má název, který odpovídá vytvořenému nasazení aktualizace.
   * Skupiny hybridních pracovních procesů vytvořené pro Update Management – každá je pojmenována podobně jako *machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8)*.

## <a name="next-steps"></a>Další kroky

Pokud chcete tuto funkci znovu povolit, přečtěte si téma [povolení Update Management z účtu Automation](enable-from-automation-account.md), [Povolení Update Management procházením Azure Portal](enable-from-portal.md), [povolením Update Management ze sady Runbook](enable-from-runbook.md)nebo [povolením Update Management z virtuálního počítače Azure](enable-from-vm.md).
