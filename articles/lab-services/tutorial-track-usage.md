---
title: Sledování využití testovacího prostředí v Azure Lab Services | Microsoft Docs
description: V tomto kurzu budete jako autor nebo vlastník testovacího prostředí sledovat jeho využití.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: 8a3ca9e011eb89b3db8b202bab11d14f10d74e7e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96434613"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Kurz: Sledování využití testovacího prostředí v Azure Lab Services
V tomto kurzu se dozvíte, jak může autor nebo vlastník testovacího prostředí sledovat jeho využití.

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Zobrazení uživatelů zaregistrovaných do testovacího prostředí
> * Zobrazení využití virtuálních počítačů v testovacím prostředí
> * Správa virtuálních počítačů studentů 


## <a name="view-registered-users"></a>Zobrazit registrované uživatele

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft.
3. Na stránce **My labs** (Moje testovací prostředí) vyberte testovací prostředí, jehož využití chcete sledovat. 
4. V levé nabídce nebo na dlaždici **Uživatelé** vyberte **Uživatelé** . Zobrazí se studenti, kteří se zaregistrovali do vašeho testovacího prostředí.  

    ![Registrovaní uživatelé](./media/tutorial-track-usage/registered-users.png)

    Další informace o přidávání a správě uživatelů pro testovací prostředí najdete v tématu [Přidání a Správa uživatelů testovacího prostředí](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Zobrazit využití virtuálních počítačů

1. V nabídce vlevo vyberte **Virtual machines** (Virtuální počítače). 
2. Ověřte, že se zobrazí stav virtuálních počítačů a počet hodin, po které jsou virtuální počítače spuštěné. Čas, který vlastník testovacího prostředí stráví na virtuálním počítači studenta, se nepočítá s časem využití zobrazeným v posledním sloupci. 

    ![Využití virtuálních počítačů](./media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Správa virtuálních počítačů studentů 
Na této stránce můžete spustit, zastavit nebo resetovat virtuální počítače studenta pomocí ovládacích prvků ve sloupci **stav** nebo na panelu nástrojů.

![Akce virtuálních počítačů](./media/tutorial-track-usage/vm-controls.png)

Další informace o správě fondu virtuálních počítačů pro testovací prostředí najdete v tématu [nastavení a Správa fondu virtuálních počítačů](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> Když Educator zapnete virtuální počítač studenta, neovlivní kvóta pro studenta. Kvóta pro uživatele určuje počet hodin testovacího prostředí uživatele mimo plánovaný čas třídy. Další informace o kvótách najdete v tématu [nastavení kvót pro uživatele](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Další kroky
Další informace o Labs najdete v článcích v části [Průvodce postupy](how-to-manage-lab-accounts.md).
