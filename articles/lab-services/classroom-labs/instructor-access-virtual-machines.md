---
title: Educator přístupu k virtuálním počítačům v Azure Lab Services
description: Tento článek ukazuje, jak pedagogy mají přístup k svým virtuálním počítačům studenta ze zobrazení Educator. Výukový asistent může být například Educator pro jednu třídu, ale student pro jiné třídy.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 449ff8aafd6dec1e9c0ff16dc407155949fd1313
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83586163"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Přístup k virtuálním počítačům jako student ze zobrazení Educator
Tento článek ukazuje, jak pedagogy mají přístup ke svým virtuálním počítačům pro třídy, které se účastní jako studenti. 

Tady je scénář, kde vám tato funkce pomůže. Výukového pomocníka je Educator pro jednu třídu, ale student v jiných třídách. A Pomocník pro vzdělávání chce zobrazit a získat přístup k virtuálním počítačům studenta ze zobrazení Educator, které zobrazuje laboratoře, které vlastní. 

## <a name="access-vms-from-educator-view"></a>Přístup k virtuálním počítačům ze zobrazení Educator

1. Přihlaste se k [webu Azure Lab Services](https://labs.azure.com). Zobrazí se cvičení, která vlastníte. Tyto laboratoře můžou být Labs, které jste vytvořili sami, nebo Labs, které vám správce přiřadil jako vlastník. Další informace najdete v tématu [Postup přidání dalších vlastníků do stávajícího testovacího prostředí](how-to-add-user-lab-owner.md) .
2. Pokud chcete získat přístup k virtuálním počítačům pro třídy, které se účastníte jako student, vyberte ikonu počítač v pravém horním rohu. Ověřte, že se zobrazí virtuální počítače, ke kterým máte přístup jako student. V následujícím příkladu je uživatel Pomocník pro vzdělávání Python Lab, ale studentem Java Lab. Uživatel tak uvidí virtuální počítač z prostředí Java Lab v rozevíracím seznamu. Uživatel může virtuální počítač spustit a připojit se k němu. 
    
    ![Přístup k virtuálním počítačům studenta](../media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Připojení k virtuálnímu počítači](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Připojení k virtuálnímu počítači pomocí protokolu RDP na Macu](connect-virtual-machine-mac-rdp.md)
- [Použití vzdálené plochy pro virtuální počítače se systémem Linux](how-to-use-remote-desktop-linux-student.md)
