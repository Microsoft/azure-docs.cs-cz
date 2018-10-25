---
title: Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu budete přistupovat k virtuálním počítačům v testovacím prostředí v učebně, kterou nastavil vyučující.
services: devtest-lab, lab-services, virtual-machines
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
ms.date: 10/17/2018
ms.author: spelluru
ms.openlocfilehash: 4b137396dd6a8ff924c9380aeb87a81b95f91414
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466218"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services
Tento článek popisuje, jak získat přístup k testovacímu prostředí v učebně, připojit se k virtuálnímu počítači v daném testovacím prostředí a zastavit daný virtuální počítač. 

## <a name="view-all-the-classroom-labs"></a>Zobrazit všechna testovací prostředí v učebně

1. Přejděte na **adresu URL pro registraci**, kterou jste dostali od vyučujícího. 
2. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 
3. Po dokončení registrace zkontrolujte, že vidíte virtuální počítače pro testovací prostředí, ke kterým máte přístup. 

    ![Zobrazit všechna testovací prostředí](../media/how-to-use-classroom-lab/all-labs.png)

## <a name="connect-to-the-virtual-machine-in-a-classroom-lab"></a>Připojení k virtuálnímu počítači v testovacím prostředí v učebně

1. Na dlaždici, která představuje virtuální počítač v testovacím prostředí, ke kterému chcete získat přístup, vyberte **Připojit**.

    ![Zobrazit všechna testovací prostředí](../media/how-to-use-classroom-lab/connect-button.png)
2. Příprava virtuálního počítače trvá několik minut.

    ![Příprava virtuálního počítače](../media/how-to-use-classroom-lab/getting-virtual-machine-ready.png)
3. Uložte soubor RDP na pevný disk a otevřete ho. 
    
    ![Uložení souboru RDP](../media/how-to-use-classroom-lab/save-rdp-file.png)
4. Pro přihlášení k počítači použijte **uživatelské jméno** a **heslo**, která jste dostali od vyučujícího. 

## <a name="stop-the-virtual-machine-in-a-classroom-lab"></a>Zastavení virtuálního počítače v testovacím prostředí v učebně

Na dlaždici, která představuje testovací prostředí v učebně, vyberte **Zastavit**. Po zastavení virtuálního počítače se na dané dlaždici zpřístupní tlačítko **Spustit**. 

## <a name="next-steps"></a>Další kroky
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](how-to-manage-classroom-labs.md)
- [Nastavení testovacího prostředí](../tutorial-create-custom-lab.md)

 