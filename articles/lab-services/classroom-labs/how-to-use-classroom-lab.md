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
ms.date: 08/19/2019
ms.author: spelluru
ms.openlocfilehash: 8987a22d4546fe887d1dda9adff8f46afe667618
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69644992"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services
Tento článek popisuje, jak se zaregistrovat do testovacího prostředí, Zobrazit všechny laboratoře, ke kterým máte přístup, jak spustit nebo zastavit virtuální počítač v testovacím prostředí a jak se připojit k virtuálnímu počítači. 

## <a name="register-to-the-lab"></a>Registrovat do testovacího prostředí

1. Přejděte na **adresu URL pro registraci**, kterou jste dostali od vyučujícího. Po dokončení registrace nemusíte používat registrační adresu URL. Místo toho použijte adresu URL: [https://labs.azure.com](https://labs.azure.com). Aplikace Internet Explorer 11 není zatím podporována. 
1. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 
2. Po dokončení registrace zkontrolujte, že vidíte virtuální počítač pro testovací prostředí, ke kterému máte přístup. 
3. Počkejte, až bude virtuální počítač připravený. Na dlaždici VM si všimněte následujících polí:
    1. V horní části dlaždice se zobrazí **název testovacího prostředí**.
    1. Napravo se zobrazí ikona představující **operační systém** virtuálního počítače. V tomto příkladu se jedná o operační systém Windows. 
    1. V dolní části dlaždice se zobrazí ikony nebo tlačítka, které spustí/zastaví virtuální počítač a připojí se k virtuálnímu počítači. 
    1. Napravo od tlačítek se zobrazí stav virtuálního počítače. Ověřte, že se zobrazuje stav virtuálního počítače Zastaveno.

        ![Virtuální počítač v zastaveném stavu](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Spuštění nebo zastavení virtuálního počítače
1. **Spusťte** virtuální počítač tak, že vyberete první tlačítko, jak je znázorněno na následujícím obrázku. Tento proces trvá nějakou dobu.  

    ![Spuštění virtuálního počítače](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Potvrďte, že stav virtuálního počítače je nastavený na **spuštěno**. 

    ![Virtuální počítač ve spuštěném stavu](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Všimněte si, že ikona prvního tlačítka se změnila tak, aby představovala operaci **zastavení** . Kliknutím na toto tlačítko můžete virtuální počítač zastavit. 

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. Vyberte druhé tlačítko, jak je znázorněno na následujícím obrázku , abyste se připojili k virtuálnímu počítači testovacího prostředí. 

    ![Připojení k virtuálnímu počítači](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Proveďte jeden z následujících kroků: 
    1. U virtuálních počítačů s **Windows** uložte soubor **RDP** na pevný disk. Otevřete soubor RDP pro připojení k virtuálnímu počítači. Přihlaste se k počítači pomocí **uživatelského jména** a **hesla** , které jste získali z Educator/profesor. 
    3. Pro virtuální počítače se **systémem Linux** můžete k připojení použít **SSH** nebo **RDP** (Pokud je povolený). Další informace najdete v tématu [Povolení připojení ke vzdálené ploše pro počítače se systémem Linux](how-to-enable-remote-desktop-linux.md). 

## <a name="progress-bar"></a>Indikátor průběhu 
Indikátor průběhu na dlaždici zobrazuje počet hodin použitých pro počet [hodin](how-to-configure-student-usage.md#set-quotas-for-users) , které vám jsou přiřazeny. Tentokrát je další čas, který jste si naplánovali vedle naplánovaného času pro testovací prostředí. Barva indikátoru průběhu a textu pod indikátorem průběhu se liší podle následujících scénářů:

- Pokud třída probíhá (v rámci plánu třídy), indikátor průběhu je šedý a představuje nepoužívané hodiny kvóty. 

    ![Indikátor průběhu v šedé barvě](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Pokud kvóta není přiřazena (0 hodin), text **dostupný pouze během tříd** je zobrazen místo indikátoru průběhu. 
    
    ![Stav, pokud není nastavená žádná kvóta](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Pokud jste nepoužívali **kvótu**, barva indikátoru průběhu je **červená**. 

    ![Indikátor průběhu v červené barvě](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Barva indikátoru průběhu je **modrá** , pokud se nachází mimo naplánovaný čas pro testovací prostředí a že se použila určitá kvótová doba. 

    ![Indikátor průběhu s modrou barvou](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Zobrazit všechna testovací prostředí v učebně
Po registraci do testovacích prostředí se můžete podívat na všechny laboratoře učebny, a to provedením následujících kroků: 

1. Přejděte na [https://labs.azure.com](https://labs.azure.com). Aplikace Internet Explorer 11 není zatím podporována. 
2. Přihlaste se ke službě pomocí uživatelského účtu, který jste použili k registraci do testovacího prostředí. 
3. Ověřte, že se zobrazí všechny laboratoře, ke kterým máte přístup. 

    ![Zobrazit všechna testovací prostředí](../media/how-to-use-classroom-lab/all-labs.png)


## <a name="next-steps"></a>Další postup
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
 