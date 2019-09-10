---
title: Správa učebných cvičení v Azure Lab Services | Microsoft Docs
description: Naučte se vytvářet a konfigurovat testovací prostředí pro učebnu, Zobrazit všechny vývojové laboratoře, sdílet odkaz na registraci s uživatelem testovacího prostředí nebo odstranit testovací prostředí.
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873575"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Správa učebných cvičení v Azure Lab Services 
Tento článek popisuje, jak vytvořit a odstranit testovací prostředí pro učebnu. Také se dozvíte, jak zobrazit všechny vývojové laboratoře v účtu testovacího prostředí. 

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí v učebně v účtu testovacího prostředí, musíte v účtu testovacího prostředí být členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Vlastník testovacího prostředí může přidat další uživatele do role testovacího prostředí pomocí kroků v následujícím článku: [Přidejte uživatele do role testovacího prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Všimněte si, že aplikace Internet Explorer 11 ještě není podporována. 
2. Vyberte **Přihlásit se**. Vyberte nebo zadejte **ID uživatele** , který je členem role testovacího **prostředí** v účtu testovacího prostředí, a zadejte heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zadejte maximální **počet virtuálních počítačů** v testovacím prostředí. Počet virtuálních počítačů v testovacím prostředí můžete zvýšit nebo snížit později. 
    6. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stránce **Select virtual machine specifications** (Výběr specifikací virtuálních počítačů) proveďte následující kroky:
    1. Vyberte **velikost** virtuálních počítačů vytvořených v testovacím prostředí. V současné době jsou povolené **malé**, **střední**, **střední (virtualizační)** , **velké**a střední velikosti **GPU** . Podrobnosti najdete v části [velikosti virtuálních počítačů](#vm-sizes) .
    1. Vyberte **oblast**, ve které se mají virtuální počítače vytvořit. 
    1. Vyberte **image virtuálního počítače**, která se má použít k vytvoření virtuálních počítačů v testovacím prostředí. Pokud vyberete image pro Linux, zobrazí se vám možnost Povolit pro ni připojení ke vzdálené ploše. Podrobnosti najdete v tématu [Povolení připojení ke vzdálené ploše pro Linux](how-to-enable-remote-desktop-linux.md).
    1. Vyberte **Další**.

        ![Zadání specifikací virtuálních počítačů](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stránce **Set credentials** (Nastavení přihlašovacích údajů) zadejte výchozí přihlašovací údaje ke všem virtuálním počítačům v testovacím prostředí. 
    1. Zadejte **jméno uživatele** pro všechny virtuální počítače v testovacím prostředí.
    2. Zadejte **heslo** tohoto uživatele. 

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Pokud chcete, aby studenti nastavili vlastní hesla, zakažte možnost **použít stejné heslo pro všechny virtuální počítače** . Tento krok je **volitelný**. 

        Učitel se může rozhodnout použít stejné heslo pro všechny virtuální počítače v testovacím prostředí, nebo umožní studentům nastavit hesla pro svoje virtuální počítače. Ve výchozím nastavení je toto nastavení povolené pro všechny image Windows a Linux s výjimkou Ubuntu. Když vyberete virtuální počítač **Ubuntu** , toto nastavení se zakáže, takže studenti budou vyzváni, aby při prvním přihlášení nastavili heslo.
    1. Vyberte **Vytvořit**. 

        ![Nastavit přihlašovací údaje](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stránce **Configure template** (Konfigurace šablony) se zobrazí stav vytváření testovacího prostředí. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurovaný přesně podle toho, co chcete uživatelům testovacího prostředí poskytnout.  

    ![Konfigurace šablony](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po dokončení konfigurace šablony se zobrazí následující stránka: 

    ![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Následující kroky jsou pro tento kurz volitelné: 
    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud se jedná o virtuální počítač šablony pro Linux, můžete zvolit, jestli se chcete připojit pomocí protokolu SSH nebo RDP (Pokud je povolený protokol RDP).
    1. Pokud chcete resetovat heslo pro tento virtuální počítač, vyberte **resetovat heslo** . 
    1. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    1. **Zastavte** virtuální počítač.  
    1. Zadejte **popis** šablony.
9. Na stránce šablony vyberte **Next** (Další). 
10. Na stránce **Publish the template** (Publikování šablony) proveďte následující akce. 
    1. Pokud chcete šablonu publikovat okamžitě, zaškrtněte políčko *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Chápu, že publikovanou šablonu není možné upravit. Tento postup je možné provést pouze jednou a může trvat až hodinu.) a vyberte **Publish** (Publikovat).  Publikujte šablonu, aby instance šablony virtuálního počítače byly dostupné všem uživatelům testovacího prostředí.

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Virtuální počítač šablony můžete publikovat i po dokončení průvodce. Podrobnosti o tom, jak nakonfigurovat a publikovat po dokončení průvodce, najdete v článku o tom, jak nakonfigurovat a publikovat po dokončení průvodce. informace najdete v části publikování šablony v článku [How to Manage The učeben Labs](how-to-manage-classroom-labs.md) .

        ![Publikování šablony](../media/tutorial-setup-classroom-lab/publish-template.png)
11. Zobrazí se **průběh publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. Po úspěšném publikování šablony se zobrazí následující stránka. Vyberte **Done** (Hotovo).

    ![Publikování šablony – úspěch](../media/tutorial-setup-classroom-lab/publish-success.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Přepněte na stránku **Virtual machines** (Virtuální počítače) a zkontrolujte, že se zobrazí virtuální počítače ve stavu **Unassigned** (Nepřiřazeno). Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>Velikost virtuálních počítačů  

| Size | Jádra | Paměť RAM | Popis | 
| ---- | ----- | --- | ----------- | 
| Malé | 2 | 3,5 GB | Tato velikost je nejvhodnější pro příkazový řádek, otevírá webový prohlížeč, webové servery s nízkým provozem, malé až střední databáze. |
| Střední | 4 | 7 GB | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy. | 
| Střední (vnořená virtualizace) | 4 | 16 GB | Tato velikost se nejlépe hodí pro relační databáze, ukládání do mezipaměti v paměti a analýzy. Tato velikost také podporuje vnořenou virtualizaci. <p>Tato velikost se dá použít ve scénářích, kdy každý student potřebuje víc virtuálních počítačů. Učitelé můžou pomocí vnořené virtualizace nastavit v rámci virtuálního počítače několik vnořených virtuálních počítačů s malou velikostí. </p> |
| Velké | 8 | 32 GB | Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, lepší výkon místních disků, velké databáze a velké mezipaměti paměti. Tato velikost také podporuje vnořenou virtualizaci. |  
| Malý grafický procesor (vizualizace) | 6 | 56 GB | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. | 
| Malý grafický procesor (COMPUTE) | 6 | 56 GB | Tato velikost se nejlépe hodí pro aplikace náročné na výpočetní výkon a sítě, jako jsou umělá studia a aplikace s hloubkovým učením. | 
| Střední GPU (vizualizace) | 12 | 112 GB | Tato velikost se nejlépe hodí pro vzdálenou vizualizaci, streamování, hraní her a kódování pomocí platforem, jako je OpenGL a DirectX. | 

## <a name="view-all-classroom-labs"></a>Zobrazit všechna cvičení v učebně
1. Přejděte na [portál Azure Lab Services](https://labs.azure.com).
2. Vyberte **Přihlásit se**. Vyberte nebo zadejte **ID uživatele** , který je členem role testovacího **prostředí** v účtu testovacího prostředí, a zadejte heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Potvrďte, že ve vybraném účtu testovacího prostředí vidíte všechny laboratoře. 

    ![Všechny laboratoře](../media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní části vyberte jiný účet testovacího prostředí. V rámci vybraného účtu testovacího prostředí uvidíte Labs. 

## <a name="delete-a-classroom-lab"></a>Odstranění testovacího prostředí učebny
1. Na dlaždici pro testovací prostředí vyberte v rohu tři tečky (...). 

    ![Výběr testovacího prostředí](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Vyberte **Odstranit**. 

    ![Tlačítko Odstranit](../media/how-to-manage-classroom-labs/delete-button.png)
3. V dialogovém okně **Odstranit testovací prostředí** vyberte **Odstranit**. 

    ![Dialogové okno Odstranit](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Přepnout na další prostředí učebny
Pokud chcete z aktuální nabídky přejít na jiné prostředí učebny, vyberte rozevírací seznam cvičení v účtu testovacího prostředí v horní části.

![V horní části vyberte testovací prostředí z rozevíracího seznamu.](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)

