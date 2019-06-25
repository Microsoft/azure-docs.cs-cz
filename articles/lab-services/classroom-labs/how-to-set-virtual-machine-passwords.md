---
title: Nastavení hesla pro virtuální počítače v Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak nastavit a resetování hesla pro virtuální počítače (VM) v testovacím prostředím v učebnách Azure Lab Services.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: c1564fadef35a20d0d87db8439ae1cc3dc923318
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67144111"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs"></a>Nastavit nebo obnovit heslo pro virtuální počítače v testovacím prostředím v učebnách
Tento článek popisuje různé možnosti nastavení a resettings hesel pro přístup k virtuálním počítačům v testovacím prostředím v učebnách. 

## <a name="lab-owners-teachers"></a>Vlastníků testovacího prostředí (učitele)
Vlastník testovacího prostředí (učitele) můžete sadu/resetování hesla pro virtuální počítače v době vytvoření testovacího prostředí (Průvodce vytvořením testovacího prostředí) nebo po vytvoření testovacího prostředí (na řídicí panel). 

### <a name="set-password-at-the-time-of-lab-creation"></a>Nastavení hesla v době vytvoření testovacího prostředí
Vlastník testovacího prostředí (učitele) můžete nastavit heslo pro virtuální počítače v testovacím prostředí na **nastavit přihlašovací údaje** stránky Průvodce vytvořením testovacího prostředí.

![Nastavení přihlašovacích údajů](../media/tutorial-setup-classroom-lab/set-credentials.png)

Tím, že povolíte nebo zakážete **použít stejné heslo pro všechny virtuální počítače** možnosti na této stránce učitel můžete použít stejné heslo pro všechny virtuální počítače v testovacím prostředí nebo povolit studenty k nastavení hesla pro své virtuální počítače. Ve výchozím nastavení je toto nastavení povolené pro všechny Windows a Linuxem bitové kopie operačního systému s výjimkou Ubuntu. Pokud je toto nastavení zakázáno, studenti se vyzve k nastavení hesla při pokusu o připojení k virtuálnímu počítači poprvé. 

Vlastník testovacího prostředí můžete resetovat heslo (v případě potřeby) na **konfigurovat šablony** stránky Průvodce vytvořením testovacího prostředí. 

![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Vlastník testovacího prostředí můžou také resetovat hesla, po vytvoření testovacího prostředí, na řídicím panelu. 

### <a name="reset-password-on-the-dashboard"></a>Resetovat heslo na řídicím panelu

1. Vyberte nabídky přetečení (svislé tři tečky) na dlaždici lab a pak vyberte **resetovat heslo**. 

    ![Resetovat heslo nabídky na domovské stránce](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Na **nastavit heslo** dialogové okno, zadejte heslo a vyberte **nastavit heslo**.
    
    ![Dialogové okno nastavit heslo](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="lab-users-students"></a>Uživatelé testovacího prostředí (studenti)
V době vytvoření testovacího prostředí, můžete povolit nebo zakázat vlastník testovacího prostředí **použít stejné heslo pro všechny virtuální počítače**. Pokud je tato možnost povolena, studenty nelze resetovat heslo. Všechny virtuální počítače v Tato praktická cvičení bude mít stejné heslo, které nastavuje učitele. 

Pokud tato možnost je zakázaná, uživatelé budou muset nastavit heslo při pokusu o připojení k virtuálnímu počítači poprvé. Když uživatelé (studenti) vyberte **připojit** tlačítko na dlaždici testovacího prostředí **Moje virtual machines** stránky, uživateli se zobrazí následující dialogové nastavit heslo pro virtuální počítač: 

![Resetování hesla pro studenta](../media/how-to-set-virtual-machine-passwords/student-set-password.png)

Student také můžete nastavit heslo kliknutím na nabídku přetečení (**tří svislých teček**) na dlaždici testovacího prostředí a vyberete **resetovat heslo**. 

## <a name="next-steps"></a>Další postup
Další informace o další možnosti použití student vám (jako vlastník testovacího prostředí) můžete konfigurovat, najdete v následujícím článku: [Konfigurace využití student](how-to-configure-student-usage.md).
