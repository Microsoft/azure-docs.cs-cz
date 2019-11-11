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
ms.date: 09/19/2019
ms.author: spelluru
ms.openlocfilehash: 2ac9e8b8d0635eceb7d4f85ad867b102f7d064f5
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585162"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services
Tento článek popisuje, jak se zaregistrovat do testovacího prostředí, Zobrazit všechny laboratoře, ke kterým máte přístup, jak spustit nebo zastavit virtuální počítač v testovacím prostředí a jak se připojit k virtuálnímu počítači. 

## <a name="register-to-the-lab"></a>Registrovat do testovacího prostředí

1. Přejděte na **adresu URL pro registraci**, kterou jste dostali od vyučujícího. Po dokončení registrace nemusíte používat registrační adresu URL. Místo toho použijte adresu URL: [https://labs.azure.com](https://labs.azure.com). Aplikace Internet Explorer 11 není zatím podporována. 
1. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 

    > [!NOTE]
    > Pro použití Azure Lab Services je vyžadován účet Microsoft. Pokud se pokoušíte použít účet Microsoft, jako jsou například účty Yahoo nebo Google, abyste se přihlásili k portálu, postupujte podle pokynů pro vytvoření účet Microsoft, která bude propojena s neúčet Microsoft. Pak postupujte podle pokynů k dokončení procesu registrace. 
1. Po dokončení registrace zkontrolujte, že vidíte virtuální počítač pro testovací prostředí, ke kterému máte přístup. 
1. Počkejte, až bude virtuální počítač připravený. Na dlaždici VM si všimněte následujících polí:
    1. V horní části dlaždice se zobrazí **název testovacího prostředí**.
    1. Napravo se zobrazí ikona představující **operační systém** virtuálního počítače. V tomto příkladu se jedná o operační systém Windows. 
    1. V dolní části dlaždice se zobrazí ikony nebo tlačítka, které spustí/zastaví virtuální počítač a připojí se k virtuálnímu počítači. 
    1. Napravo od tlačítek se zobrazí stav virtuálního počítače. Ověřte, že se zobrazuje stav virtuálního počítače **Zastaveno**.

        ![Virtuální počítač v zastaveném stavu](../media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Spuštění nebo zastavení virtuálního počítače
1. **Spusťte** virtuální počítač tak, že vyberete první tlačítko, jak je znázorněno na následujícím obrázku. Tento proces trvá nějakou dobu.  

    ![Spuštění virtuálního počítače](../media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Potvrďte, že stav virtuálního počítače je nastavený na **spuštěno**. 

    ![Virtuální počítač ve spuštěném stavu](../media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Všimněte si, že ikona prvního tlačítka se změnila tak, aby představovala operaci **zastavení** . Kliknutím na toto tlačítko můžete virtuální počítač zastavit. 

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. Vyberte druhé tlačítko, jak je znázorněno na následujícím obrázku, abyste se **připojili** k virtuálnímu počítači testovacího prostředí. 

    ![Připojení k virtuálnímu počítači](../media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Proveďte jeden z následujících kroků: 
    1. U virtuálních počítačů s **Windows** uložte soubor **RDP** na pevný disk. Otevřete soubor RDP pro připojení k virtuálnímu počítači. Přihlaste se k počítači pomocí **uživatelského jména** a **hesla** , které jste získali z Educator/profesor. 
    3. Pro virtuální počítače se **systémem Linux** můžete k připojení použít **SSH** nebo **RDP** (Pokud je povolený). Další informace najdete v tématu [Povolení připojení ke vzdálené ploše pro počítače se systémem Linux](how-to-enable-remote-desktop-linux.md). 
    1. Pokud pro připojení k virtuálnímu počítači testovacího prostředí používáte **Mac** , postupujte podle pokynů v následující části. 

## <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Připojení k virtuálnímu počítači pomocí protokolu RDP na Macu
V této části se dozvíte, jak se student může připojit k virtuálnímu počítači z Mac pomocí protokolu RDP.

### <a name="step-1-install-microsoft-remote-desktop-on-a-mac"></a>Krok 1: instalace Vzdálená plocha Microsoft na Macu
1. Otevřete App Store na Macu a vyhledejte **Vzdálená plocha Microsoft**.

    ![Vzdálená plocha Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Nainstalujte nejnovější verzi Vzdálená plocha Microsoft. 

### <a name="step-2-access-the-vm-from-your-mac-using-rdp"></a>Krok 2: přístup k virtuálnímu počítači z počítače Mac pomocí protokolu RDP
1. Otevřete soubor **RDP** , který je stažený na počítači s nainstalovaným **Vzdálená plocha Microsoft** . Mělo by se začít připojovat k virtuálnímu počítači. 

    ![Připojení k virtuálnímu počítači](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Pokud se zobrazí následující upozornění, vyberte **pokračovat** . 

    ![Upozornění certifikátu](../media/how-to-use-classroom-lab/certificate-error.png)
1. Měl by se zobrazit virtuální počítač. 

    > [!NOTE]
    > Následující příklad je pro virtuální počítač s CentOS Linux. 

    ![Virtuální počítač](../media/how-to-use-classroom-lab/vm-ui.png)

## <a name="progress-bar"></a>Indikátor průběhu 
Indikátor průběhu na dlaždici zobrazuje počet hodin použitých pro počet [hodin](how-to-configure-student-usage.md#set-quotas-for-users) , které vám jsou přiřazeny. Tentokrát je další čas, který jste si naplánovali vedle naplánovaného času pro testovací prostředí. Barva indikátoru průběhu a textu pod indikátorem průběhu se liší podle následujících scénářů:

- Pokud třída probíhá (v rámci plánu třídy), indikátor průběhu je zobrazen šedě, aby představovala nepoužité hodiny kvóty. 

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

    ![Zobrazit všechna testovací prostředí](../media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
 