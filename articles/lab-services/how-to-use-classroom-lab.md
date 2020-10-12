---
title: Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak se zaregistrovat do testovacího prostředí, Zobrazit všechny laboratoře, ke kterým máte přístup, jak spustit nebo zastavit virtuální počítač v testovacím prostředí a jak se připojit k virtuálnímu počítači.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0c2f0ba5e6a47e00f47a31e08c036ea17ff2507a
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946106"
---
# <a name="how-to-access-a-classroom-lab-in-azure-lab-services"></a>Jak získat přístup k testovacímu prostředí v učebně v Azure Lab Services
Tento článek popisuje, jak se zaregistrovat do testovacího prostředí, Zobrazit všechny laboratoře, ke kterým máte přístup, jak spustit nebo zastavit virtuální počítač v testovacím prostředí a jak se připojit k virtuálnímu počítači. 

## <a name="register-to-the-lab"></a>Registrovat do testovacího prostředí

1. Přejděte na **adresu URL registrace** , kterou jste dostali z Educator. Po dokončení registrace nemusíte používat registrační adresu URL. Místo toho použijte adresu URL: [https://labs.azure.com](https://labs.azure.com) . Aplikace Internet Explorer 11 není zatím podporována. 

    ![Registrovat do testovacího prostředí](./media/tutorial-connect-vm-in-classroom-lab/register-lab.png)
1. Přihlaste se ke službě pomocí školního účtu, abyste dokončili registraci. 

    > [!NOTE]
    > Pro použití Azure Lab Services je vyžadován účet Microsoft. Pokud se pokoušíte použít účet Microsoft, jako jsou například účty Yahoo nebo Google, abyste se přihlásili k portálu, postupujte podle pokynů pro vytvoření účet Microsoft, která bude propojena s neúčet Microsoft. Pak postupujte podle pokynů k dokončení procesu registrace. 
1. Po dokončení registrace zkontrolujte, že vidíte virtuální počítač pro testovací prostředí, ke kterému máte přístup. 

    ![Dostupné virtuální počítače](./media/tutorial-connect-vm-in-classroom-lab/accessible-vms.png)
1. Počkejte, až bude virtuální počítač připravený. Na dlaždici VM si všimněte následujících polí:
    1. V horní části dlaždice se zobrazí **název testovacího prostředí**.
    1. Napravo se zobrazí ikona představující **operační systém** virtuálního počítače. V tomto příkladu se jedná o operační systém Windows. 
    1. V dolní části dlaždice se zobrazí ikony nebo tlačítka, které spustí/zastaví virtuální počítač a připojí se k virtuálnímu počítači. 
    1. Napravo od tlačítek se zobrazí stav virtuálního počítače. Ověřte, že se zobrazuje stav virtuálního počítače **Zastaveno**.

        ![Virtuální počítač v zastaveném stavu](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-or-stop-the-vm"></a>Spuštění nebo zastavení virtuálního počítače
1. **Spusťte** virtuální počítač tak, že vyberete první tlačítko, jak je znázorněno na následujícím obrázku. Tento proces trvá nějakou dobu.  

    ![Spuštění virtuálního počítače](./media/tutorial-connect-vm-in-classroom-lab/start-vm.png)
4. Potvrďte, že stav virtuálního počítače je nastavený na **spuštěno**. 

    ![Virtuální počítač ve spuštěném stavu](./media/tutorial-connect-vm-in-classroom-lab/vm-running.png)

    Všimněte si, že ikona prvního tlačítka se změnila tak, aby představovala operaci **zastavení** . Kliknutím na toto tlačítko můžete virtuální počítač zastavit. 

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

1. Vyberte druhé tlačítko, jak je znázorněno na následujícím obrázku, abyste se **připojili** k virtuálnímu počítači testovacího prostředí. 

    ![Připojení k virtuálnímu počítači](./media/tutorial-connect-vm-in-classroom-lab/connect-vm.png)
2. Proveďte jeden z následujících kroků: 
    1. U virtuálních počítačů s **Windows** uložte soubor **RDP** na pevný disk. Otevřete soubor RDP pro připojení k virtuálnímu počítači. K přihlášení k počítači použijte **uživatelské jméno** a **heslo** , které jste získali ze svého Educator. 
    3. Pro virtuální počítače se **systémem Linux** můžete k připojení použít **SSH** nebo **RDP** (Pokud je povolený). Další informace najdete v tématu [Povolení připojení ke vzdálené ploše pro počítače se systémem Linux](how-to-enable-remote-desktop-linux.md). 
    1. Pokud pro připojení k virtuálnímu počítači testovacího prostředí používáte **Mac** , postupujte podle pokynů v následující části. 

## <a name="progress-bar"></a>Indikátor průběhu 
Indikátor průběhu na dlaždici zobrazuje počet hodin použitých pro počet [hodin](how-to-configure-student-usage.md#set-quotas-for-users) , které vám jsou přiřazeny. Tentokrát je další čas, který jste si naplánovali vedle naplánovaného času pro testovací prostředí. Barva indikátoru průběhu a textu pod indikátorem průběhu se liší podle následujících scénářů:

- Pokud třída probíhá (v rámci plánu třídy), indikátor průběhu je zobrazen šedě, aby představovala nepoužité hodiny kvóty. 

    ![Indikátor průběhu v šedé barvě](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-class-in-progress.png)
- Pokud kvóta není přiřazena (0 hodin), text **dostupný pouze během tříd** je zobrazen místo indikátoru průběhu. 
    
    ![Stav, pokud není nastavená žádná kvóta](./media/tutorial-connect-vm-in-classroom-lab/available-during-class.png)
- Pokud jste nepoužívali **kvótu**, barva indikátoru průběhu je **červená**. 

    ![Indikátor průběhu v červené barvě](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-red-color.png)
- Barva indikátoru průběhu je **modrá** , pokud se nachází mimo naplánovaný čas pro testovací prostředí a že se použila určitá kvótová doba. 

    ![Indikátor průběhu s modrou barvou](./media/tutorial-connect-vm-in-classroom-lab/progress-bar-blue-color.png)


## <a name="view-all-the-classroom-labs"></a>Zobrazit všechna testovací prostředí v učebně
Po registraci do testovacích prostředí se můžete podívat na všechny laboratoře učebny, a to provedením následujících kroků: 

1. Přejděte na [https://labs.azure.com](https://labs.azure.com) . Aplikace Internet Explorer 11 není zatím podporována. 
2. Přihlaste se ke službě pomocí uživatelského účtu, který jste použili k registraci do testovacího prostředí. 
3. Ověřte, že se zobrazí všechny laboratoře, ke kterým máte přístup. 

    ![Zobrazit všechna testovací prostředí](./media/how-to-manage-classroom-labs/all-labs.png)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
 