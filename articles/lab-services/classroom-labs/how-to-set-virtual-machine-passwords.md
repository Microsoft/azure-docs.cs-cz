---
title: Nastavte hesla pro virtuální počítače v Azure Lab Services | Microsoft Docs
description: Naučte se, jak nastavit a resetovat hesla pro virtuální počítače v učebných laboratořích Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 99c6ee5d58655350d14add61530fae647a675508
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443223"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Nastavení a správa fondu virtuálních počítačů 
V tomto článku se dozvíte, jak provádět následující úlohy:

- Zvýšení počtu virtuálních počítačů v testovacím prostředí
- Spuštění všech virtuálních počítačů nebo vybraných virtuálních počítačů 
- Resetování virtuálních počítačů

## <a name="update-the-lab-capacity"></a>Aktualizace kapacity testovacího prostředí
Chcete-li zvýšit nebo snížit kapacitu testovacího prostředí (počet virtuálních počítačů v testovacím prostředí), proveďte následující kroky:

1. Na stránce **fond virtuálních počítačů** vyberte možnost **kapacita testovacího prostředí: &lt;číslo&gt; počítače**.
2. Zadejte nový **počet virtuálních počítačů** , které chcete v testovacím prostředí. Tento počet musí být větší nebo roven počtu uživatelů registrovaných v testovacím prostředí. 
3. Potom vyberte **Uložit**. 

    ![Tlačítko spustit vše](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Pokud jste zvýšili kapacitu, uvidíte virtuální počítač nebo virtuální počítače, které jsou vytvářeny. Pokud v seznamu nevidíte nový virtuální počítač, aktualizujte stránku. 

    ![Vytvářený virtuální počítač](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Spuštění virtuálních počítačů

### <a name="start-ot-stop-all-vms"></a>Start ot (zastavit všechny virtuální počítače)
1. Přepněte na stránku **fondu virtuálních počítačů** . 
2. Na panelu nástrojů vyberte **Spustit vše** . 

    ![Tlačítko spustit vše](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Po spuštění všech virtuálních počítačů můžete zastavit všechny virtuální počítače tak, že na panelu nástrojů vyberete tlačítko **Zastavit vše** . 

    ![Tlačítko Zastavit vše](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Spustit vybrané virtuální počítače
Existují dva způsoby, jak spustit vybrané virtuální počítače (jeden nebo více). Prvním způsobem je vybrat virtuální počítač nebo virtuální počítače v seznamu a pak na panelu nástrojů vybrat **Spustit** . 

Druhým způsobem je vybrat jeden nebo více virtuálních počítačů v seznamu a přepnout tlačítko ve sloupci **stav** . 

![Spustit vybrané virtuální počítače](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Podobně můžete zastavit jeden nebo více virtuálních počítačů přepnutím tlačítka ve sloupci **stav** nebo výběrem možnosti **zastavit** na panelu nástrojů. 

## <a name="reset-vms"></a>Resetování virtuálních počítačů
Pokud chcete resetovat jeden nebo víc virtuálních počítačů, vyberte je v seznamu a pak na panelu nástrojů vyberte **obnovit** . 

![Resetovat vybrané virtuální počítače](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

V dialogovém okně **resetovat virtuální počítače** vyberte **resetovat**. 

![Dialogové okno resetovat virtuální počítač](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Nastavení hesla pro virtuální počítače
Vlastník testovacího prostředí (učitel) může nastavit nebo resetovat heslo pro virtuální počítače v době vytvoření testovacího prostředí (Průvodce vytvořením testovacího prostředí) nebo po vytvoření testovacího prostředí na stránce **šablony** . 

### <a name="set-password-at-the-time-of-lab-creation"></a>Nastavení hesla v době vytváření testovacího prostředí
Vlastník testovacího prostředí (učitel) může nastavit heslo pro virtuální počítače v testovacím prostředí na stránce **přihlašovací údaje virtuálního počítače** v Průvodci vytvořením testovacího prostředí.

![Nové okno testovacího prostředí](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Když povolíte nebo zakážete možnost **použít stejné heslo pro všechny virtuální počítače** na této stránce, učitel se může rozhodnout použít stejné heslo pro všechny virtuální počítače v testovacím prostředí nebo umožnit studentům nastavit hesla pro své virtuální počítače. Ve výchozím nastavení je toto nastavení povolené pro všechny image operačních systémů Windows a Linux s výjimkou Ubuntu. Když je toto nastavení zakázané, Student se při prvním pokusu o připojení k virtuálnímu počítači vyzve k nastavení hesla. 

### <a name="reset-password-later"></a>Resetování hesla později

1. Na stránce **Šablona** testovacího prostředí vyberte **resetovat heslo** na panelu nástrojů. 
1. V dialogovém okně **resetovat heslo** zadejte heslo a vyberte **resetovat heslo**.
    
    ![Dialogové okno nastavit heslo](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Připojení k virtuálním počítačům studenta
Tvůrce testovacího prostředí (instruktor/profesor) se může připojit k virtuálnímu počítači studenta, pokud jsou splněné následující podmínky: 

- Při vytváření testovacího prostředí se vybrala možnost **použít stejné heslo pro všechny virtuální počítače** .
- Virtuální počítač je spuštěný. 

 Pokud se chcete připojit k virtuálnímu počítači studenta, najeďte myší na virtuální počítač v seznamu a vyberte tlačítko počítač.  

![Tlačítko připojit k virtuálnímu počítači studenta](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Když profesor spustí virtuální počítač a připojí se k němu, neovlivní se kvóta studenta. 

## <a name="next-steps"></a>Další kroky
Další informace o možnostech využití studenta (jako vlastník testovacího prostředí) můžete nakonfigurovat v následujícím článku: [Konfigurace využití studenta](how-to-configure-student-usage.md).

Další informace o tom, jak studenti můžou resetovat hesla pro své virtuální počítače, najdete v tématu [nastavení nebo resetování hesla pro virtuální počítače v učebně Labs (studenti)](how-to-set-virtual-machine-passwords-student.md).