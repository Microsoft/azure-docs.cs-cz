---
title: Nastavení hesel pro virtuální počítače ve službách Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nastavit a resetovat hesla pro virtuální počítače (VM) v učebních testovacích prostředích Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 0ff464936025a20cb6925adc7ef6eb44c2fe1f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78933803"
---
# <a name="set-up-and-manage-virtual-machine-pool"></a>Nastavení a správa fondu virtuálních počítačů 
Tento článek ukazuje, jak provést následující úkoly:

- Zvýšení počtu virtuálních počítačů (VM) v testovacím prostředí
- Spuštění všech virtuálních discích nebo vybraných virtuálních mích 
- Resetovat virtuální ms

## <a name="update-the-lab-capacity"></a>Aktualizace kapacity laboratoře
Chcete-li zvýšit nebo snížit kapacitu testovacího prostředí (počet virtuálních počítačů v testovacím prostředí), postupujte takto:

1. Na stránce **Fond virtuálních počítačů** vyberte **Kapacita laboratoře: &lt;počet&gt; počítačů**.
2. Zadejte nový **počet virtuálních discích,** které chcete v testovacím prostředí. Toto číslo musí být větší nebo rovno počtu uživatelů registrovaných v testovacím prostředí. 
3. Potom vyberte **Uložit**. 

    ![Tlačítko Spustit vše](../media/how-to-set-virtual-machine-passwords/number-of-vms-in-lab.png)
4. Pokud jste zvýšili kapacitu, uvidíte, že se vytvářejí virtuální hospořitel nebo virtuálních můřic. Pokud nový virtuální počítače v seznamu nevidíte, aktualizujte stránku. 

    ![Vytváření virtuálního virtuálního montovny](../media/how-to-set-virtual-machine-passwords/vm-being-created.png)

## <a name="start-vms"></a>Spuštění virtuálních montovních

### <a name="start-ot-stop-all-vms"></a>Spuštění ot zastavit všechny virtuální chod
1. Přepněte na stránku **fondu virtuálních strojů.** 
2. Na panelu nástrojů vyberte **Spustit vše.** 

    ![Tlačítko Spustit vše](../media/how-to-set-virtual-machine-passwords/start-all-vms-button.png)
3. Po spuštění všech virtuálních počítačů můžete zastavit všechny virtuální počítače výběrem tlačítka **Zastavit vše** na panelu nástrojů. 

    ![Tlačítko Zastavit vše](../media/how-to-set-virtual-machine-passwords/stop-all-vms-button.png)

### <a name="start-selected-vms"></a>Spuštění vybraných virtuálních ms
Vybrané virtuální hody lze spustit dvěma způsoby (jeden nebo více). První způsob je vybrat virtuální počítač nebo virtuální počítač v seznamu a pak vybrat **Start** na panelu nástrojů. 

Druhým způsobem je vybrat jeden nebo více virtuálních virtuálních discích v seznamu a přepnout tlačítko ve sloupci **Stav.** 

![Spuštění vybraných virtuálních ms](../media/how-to-set-virtual-machine-passwords/start-selected-vms.png)

Podobně můžete zastavit jeden nebo více virtuálních počítačů přepnutím tlačítka ve sloupci **Stav** nebo výběrem **stop** na panelu nástrojů. 

