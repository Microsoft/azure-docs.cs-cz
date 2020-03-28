---
title: Sledování využití testovacího prostředí v Azure Lab Services | Microsoft Docs
description: V tomto kurzu budete jako autor nebo vlastník testovacího prostředí sledovat jeho využití.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: a1a3e62646fedd468a02eac7b1a48d0b2d00fd74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77591978"
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
4. V levé nabídce nebo **na dlaždici Uživatelé** vyberte **Uživatelé.** Zobrazí se studenti, kteří se zaregistrovali do vašeho testovacího prostředí.  

    ![Registrovaní uživatelé](../media/tutorial-track-usage/registered-users.png)

    Další informace o přidávání a správě uživatelů testovacího prostředí naleznete v tématu [Přidání a správa uživatelů testovacího prostředí](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms"></a>Zobrazení využití virtuálních počítače

1. V nabídce vlevo vyberte **Virtual machines** (Virtuální počítače). 
2. Ověřte, že se zobrazí stav virtuálních počítačů a počet hodin, po které jsou virtuální počítače spuštěné. Čas, který vlastník testovacího prostředí stráví na virtuálním počítači studenta se nezapočítává do doby využití uvedené v posledním sloupci. 

    ![Využití virtuálních počítačů](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Správa virtuálních počítačů studentů 
Na této stránce můžete spustit, zastavit nebo obnovit virtuální chod studentů pomocí ovládacích prvků ve sloupci **Stav** nebo na panelu nástrojů.

![Akce virtuálních montovna](../media/tutorial-track-usage/vm-controls.png)

Další informace o správě fondu virtuálních strojů pro testovací prostředí najdete v tématu [Nastavení a správa fondu virtuálních strojů](how-to-set-virtual-machine-passwords.md).

> [!NOTE]
> Když pedagog zapne virtuální počítač pro studenty, kvóta pro studenta není ovlivněna. Kvóta pro uživatele určuje počet hodin testovacího prostředí, které má uživatel k dispozici mimo plánovaný čas třídy. Další informace o kvótách naleznete v tématu [Nastavení kvót pro uživatele](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="next-steps"></a>Další kroky
Další informace o učebních laboratořích najdete v článcích v článku [Návody .](how-to-manage-lab-accounts.md)
