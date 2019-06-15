---
title: Správa testovacích prostředí v učebnách ve službě Azure Lab Services | Dokumentace Microsoftu
description: Informace o vytvoření a konfigurace testovacího prostředí v učebně, zobrazit všichni testovací prostředí v učebnách, nasdílet odkaz na registraci uživatele testovacího prostředí nebo odstranění testovacího prostředí.
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
ms.openlocfilehash: 6ba41132c93ebdb2578bafb100416ca3fe579298
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67123291"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Správa testovacích prostředí v učebnách ve službě Azure Lab Services 
Tento článek popisuje, jak vytvářet a odstraňovat prostředí v učebně. Je také ukazuje, jak zobrazit všechny testovací prostředí v učebnách účtu testovacího prostředí. 

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí v učebně v účtu testovacího prostředí, musíte v účtu testovacího prostředí být členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Vlastník testovacího prostředí můžete přidat další uživatele do role Tvůrce prostředí pomocí kroků v následujícím článku: [Přidání uživatele do role Tvůrce prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Všimněte si, že aplikace Internet Explorer 11 se ještě nepodporuje. 
2. Vyberte **přihlášení**. Vyberte nebo zadejte **ID uživatele** , který je členem skupiny **Autor testovacího prostředí** roli v testovacím prostředí účet a zadat heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Zadejte maximální **počet virtuálních počítačů** v testovacím prostředí. Můžete zvýšit nebo snížit počet virtuálních počítačů v testovacím prostředí později. 
    6. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Na stránce **Select virtual machine specifications** (Výběr specifikací virtuálních počítačů) proveďte následující kroky:
    1. Vyberte **velikost** virtuálních počítačů vytvořených v testovacím prostředí. V současné době **malé**, **střední**, **střední (virtualizace)** , **velké**, a **GPU** velikosti jsou povolené. Podrobnosti najdete v tématu [velikosti virtuálních počítačů](#vm-sizes) oddílu.
    1. Vyberte **oblast**, ve které se mají virtuální počítače vytvořit. 
    1. Vyberte **image virtuálního počítače**, která se má použít k vytvoření virtuálních počítačů v testovacím prostředí. Pokud vyberete image Linuxu, zobrazí se možnost Povolit připojení ke vzdálené ploše pro něj. Podrobnosti najdete v tématu [povolit připojení ke vzdálené ploše pro Linux](how-to-enable-remote-desktop-linux.md).
    1. Vyberte **Další**.

        ![Zadání specifikací virtuálních počítačů](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Na stránce **Set credentials** (Nastavení přihlašovacích údajů) zadejte výchozí přihlašovací údaje ke všem virtuálním počítačům v testovacím prostředí. 
    1. Zadejte **jméno uživatele** pro všechny virtuální počítače v testovacím prostředí.
    2. Zadejte **heslo** tohoto uživatele. 

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Zakázat **použít stejné heslo pro všechny virtuální počítače** možnost, pokud chcete, aby studenti mohli nastavit hesla. Tento krok je **volitelný**. 

        Učitel můžete použít stejné heslo pro virtuální počítače v testovacím prostředí, nebo povolíte studenty k nastavení hesla pro své virtuální počítače. Ve výchozím nastavení je toto nastavení povolené pro všechny Windows a Linuxem Image s výjimkou Ubuntu. Když vyberete **Ubuntu** virtuálních počítačů, toto nastavení je zakázané, takže studenty vyzve k nastavení hesla, při prvním přihlášení.
    1. Vyberte **Vytvořit**. 

        ![Nastavení přihlašovacích údajů](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Na stránce **Configure template** (Konfigurace šablony) se zobrazí stav vytváření testovacího prostředí. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurovaný přesně podle toho, co chcete uživatelům testovacího prostředí poskytnout.  

    ![Konfigurace šablony](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Po dokončení konfigurace šablony se zobrazí následující stránka: 

    ![Stránka Configure template (Konfigurace šablony) po dokončení konfigurace](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. Následující kroky jsou pro tento kurz volitelné: 
    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud je šablona Linux virtuálního počítače, můžete vybrat, jestli se chcete připojit pomocí SSH nebo RDP (Pokud je povolen protokol RDP).
    1. Vyberte **resetovat heslo** k resetování hesla pro virtuální počítač. 
    1. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    1. **Zastavte** virtuální počítač.  
    1. Zadejte **popis** šablony.
9. Na stránce šablony vyberte **Next** (Další). 
10. Na stránce **Publish the template** (Publikování šablony) proveďte následující akce. 
    1. Pokud chcete šablonu publikovat okamžitě, zaškrtněte políčko *I understand I can't modify the template after publishing. This process can only be done once and can take up to an hour* (Chápu, že publikovanou šablonu není možné upravit. Tento postup je možné provést pouze jednou a může trvat až hodinu.) a vyberte **Publish** (Publikovat).  Publikujte šablonu, aby instance šablony virtuálního počítače byly dostupné všem uživatelům testovacího prostředí.

        > [!WARNING]
        > Publikování nejde vrátit zpět. 
    2. Pokud chcete publikování provést později, vyberte **Save for later** (Uložit na později). Virtuální počítač šablony můžete publikovat i po dokončení průvodce. Podrobnosti o tom, jak konfigurace a publikování po dokončení průvodce, naleznete v části Podrobnosti o tom, jak konfigurace a publikování po dokončení průvodce, najdete v části publikování v oddílu šablony [Správa testovacích prostředí v učebnách](how-to-manage-classroom-labs.md) článku.

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

| Velikost | Jádra | Paměť RAM | Popis | 
| ---- | ----- | --- | ----------- | 
| Malé | 2 | 3,5 GB | Tato velikost je nejvhodnější pro příkazový řádek, otevřete webový prohlížeč, webové servery s malým provozem, malé až střední databáze. |
| Střední | 4 | 7 GB | Tato velikost je nejvhodnější pro relační databáze, ukládání do mezipaměti a analýzu | 
| Střední (vnořené virtualizace) | 4 | 16 GB | Tato velikost je nejvhodnější pro relační databáze, ukládání do mezipaměti a analýzu. Tato velikost podporuje také vnořené virtualizace. <p>Tato velikost je možné ve scénářích, kde každý student potřebovat víc virtuálních počítačů. Učitelé můžete použít vnořená virtualizace zřídit několik virtuálních počítačů pro krátkodobé vnořené uvnitř virtuálního počítače. </p> |
| Velké | 8 | 32 GB | Tato velikost je nejvhodnější pro aplikace, které vyžadují rychlejší procesory, vyšší výkon místního disku, velkých databází, mezipaměti velké paměti. Tato velikost podporuje také vnořené virtualizace |  
| GPU | 12 | 112 GB | Tato velikost je nejvhodnější pro úlohy náročné na výpočetní prostředky, náročné na grafiku a vizualizace | 

## <a name="view-all-classroom-labs"></a>Zobrazit všechny testovací prostředí v učebnách
1. Přejděte do [portálu Azure Lab Services](https://labs.azure.com).
2. Vyberte **přihlášení**. Vyberte nebo zadejte **ID uživatele** , který je členem skupiny **Autor testovacího prostředí** roli v testovacím prostředí účet a zadat heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Ověřte, že testovací prostředí v rámci účtu vybraného testovacího prostředí. 

    ![Všechny testovací prostředí](../media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní části vyberte účet jiného testovacího prostředí. Zobrazí testovací prostředí v rámci účtu vybraného testovacího prostředí. 

## <a name="delete-a-classroom-lab"></a>Odstranění testovacího prostředí v učebně
1. Na dlaždici pro testovací prostředí vyberte tři tečky (...) v pravém rohu. 

    ![Výběr testovacího prostředí](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Vyberte **Odstranit**. 

    ![Tlačítko Odstranit](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na **Delete lab** dialogu **odstranit**. 

    ![Dialogové okno Odstranit](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Přepnout do jiného prostředí v učebně
Přejděte do jiného prostředí v učebně z aktuálního vyberte rozevírací seznam testovacích prostředí v rámci účtu testovacího prostředí v horní části.

![Vyberte z rozevíracího seznamu v horní části testovacího prostředí](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurovat a spravovat využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)

