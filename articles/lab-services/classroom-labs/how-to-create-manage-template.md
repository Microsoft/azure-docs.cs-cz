---
title: Správa šablony testovacího prostředí učebny ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit a spravovat šablonu testovacího prostředí učebny ve službě Azure Lab Services.
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539010"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Vytvoření a správa šablony učebny ve službě Azure Lab Services
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurován přesně podle toho, co chcete uživatelům testovacího prostředí poskytovat. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Potom publikujete šablonu, abyste zpřístupnili instance virtuálního počítače šablony uživatelům testovacího prostředí. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.

Tento článek popisuje, jak vytvořit a spravovat virtuální počítač šablony v učebně testovacího prostředí Azure Lab Services. 

## <a name="set-or-update-template-title-and-description"></a>Nastavení nebo aktualizace názvu a popisu šablony
Pomocí následujících kroků můžete poprvé nastavit název a popis a později je aktualizovat. 

1. Na stránce **Šablona** zadejte nový **název** testovacího prostředí.  
2. Zadejte nový **popis** šablony. Když přesunete fokus mimo textové pole, automaticky se uloží. 

    ![Název a popis šablony](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aktualizace virtuálního počítače šablony
Pomocí následujících kroků aktualizujte virtuální počítače šablony.  

1. Počkejte, až se spustí virtuální počítač šablony, a pak na panelu nástrojů vyberte **Připojit k šabloně,** abyste se připojili k virtuálnímu počítači šablony a postupovali podle pokynů. Pokud se jedná o počítač se systémem Windows, zobrazí se možnost stažení souboru RDP. 
1. Jakmile se připojíte k šabloně a provedete změny, nebude mít již stejné nastavení jako virtuální počítače, které byly naposledy publikovány vašim uživatelům. Změny šablony se neprojeví na stávajících virtuálních počítačích uživatelů, dokud znovu nepublikujete.

    ![Připojení k šabloně virtuálního počítače](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Nainstalujte software, který studenti v testovacím prostředí potřebují (například sadu Visual Studio, Průzkumníka služby Azure Storage atd.). 
1. Odpojte se od šablony virtuálního počítače (ukončete relaci vzdálené plochy). 
1. **Zastavte** virtuální virtuální počítače šablony výběrem **možnosti Zastavit šablonu**. 
1. Postupujte podle kroků v další části **publikovat** aktualizovanou šablonu virtuálního počítače. 

## <a name="publish-the-template-vm"></a>Publikování šablony virtuálního počítače  
V tomto kroku publikujete virtuální počítače šablony. Když publikujete šablonu virtuálního počítače, Azure Lab Services vytvoří virtuální počítače v testovacím prostředí pomocí šablony. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.


1. Na stránce **Šablona** vyberte **Publikovat** na panelu nástrojů. 

    ![Tlačítko Publikovat šablonu](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Publikování nejde vrátit zpět. 
2. Na stránce **Šablony publikování** zadejte počet virtuálních počítačů, které chcete vytvořit v testovacím prostředí, a pak vyberte **Publikovat**. 

    ![Šablona publikování – počet virtuálních počítače](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Na stránce se zobrazí **stav publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Počkejte, až publikování je dokončena a přepněte na stránku **fondu virtuálních počítačů** výběrem **virtuální počítače** v levém menu nebo výběrem virtuální **počítače** dlaždice. Zkontrolujte, zda se zobrazují virtuální počítače, které jsou ve stavu **Nepřiřazené.** Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako správce vytvářejte a spravujte účty testovacího prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářejte a spravujte testovací prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel laboratoře, přístup k učebně labs](how-to-use-classroom-lab.md)