> [!NOTE]
> Když pedagog zapne virtuální počítač pro studenty, kvóta pro studenta není ovlivněna. Kvóta pro uživatele určuje počet hodin testovacího prostředí, které má uživatel k dispozici mimo plánovaný čas třídy. Další informace o kvótách naleznete v tématu [Nastavení kvót pro uživatele](how-to-configure-student-usage.md?#set-quotas-for-users).

## <a name="reset-vms"></a>Resetovat virtuální ms
Pokud chcete resetovat jeden nebo více virtuálních počítačů, vyberte je v seznamu a pak na panelu nástrojů vyberte **Obnovit.** 

![Obnovení vybraných virtuálních montovek](../media/how-to-set-virtual-machine-passwords/reset-vm-button.png)

V dialogovém okně **Obnovit virtuální počítače** vyberte **Obnovit**. 

![Dialogové okno Obnovit virtuální virtuální mísu](../media/how-to-set-virtual-machine-passwords/reset-vms-dialog.png)



## <a name="set-password-for-vms"></a>Nastavení hesla pro virtuální počítače
Vlastník testovacího prostředí (učitel) můžete nastavit nebo obnovit heslo pro virtuální počítače v době vytvoření testovacího prostředí (průvodce vytvořením testovacího prostředí) nebo po vytvoření testovacího prostředí na stránce **Šablona.** 

### <a name="set-password-at-the-time-of-lab-creation"></a>Nastavení hesla v době vytvoření testovacího prostředí
Vlastník testovacího prostředí (učitel) můžete nastavit heslo pro virtuální počítače v testovacím prostředí na stránce **přihlašovací údaje virtuálního počítače** průvodce vytvořením testovacího prostředí.

![Nové okno laboratoře](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

Povolením nebo zakázáním **možnosti Použít stejné heslo pro všechny virtuální počítače** na této stránce může učitel zvolit použití stejného hesla pro všechny virtuální počítače v testovacím prostředí nebo umožnit studentům nastavit hesla pro své virtuální počítače. Ve výchozím nastavení je toto nastavení povoleno pro všechny bitové kopie operačního systému Windows a Linux s výjimkou Ubuntu. Pokud je toto nastavení zakázáno, studenti budou vyzváni k nastavení hesla při pokusu o připojení k virtuálnímu počítače poprvé. 

### <a name="reset-password-later"></a>Resetovat heslo později

1. Na stránce **Šablona** v testovacím prostředí vyberte na panelu nástrojů **možnost Obnovit heslo.** 
1. V dialogovém okně **Obnovit heslo** zadejte heslo a vyberte **Obnovit heslo**.
    
    ![Dialogové okno Nastavit heslo](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="connect-to-student-vms"></a>Připojení k virtuálním virtuálním zařízení pro studenty
Tvůrce testovacího prostředí (instruktor/profesor) se může připojit k virtuálnímu virtuálnímu virtuálnímu zařízení studenta, pokud jsou splněny následující podmínky: 

- Při vytváření testovacího prostředí byla vybrána možnost **Použít stejné heslo pro všechny virtuální počítače.**
- Virtuální virtuální mísa je spuštěná 

 Chcete-li se připojit k virtuálnímu počítači studenta, najeďte myší na virtuální počítač v seznamu a vyberte tlačítko počítače.  

![Tlačítko Připojit se k virtuálnímu virtuálnímu virtuálnímu zařízení pro studenty](../media/how-to-set-virtual-machine-passwords/connect-student-vm.png)

> [!NOTE]
> Když profesor spustí virtuální hod a připojí se k němu, kvóta studenta není ovlivněna. 

## <a name="export-list-of-virtual-machines-to-a-csv-file"></a>Export seznamu virtuálních počítačů do souboru CSV

1. Přepněte na kartu **Fond virtuálních strojů.**
2. Vyberte **...** (tři tečky) na panelu nástrojů a pak vyberte **Exportovat CSV**. 

    ![Export seznamu virtuálních počítačů](../media/how-to-export-users-virtual-machines-csv/virtual-machines-export-csv.png)

## <a name="next-steps"></a>Další kroky
Další informace o dalších možnostech využití studentů, které můžete (jako vlastník testovacího prostředí) nakonfigurovat, naleznete v následujícím článku: [Konfigurace využití studentů](how-to-configure-student-usage.md).

Informace o tom, jak můžou studenti resetovat hesla pro své virtuální počítače, najdete v tématu [Nastavení nebo resetování hesla pro virtuální počítače v učebních laboratořích (studenti).](how-to-set-virtual-machine-passwords-student.md)