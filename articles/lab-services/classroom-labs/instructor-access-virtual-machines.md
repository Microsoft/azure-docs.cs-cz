---
title: Instruktor přístup k virtuálním mům ve službě Azure Lab Services
description: Tento článek ukazuje, jak instruktoři přístup k jejich virtuální chod studentů z zobrazení instruktora. Například asistent výuky může být instruktorem pro jednu třídu, ale studentem pro jiné třídy.
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
ms.date: 04/17/2020
ms.author: spelluru
ms.openlocfilehash: c048a2f159784a5bd38f185af29cec314b31824f
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641937"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Přístup k virtuálním strojům jako student ze zobrazení instruktora
Tento článek ukazuje, jak instruktoři přístup k jejich virtuálních stránek pro třídy, které navštěvují jako studenti. 

Zde je scénář, kde tato funkce pomůže. Asistent učitele je instruktorem pro jednu třídu, ale studentem v jiných třídách. A učitel chce zobrazit a přístup k virtuálním vyvěšením studentů z pohledu instruktora, který zobrazuje laboratoře, které vlastní. 

## <a name="access-vms-from-instructor-view"></a>Přístup k virtuálním mům ze zobrazení instruktora

1. Přihlaste se na [web Azure Lab Services](https://labs.azure.com). Vidíte ty laboratoře, které vlastníte. Tato testovací prostředí mohou být testovací prostředí, která jste sami vytvořili, nebo testovací prostředí, která vám správce přiřadil jako vlastník. Další informace naleznete v tématu [Jak přidat další vlastníky do existujícího testovacího prostředí](how-to-add-user-lab-owner.md)
2. Chcete-li získat přístup k virtuálním počítačům pro třídy, které navštěvujete jako student, vyberte ikonu počítače v pravém horním rohu. Zkontrolujte, jestli se vám můžou zobrazit virtuální hod. V následujícím příkladu je uživatel asistentem pro výuku pro testovací prostředí Pythonu, ale studentem laboratoře Java. Takže uživatel vidí virtuální ho virtuálního uživatele z laboratoře Java v rozevíracím seznamu. Uživatel může spustit virtuální hod a připojit se k němu. 
    
    ![Přístup k virtuálním virtuálním mům studentů](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Připojení k virtuálnímu počítači](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Připojení k virtuálnímu počítači pomocí rdp na Macu](connect-virtual-machine-mac-rdp.md)
- [Použití vzdálené plochy pro virtuální počítače s Linuxem](how-to-use-remote-desktop-linux-student.md)
