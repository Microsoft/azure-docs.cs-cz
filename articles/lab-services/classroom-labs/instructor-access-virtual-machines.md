---
title: Instruktor, který přistupuje k virtuálním počítačům v Azure Lab Services
description: Tento článek ukazuje, jak můžou instruktoři získat přístup k virtuálním počítačům studenta ze zobrazení instruktor. Výukový asistent může být například instruktor pro jednu třídu, ale student pro jiné třídy.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641937"
---
# <a name="access-virtual-machines-as-a-student-from-the-instructor-view"></a>Přístup k virtuálním počítačům jako student ze zobrazení instruktor
Tento článek ukazuje, jak můžou vyučující přistupovat ke svým virtuálním počítačům pro třídy, které se účastní jako studenti. 

Tady je scénář, kde vám tato funkce pomůže. Výukového pomocníka je instruktorem jedné třídy, ale studentem v jiných třídách. A vyučující učitel chce zobrazit a získat přístup k virtuálním počítačům studenta ze zobrazení instruktora, které zobrazuje laboratoře, které vlastní. 

## <a name="access-vms-from-instructor-view"></a>Přístup k virtuálním počítačům ze zobrazení instruktor

1. Přihlaste se k [webu Azure Lab Services](https://labs.azure.com). Zobrazí se cvičení, která vlastníte. Tyto laboratoře můžou být Labs, které jste vytvořili sami, nebo Labs, které vám správce přiřadil jako vlastník. Další informace najdete v tématu [Postup přidání dalších vlastníků do stávajícího testovacího prostředí](how-to-add-user-lab-owner.md) .
2. Pokud chcete získat přístup k virtuálním počítačům pro třídy, které se účastníte jako student, vyberte ikonu počítač v pravém horním rohu. Ověřte, že se zobrazí virtuální počítače, ke kterým máte přístup jako student. V následujícím příkladu je uživatel Pomocník pro vzdělávání Python Lab, ale studentem Java Lab. Uživatel tak uvidí virtuální počítač z prostředí Java Lab v rozevíracím seznamu. Uživatel může virtuální počítač spustit a připojit se k němu. 
    
    ![Přístup k virtuálním počítačům studenta](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Připojení k virtuálnímu počítači](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Připojení k virtuálnímu počítači pomocí protokolu RDP na Macu](connect-virtual-machine-mac-rdp.md)
- [Použití vzdálené plochy pro virtuální počítače se systémem Linux](how-to-use-remote-desktop-linux-student.md)
