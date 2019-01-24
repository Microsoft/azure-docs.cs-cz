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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 93d7a6e884cf02fa41838d4a07644c122a43823b
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54823823"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Kurz: Sledování využití tohoto prostředí ve službě Azure Lab
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
4. Vyberte **uživatelé** v nabídce vlevo nebo **uživatelé** dlaždici. Zobrazí se studenti, kteří se zaregistrovali do vašeho testovacího prostředí. Můžete vybrat **Registration link** (Odkaz pro registraci), zkopírovat odkaz a odeslat ho novým studentům, kteří se do testovacího prostředí ještě nezaregistrovali. 

    ![Registrovaní uživatelé](../media/tutorial-track-usage/registered-users.png)

## <a name="view-the-usage-of-vms-in-the-lab"></a>Zobrazení využití virtuálních počítačů v testovacím prostředí 

1. V nabídce vlevo vyberte **Virtual machines** (Virtuální počítače). 
2. Ověřte, že se zobrazí stav virtuálních počítačů a počet hodin, po které jsou virtuální počítače spuštěné. Čas, který stráví vlastník testovacího prostředí na student virtuálního počítače není započítávat čas využití v posledním sloupci. 

    ![Využití virtuálních počítačů](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Správa virtuálních počítačů studentů 
Když najedete myší řádek v seznamu virtuálních počítačů, se zobrazí ovládací prvky a proveďte následující úlohy (jak je znázorněno na obrázku v předchozí části): 

- Připojení k virtuálnímu počítači
- Spuštění virtuálního počítače
- Zastavení virtuálního počítače
- Odstranění virtuálního počítače

Tlačítka panelu nástrojů můžete také ke spuštění, zastavení nebo odstranění virtuálního počítače. 



## <a name="next-steps"></a>Další postup
Další informace o testovacím prostředím v učebnách, najdete v článcích v části [provede postupy](how-to-manage-lab-accounts.md).
