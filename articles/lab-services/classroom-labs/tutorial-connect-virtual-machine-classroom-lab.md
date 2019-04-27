---
title: Přístup k testovacímu prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu budete přistupovat k virtuálním počítačům v testovacím prostředí v učebně, které nastavil vyučující.
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
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 3be1da54b16a24ce3c4431dfe26eb778cea5c83d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60694648"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Kurz: Přístup k testovacímu prostředí v učebně ve službě Azure Lab Services
V tomto kurzu se vy, jako student, připojíte k virtuálnímu počítači v testovacím prostředí v učebně. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Použití odkazu pro registraci 
> * Připojení k virtuálnímu počítači

## <a name="use-the-registration-link"></a>Použití odkazu pro registraci

1. Přejděte na **adresu URL pro registraci**, kterou jste dostali od vyučujícího. Není potřeba po dokončení registrace použijte adresa URL pro registraci. Místo toho použijte adresu URL: [ https://labs.azure.com ](https://labs.azure.com). Všimněte si, že aplikace Internet Explorer 11 se ještě nepodporuje. 
1. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 
2. Po dokončení registrace zkontrolujte, že vidíte virtuální počítač pro testovací prostředí, ke kterému máte přístup. 
3. Počkejte, až bude virtuální počítač připravený, a pak ho **spusťte**. Tento proces trvá nějakou dobu.  

    ![Spuštění virtuálního počítače](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

1. Vyberte **připojit** na dlaždici pro virtuální počítač, který chcete získat přístup k testovacím prostředí. 

    ![Připojení k virtuálnímu počítači](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Proveďte jednu z následujících kroků: 
    1. Pro **Windows** ukládat virtuální počítače, **RDP** soubor na pevný disk. Otevřete soubor RDP pro připojení k virtuálnímu počítači. Použití **uživatelské jméno** a **heslo** získáte od educator/profesor pro přihlášení k počítači. 
    3. Pro **Linux** virtuálních počítačů, můžete použít **SSH** nebo **RDP** (Pokud je povolená) pro připojení k nim. Další informace najdete v tématu [povolit připojení ke vzdálené ploše pro počítače s Linuxem](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Další postup
V tomto kurzu jste přistupovali k testovacímu prostředí v učebně pomocí odkazu pro registraci, který jste získali od vyučujícího.

Jako vlastník testovacího prostředí budete chtít zobrazit, který je zaregistrován s testovacího prostředí a sledovat využití virtuálních počítačů. Přejděte k dalšímu kurzu, přečtěte si, jak sledovat využití testovacího prostředí:

> [!div class="nextstepaction"]
> [Sledování využití testovacího prostředí](tutorial-track-usage.md) 
