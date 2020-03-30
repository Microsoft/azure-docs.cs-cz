---
title: Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services | Microsoft Docs
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
ms.topic: how-to
ms.custom: mvc
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: db1e60ccd7e05cb8b6923d6b2ec008cdfb76eaa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501931"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services
Tento článek popisuje, jak se zaregistrovat do testovacího prostředí učebny, zobrazit všechna testovací prostředí, ke kterým máte přístup, spustit nebo zastavit virtuální hospodařící v testovacím prostředí a připojit se k virtuálnímu virtuálnímu zařízení. 

## <a name="register-to-the-lab"></a>Zaregistrujte se do laboratoře

1. Přejděte na **registrační adresu URL,** kterou jste obdrželi od profesora/pedagoga. Po dokončení registrace nemusíte používat registrační adresu URL. Místo toho použijte [https://labs.azure.com](https://labs.azure.com)adresu URL: . Internet Explorer 11 ještě není podporovaný. 
1. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 

    > [!NOTE]
    > Pro používání Azure Lab Services je potřeba účet Microsoft. Pokud se k přihlášení k portálu pokoušíte použít svůj účet, který není od Microsoftu, například Yahoo nebo Účty Google, vytvořte podle pokynů účet Microsoft, který bude propojen s vaším účtem jiného výrobce než Microsoft. Potom postupujte podle pokynů k dokončení procesu registrace. 
1. Po dokončení registrace zkontrolujte, že vidíte virtuální počítač pro testovací prostředí, ke kterému máte přístup. 
1. Počkejte, až bude virtuální počítač připraven. Na dlaždici virtuálního počítači si všimněte následujících polí:
    1. V horní části dlaždice se zobrazí **název laboratoře**.
    1. Vpravo se zobrazí ikona představující **operační systém (OS)** virtuálního soudu. V tomto příkladu je operační systém Windows. 
    1. V dolní části dlaždice se zobrazují ikony nebo tlačítka, které spouštějí nebo zastavují virtuální hospo, a připojují se k virtuálnímu virtuálnímu virtuálnímu zařízení. 
    1. Napravo od tlačítek se zobrazí stav virtuálního virtuálního soudu. Zkontrolujte, že se zobrazuje stav virtuálního virtuálního **soudu je zastaven .**

        ![Virtuální mísa v zastaveném stavu](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Spuštění nebo zastavení virtuálního mísy
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
    1. Pokud používáte **Mac** pro připojení k virtuálnímu počítači testovacího prostředí, postupujte podle pokynů v další části. 

## <a name="progress-bar"></a>Indikátor průběhu 
Indikátor průběhu na dlaždici zobrazuje počet hodin použitých v porovnání s počtem [hodin kvót,](how-to-configure-student-usage.md#set-quotas-for-users) které vám byly přiřazeny. Tento čas je navíc k naplánovanému času pro laboratoř přidělený další čas. Barva indikátoru průběhu a textu pod indikátorem průběhu se liší podle následujících scénářů:

- Pokud probíhá třída (v rámci plánu třídy), indikátor průběhu je šedě zobrazen, aby představoval hodiny kvóty, které se nepoužívají. 

    ![Indikátor průběhu v šedé barvě](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Pokud kvóta není přiřazena (nula hodin), text **K dispozici pouze během tříd** je zobrazen místo indikátoru průběhu. 
    
    ![Stav, pokud není nastavena žádná kvóta](../media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Pokud vám **došla kvóta**, barva indikátoru průběhu je **červená**. 

    ![Indikátor průběhu v červené barvě](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Barva indikátoru průběhu je **modrá,** pokud je mimo plánovaný čas pro testovací prostředí a byl použit určitý čas kvóty. 

    ![Indikátor průběhu v modré barvě](../media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Zobrazit všechna testovací prostředí v učebně
Po registraci do testovacího prostředí můžete zobrazit všechny laboratoře ve třídě následujícím postupem: 

1. Přejděte [https://labs.azure.com](https://labs.azure.com)na . Internet Explorer 11 ještě není podporovaný. 
2. Přihlaste se ke službě pomocí uživatelského účtu, který jste použili k registraci do testovacího prostředí. 
3. Potvrďte, že vidíte všechny laboratoře, ke kterým máte přístup. 

    ![Zobrazit všechna testovací prostředí](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako správce vytvářejte a spravujte účty testovacího prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářejte a spravujte testovací prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavte a publikujte šablony](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
 