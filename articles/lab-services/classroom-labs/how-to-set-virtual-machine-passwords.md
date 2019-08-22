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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645013"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>Nastavení nebo resetování hesla pro virtuální počítače v učebně Labs (instruktor)
Vlastník testovacího prostředí (učitel) může nastavit nebo resetovat heslo pro virtuální počítače v době vytvoření testovacího prostředí (Průvodce vytvořením testovacího prostředí) nebo po vytvoření testovacího prostředí (na řídicím panelu). 

## <a name="set-password-at-the-time-of-lab-creation"></a>Nastavení hesla v době vytváření testovacího prostředí
Vlastník testovacího prostředí (učitel) může nastavit heslo pro virtuální počítače v testovacím prostředí na stránce **nastavit přihlašovací údaje** v Průvodci vytvořením testovacího prostředí.

![Nastavit přihlašovací údaje](../media/tutorial-setup-classroom-lab/set-credentials.png)

Když povolíte nebo zakážete možnost **použít stejné heslo pro všechny virtuální počítače** na této stránce, učitel se může rozhodnout použít stejné heslo pro všechny virtuální počítače v testovacím prostředí nebo umožnit studentům nastavit hesla pro své virtuální počítače. Ve výchozím nastavení je toto nastavení povolené pro všechny image operačních systémů Windows a Linux s výjimkou Ubuntu. Když je toto nastavení zakázané, Student se při prvním pokusu o připojení k virtuálnímu počítači vyzve k nastavení hesla. 

Vlastník testovacího prostředí může resetovat toto heslo (v případě potřeby) na stránce **Konfigurovat šablonu** v Průvodci vytvořením testovacího prostředí. 

![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

Vlastník testovacího prostředí může také resetovat heslo po vytvoření testovacího prostředí na řídicím panelu. 

## <a name="reset-password-on-the-dashboard"></a>Resetování hesla na řídicím panelu

1. V dlaždici testovací prostředí vyberte nabídku přetečení (svislá a tři tečky) a vyberte **resetovat heslo**. 

    ![Nabídka resetovat heslo na domovské stránce](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. V dialogovém okně **nastavit heslo** zadejte heslo a vyberte **nastavit heslo**.
    
    ![Dialogové okno nastavit heslo](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Další postup
Další informace o možnostech využití studenta (jako vlastník testovacího prostředí) můžete nakonfigurovat v následujícím článku: [Nakonfigurujte využití studenta](how-to-configure-student-usage.md).

Další informace o tom, jak studenti můžou resetovat hesla pro své virtuální počítače, najdete v tématu [nastavení nebo resetování hesla pro virtuální počítače v učebně Labs (studenti)](how-to-set-virtual-machine-passwords-student.md).