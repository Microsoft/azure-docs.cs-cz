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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 27d79e28a986e929fb71dd77fc50b3c2cd32618f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77134041"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Kurz: Přístup k testovacímu prostředí v učebně v Azure Lab Services
V tomto kurzu se vy, jako student, připojíte k virtuálnímu počítači v testovacím prostředí v učebně. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Zaregistrujte se do laboratoře
> * Spuštění virtuálního počítače
> * Připojení k virtuálnímu počítači

## <a name="register-to-the-lab"></a>Zaregistrujte se do laboratoře

1. Přejděte na **registrační adresu URL,** kterou jste obdrželi od profesora/pedagoga. Po dokončení registrace nemusíte používat registrační adresu URL. Místo toho použijte [https://labs.azure.com](https://labs.azure.com)adresu URL: . Internet Explorer 11 ještě není podporovaný. 
1. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 

    > [!NOTE]
    > Pro používání Azure Lab Services je potřeba účet Microsoft. Pokud se k přihlášení k portálu pokoušíte použít svůj účet, který není od Microsoftu, například Yahoo nebo Účty Google, vytvořte podle pokynů účet Microsoft, který bude propojen s vaším účtem jiného výrobce než Microsoft. Potom postupujte podle pokynů k dokončení procesu registrace. 
1. Po dokončení registrace zkontrolujte, že vidíte virtuální počítač pro testovací prostředí, ke kterému máte přístup. 
1. Počkejte, až bude virtuální počítač připraven. Na dlaždici virtuálního počítači si všimněte následujících polí:
    1. V horní části dlaždice se zobrazí **název laboratoře**.
    1. Vpravo se zobrazí ikona představující **operační systém (OS)** virtuálního soudu. V tomto příkladu je operační systém Windows. 
    1. Indikátor průběhu na dlaždici zobrazuje počet hodin použitých v porovnání s počtem [hodin kvót,](how-to-configure-student-usage.md#set-quotas-for-users) které vám byly přiřazeny. Tento čas je navíc k naplánovanému času pro laboratoř přidělený další čas. 
    1. V dolní části dlaždice se zobrazují ikony nebo tlačítka, které spouštějí nebo zastavují virtuální hospo, a připojují se k virtuálnímu virtuálnímu virtuálnímu zařízení. 
    1. Napravo od tlačítek se zobrazí stav virtuálního virtuálního soudu. Zkontrolujte, že se zobrazuje stav virtuálního virtuálního **soudu je zastaven .** 

        ![Virtuální mísa v zastaveném stavu](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Spuštění virtuálního počítače
1. **Spusťte** virtuální virtuální ho spouštění tak, že vyberete první tlačítko, jak je znázorněno na následujícím obrázku. Tento proces trvá nějakou dobu.  

    ![Spuštění virtuálního počítače](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Zkontrolujte, že je stav virtuálního virtuálního soudu nastavený na **spuštěno**. 

    ![Virtuální ms ve spuštěném stavu](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Všimněte si, že ikona prvního tlačítka se změnila, aby představovala operaci **zastavení.** Toto tlačítko můžete vybrat, chcete-li virtuální ho zastavit. 

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. Vyberte druhé tlačítko, jak je znázorněno na následujícím obrázku pro **připojení** k virtuálnímu virtuálnímu zařízení testovacího prostředí. 

    ![Připojení k virtuálnímu počítači](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Proveďte jeden z následujících kroků: 
    1. U virtuálních počítačů **se systémem Windows** uložte soubor **RDP** na pevný disk. Otevřete soubor RDP pro připojení k virtuálnímu počítači. Použijte **uživatelské jméno** a **heslo,** které získáte od svého pedagoga / profesora pro přihlášení k počítači. 
    3. Pro virtuální počítače **s Linuxem** se k nim můžete připojit pomocí **SSH** nebo **RDP** (pokud je povolen). Další informace naleznete v [tématu Povolení připojení ke vzdálené ploše pro počítače s Linuxem](how-to-enable-remote-desktop-linux.md). 

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste přistupovali k testovacímu prostředí v učebně pomocí odkazu pro registraci, který jste získali od vyučujícího.

Jako vlastník testovacího prostředí chcete zobrazit, kdo se zaregistroval v testovacím prostředí a sledovat využití virtuálních počítačů. Přejdete k dalšímu kurzu, který se dozvíte, jak sledovat využití laboratoře:

> [!div class="nextstepaction"]
> [Sledování využití testovacího prostředí](tutorial-track-usage.md) 
