---
title: Správa testovacích prostředí ve třídě ve službách Azure Lab Services | Dokumenty společnosti Microsoft
description: Přečtěte si, jak vytvořit a nakonfigurovat testovací prostředí učebny, zobrazit všechna testovací prostředí ve třídě, sdílet registrační odkaz s uživatelem testovacího prostředí nebo odstranit testovací prostředí.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 46c53c99c12ade986ab913bf013b652a931a4d22
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257738"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Správa testovacích prostředí ve třídě ve službě Azure Lab Services 
Tento článek popisuje, jak vytvořit a odstranit testovací prostředí učebny. Také ukazuje, jak zobrazit všechny učebny laboratoře v účtu testovacího prostředí. 

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí v učebně v účtu testovacího prostředí, musíte v účtu testovacího prostředí být členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Vlastník testovacího prostředí může přidat další uživatele do role Autor testovacího prostředí podle postupu v následujícím článku: [Přidání uživatele do role Autor testovacího prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com). Internet Explorer 11 ještě není podporován. 
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. Vyberte nebo zadejte **ID uživatele,** který je členem role **Tvůrce testovacího prostředí** v účtu testovacího prostředí, a zadejte heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Vyberte **nové testovací prostředí**. 
    
    ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí. 
    2. Vyberte **velikost virtuálních počítačů,** které potřebujete pro třídu. Seznam velikostí, které jsou k dispozici, najdete v části [Velikosti virtuálních počítače.](#vm-sizes) 
    3. Vyberte **bitovou kopii virtuálního počítače,** kterou chcete použít pro testovací prostředí učebny. Pokud vyberete bitovou kopii Linuxu, zobrazí se možnost povolit připojení ke vzdálené ploše. Podrobnosti naleznete v [tématu Povolení připojení ke vzdálené ploše pro Linux](how-to-enable-remote-desktop-linux.md).

        Pokud jste se přihlásili pomocí přihlašovacích údajů vlastníka účtu testovacího prostředí, zobrazí se možnost povolit další image pro testovací prostředí. Další informace naleznete [v tématu Povolení obrázků v době vytvoření testovacího prostředí](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation).
    4. Zkontrolujte **celkovou cenu za hodinu** zobrazenou na stránce. 
    6. Vyberte **Uložit**.

        ![Nové okno laboratoře](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > Pokud byl účet testovacího prostředí nakonfigurován tak, aby [umožnil tvůrci testovacího prostředí vybrat možnost umístění testovacího prostředí,](allow-lab-creator-pick-lab-location.md) zobrazí se možnost vybrat umístění pro testovací prostředí. 
4. Na stránce **přihlašovací údaje virtuálního počítače** zadejte výchozí pověření pro všechny virtuální počítače v testovacím prostředí.
    1. Zadejte **jméno uživatele** pro všechny virtuální počítače v testovacím prostředí.
    2. Zadejte **heslo** tohoto uživatele. 

        > [!IMPORTANT]
        > Uživatelské jméno a heslo si poznamenejte. Znovu se už nezobrazí.
    3. Zakázat **Použít stejné heslo pro všechny virtuální počítače** možnost, pokud chcete, aby studenti nastavit vlastní hesla. Tento krok je **volitelný**. 

        Učitel se může rozhodnout použít stejné heslo pro všechny virtuální počítače v testovacím prostředí nebo umožnit studentům nastavit hesla pro své virtuální počítače. Ve výchozím nastavení je toto nastavení povoleno pro všechny obrázky Windows a Linux s výjimkou Ubuntu. Když vyberete virtuální ms **Ubuntu,** toto nastavení je zakázáno, takže studenti budou vyzváni k nastavení hesla při prvním přihlášení.  

        ![Nové okno laboratoře](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. Potom vyberte **Další** na stránce **přihlašovacíúdaje virtuálního počítače.** 
5. Na stránce **Zásady testovacího prostředí** postupujte takto:
    1. Zadejte počet hodin přidělených pro každého uživatele **(kvóta pro každého uživatele)** mimo plánovaný čas testovacího prostředí. 
    2. Pro **možnost automatickévypnutí virtuálních počítačů** určete, zda chcete, aby se virtuální počítač automaticky vypnul, když se uživatel odpojí. Můžete také určit, jak dlouho má virtuální ms čekat, než se uživatel znovu připojí, než se automaticky vypne. Další informace najdete [v tématu Povolení automatického vypnutí virtuálních počítačů při odpojení](how-to-enable-shutdown-disconnect.md).
    3. Potom vyberte **Dokončit**. 

        ![Kvóta pro každého uživatele](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. Měli byste vidět na následující obrazovce, která zobrazuje stav vytvoření šablony virtuálního počítače. Vytvoření šablony v testovacím prostředí může trvat až 20 minut. 

    ![Stav vytvoření šablony virtuálního počítače](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Na stránce **Šablona** postupujte takto: Tyto kroky jsou **volitelné** pro kurz.

    2. Vyberte **Connect** (Připojit) a připojte se k virtuálnímu počítači šablony. Pokud se jedná o virtuální počítač se šablonou Linuxu, můžete zvolit, zda se chcete připojit pomocí SSH nebo RDP (pokud je rdp povolen).
    1. Vyberte **Obnovit heslo** pro obnovení hesla pro virtuální počítače. 
    1. Nainstalujte a nakonfigurujte na virtuálním počítači šablony požadovaný software. 
    1. **Zastavte** virtuální počítač.  
    1. Zadejte **popis** šablony.
10. Na stránce **Šablona** vyberte **Publikovat** na panelu nástrojů. 

    ![Tlačítko Publikovat šablonu](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Publikování nejde vrátit zpět. 
8. Na stránce **Šablony publikování** zadejte počet virtuálních počítačů, které chcete vytvořit v testovacím prostředí, a pak vyberte **Publikovat**. 

    ![Šablona publikování – počet virtuálních počítače](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. Na stránce se zobrazí **stav publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Přepněte na stránku **fondu virtuálních počítačů** výběrem virtuálních počítačů v levém menu nebo výběrem dlaždice Virtuální počítače. Zkontrolujte, zda se zobrazují virtuální počítače, které jsou ve stavu **Nepřiřazené.** Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    Na této stránce provedete následující úkoly (neprováděle tyto kroky pro kurz. Tyto kroky jsou pouze pro vaši informaci.): 
    
    1. Pokud chcete změnit kapacitu testovacího prostředí (počet virtuálních počítačů v testovacím prostředí), vyberte na panelu nástrojů **možnost Kapacita** testovacího prostředí.
    2. Pokud chcete spustit všechny virtuální počítače najednou, vyberte **Spustit vše** na panelu nástrojů. 
    3. Pokud chcete spustit konkrétní virtuální hod, vyberte šipku dolů ve **stavu**a pak vyberte **Start**. Virtuální počítač můžete spustit taky výběrem virtuálního počítače v prvním sloupci a potom výběrem **start** na panelu nástrojů.                

### <a name="vm-sizes"></a>Velikost virtuálních počítačů  

| Velikost | Cores | Paměť RAM | Popis | 
| ---- | ----- | --- | ----------- | 
| Krátkodobé používání | 2 | 3,5 GB | Tato velikost je nejvhodnější pro příkazový řádek, otevření webového prohlížeče, webové servery s nízkým provozem, malé až střední databáze. |
| Střednědobé používání | 4 | 7 GB | Tato velikost je nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy | 
| Střední (vnořená virtualizace) | 4 | 16 GB | Tato velikost je nejvhodnější pro relační databáze, ukládání do mezipaměti v paměti a analýzy. Tato velikost také podporuje vnořenou virtualizaci. <p>Tuto velikost lze použít ve scénářích, kde každý student potřebuje více virtuálních počítačů. Učitelé mohou pomocí vnořené virtualizace nastavit několik malých vnořených virtuálních počítačů uvnitř virtuálního počítače. </p> |
| Dlouhodobé používání | 8 | 32 GB | Tato velikost je nejvhodnější pro aplikace, které potřebují rychlejší procesory, lepší výkon místního disku, velké databáze, velké mezipaměti. Tato velikost také podporuje vnořenou virtualizaci. |  
| Malý GPU (vizualizace) | 6 | 56 GB | Tato velikost je nejvhodnější pro vzdálenou vizualizaci, streamování, hraní her, kódování pomocí architektur, jako jsou OpenGL a DirectX. | 
| Malý GPU (výpočetní) | 6 | 56 GB | Tato velikost je nejvhodnější pro aplikace náročné na výpočetní výkon a sítě, jako je umělá inteligence a aplikace pro hluboké učení. | 
| Střední GPU (vizualizace) | 12 | 112 GB | Tato velikost je nejvhodnější pro vzdálenou vizualizaci, streamování, hraní her, kódování pomocí architektur, jako jsou OpenGL a DirectX. | 

> [!NOTE]
> Azure Lab Services automaticky nainstaluje a nakonfiguruje potřebné ovladače GPU pro vás při vytváření testovacího prostředí s image GPU.  

## <a name="view-all-classroom-labs"></a>Zobrazit všechny učebny
1. Přejděte na [portál Azure Lab Services](https://labs.azure.com).
2. Vyberte **Sign in** (Přihlásit se). Vyberte nebo zadejte **ID uživatele,** který je členem role **Tvůrce testovacího prostředí** v účtu testovacího prostředí, a zadejte heslo. Azure Lab Services podporuje účty organizací a účty Microsoft. 
3. Potvrďte, že vidíte všechny testovací prostředí ve vybraném účtu testovacího prostředí. Na dlaždici testovacího prostředí uvidíte počet virtuálních počítačů v testovacím prostředí a kvótu pro každého uživatele (mimo plánovaný čas).

    ![Všechny laboratoře](../media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní části vyberte jiný účet testovacího prostředí. Zobrazí se testovací prostředí ve vybraném účtu testovacího prostředí. 

## <a name="delete-a-classroom-lab"></a>Odstranění testovacího prostředí ve třídě
1. Na dlaždici pro testovací prostředí vyberte v rohu tři tečky (...) a pak vyberte **Odstranit**. 

    ![Tlačítko Odstranit](../media/how-to-manage-classroom-labs/delete-button.png)
3. V dialogovém okně **Odstranit testovací prostředí** vyberte **Odstranit,** abyste pokračovali v odstraňování. 

## <a name="switch-to-another-classroom-lab"></a>Přepnutí do jiné laboratoře ve třídě
Chcete-li přepnout do jiné laboratoře učebny z aktuálního, vyberte rozevírací seznam testovacích prostředí v účtu testovacího prostředí v horní části.

![Vyberte testovací prostředí z rozevíracího seznamu v horní části](../media/how-to-manage-classroom-labs/switch-lab.png)

Můžete také vytvořit nové testovací prostředí pomocí **nové testovací ho testovacího prostředí** v tomto rozevíracím seznamu. 

> [!NOTE]
> Ke správě testovacích prostředí můžete také použít modul Az.LabServices PowerShell (preview). Další informace naleznete na [domovské stránce Az.LabServices na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

Pokud chcete přepnout na jiný účet testovacího prostředí, vyberte rozevírací soubor vedle účtu testovacího prostředí a vyberte jiný účet testovacího prostředí. 

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí nastavte a publikujte šablony](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel laboratoře, přístup k učebně labs](how-to-use-classroom-lab.md)

