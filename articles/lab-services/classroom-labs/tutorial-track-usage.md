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
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 315ebfa1460f9d9bc041925cec2451f63ac5be16
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580247"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Kurz: Sledování využití testovacího prostředí v Azure Lab Services
V tomto kurzu se dozvíte, jak může autor nebo vlastník testovacího prostředí sledovat jeho využití.

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Zobrazení uživatelů zaregistrovaných do testovacího prostředí
> * Zobrazení využití virtuálních počítačů v testovacím prostředí
> * Správa virtuálních počítačů studentů 


## <a name="view-users-registered-with-the-lab"></a>Zobrazení uživatelů zaregistrovaných do testovacího prostředí

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Azure Lab Services podporuje účty organizací a účty Microsoft.
3. Na stránce **My labs** (Moje testovací prostředí) vyberte testovací prostředí, jehož využití chcete sledovat. 
4. V levé nabídce nebo na dlaždici **Uživatelé** vyberte **Uživatelé** . Zobrazí se studenti, kteří se zaregistrovali do vašeho testovacího prostředí. Můžete vybrat **Registration link** (Odkaz pro registraci), zkopírovat odkaz a odeslat ho novým studentům, kteří se do testovacího prostředí ještě nezaregistrovali. 

    ![Registrovaní uživatelé](../media/tutorial-track-usage/registered-users.png)

    Další informace o přidávání a správě uživatelů pro testovací prostředí najdete v tématu [Přidání a Správa uživatelů testovacího prostředí](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms-in-the-lab"></a>Zobrazení využití virtuálních počítačů v testovacím prostředí 

1. V nabídce vlevo vyberte **Virtual machines** (Virtuální počítače). 
2. Ověřte, že se zobrazí stav virtuálních počítačů a počet hodin, po které jsou virtuální počítače spuštěné. Čas, který vlastník testovacího prostředí stráví na virtuálním počítači studenta, se nepočítá s časem využití zobrazeným v posledním sloupci. 

    ![Využití virtuálních počítačů](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Správa virtuálních počítačů studentů 
Na této stránce můžete spustit, zastavit nebo resetovat virtuální počítače studenta pomocí ovládacích prvků ve sloupci **stav** nebo na panelu nástrojů.

![Ovládací prvky virtuálního počítače](../media/tutorial-track-usage/vm-controls.png)

Další podrobnosti o správě fondu virtuálních počítačů pro testovací prostředí najdete v tématu [nastavení a Správa fondu virtuálních počítačů](how-to-set-virtual-machine-passwords.md).

## <a name="next-steps"></a>Další kroky
Další informace o učebn Labs najdete v článcích v části [Průvodce postupy](how-to-manage-lab-accounts.md).
