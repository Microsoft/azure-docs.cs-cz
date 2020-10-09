---
title: Přístup k testovacímu prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu získáte přístup k virtuálním počítačům v prostředí učebny, které je nastavené pomocí Educator.
ms.topic: tutorial
ms.date: 06/26/2020
ms.openlocfilehash: dacfa34c0d3ab637ef513342bc5ce5fe81038e11
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443464"
---
# <a name="tutorial-access-a-classroom-lab-in-azure-lab-services"></a>Kurz: Přístup k testovacímu prostředí v učebně v Azure Lab Services
V tomto kurzu se vy, jako student, připojíte k virtuálnímu počítači v testovacím prostředí v učebně. 

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Registrovat do testovacího prostředí
> * Spuštění virtuálního počítače
> * Připojení k virtuálnímu počítači

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
    1. Indikátor průběhu na dlaždici zobrazuje počet hodin použitých pro počet [hodin](how-to-configure-student-usage.md#set-quotas-for-users) , které vám jsou přiřazeny. Tentokrát je další čas, který jste si naplánovali vedle naplánovaného času pro testovací prostředí. 
    1. V dolní části dlaždice se zobrazí ikony nebo tlačítka, které spustí/zastaví virtuální počítač a připojí se k virtuálnímu počítači. 
    1. Napravo od tlačítek se zobrazí stav virtuálního počítače. Ověřte, že se zobrazuje stav virtuálního počítače **Zastaveno**. 

        ![Virtuální počítač v zastaveném stavu](./media/tutorial-connect-vm-in-classroom-lab/vm-in-stopped-state.png)

## <a name="start-the-vm"></a>Spuštění virtuálního počítače
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

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste získali přístup k prostředí učebny pomocí registračního odkazu, který získáte z Educator.

Jako vlastník testovacího prostředí chcete zobrazit, kdo se zaregistroval s vaším testovacím prostředím a sledovat využití virtuálních počítačů. Přejděte k dalšímu kurzu, kde se dozvíte, jak sledovat využití testovacího prostředí:

> [!div class="nextstepaction"]
> [Sledování využití testovacího prostředí](tutorial-track-usage.md) 
