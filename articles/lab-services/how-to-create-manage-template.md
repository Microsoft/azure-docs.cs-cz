---
title: Správa šablony testovacího prostředí učebny v Azure Lab Services | Microsoft Docs
description: Naučte se, jak vytvořit a spravovat šablonu prostředí učebny v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be84a05fd8b278f5176cd768191d10a20fc719e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96434273"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Vytvoření a Správa šablony učebny v Azure Lab Services
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurován přesně podle toho, co chcete uživatelům testovacího prostředí poskytovat. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Pak publikujete šablonu, aby byly k dispozici instance virtuálního počítače šablony pro vaše uživatele testovacího prostředí. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.

Tento článek popisuje, jak vytvořit a spravovat virtuální počítač šablony v laboratorním prostředí pro učebnu Azure Lab Services. 

> [!NOTE]
> Při vytváření testovacího prostředí se vytvoří virtuální počítač šablony, který ale není spuštěný. Můžete ji spustit, připojit k ní a nainstalovat libovolný nezbytný software pro testovací prostředí a pak ho publikovat. Když publikujete virtuální počítač šablony, automaticky se vypíná za vás, pokud jste to ještě neudělali. 
> 
> Virtuální počítače šablony účtují **náklady** při jejich spuštění, takže se ujistěte, že je virtuální počítač šablony vypnutý, když ho nepotřebujete používat. 


## <a name="set-or-update-template-title-and-description"></a>Nastavit nebo aktualizovat název a popis šablony
Pomocí následujícího postupu můžete nastavit název a popis poprvé a později je aktualizovat. 

1. Na stránce **Šablona** zadejte nový **název** testovacího prostředí.  
2. Zadejte nový **Popis** šablony. Když přesunete fokus z textového pole, bude automaticky uložen. 

    ![Název a popis šablony](./media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aktualizace virtuálního počítače šablony
Pomocí následujících kroků aktualizujte virtuální počítač šablony.  

1. Počkejte, než se spustí virtuální počítač šablony, a pak vyberte **připojit k šabloně** na panelu nástrojů, abyste se připojili k virtuálnímu počítači šablony, a postupujte podle pokynů. Pokud se jedná o počítač s Windows, zobrazí se možnost stáhnout soubor RDP. 
1. Jakmile se připojíte k šabloně a provedete změny, již nebude mít stejné nastavení jako virtuální počítače naposledy publikované pro vaše uživatele. Změny šablony se neprojeví na stávajících virtuálních počítačích vašich uživatelů, dokud je znovu nepublikujete.

    ![Připojení k šabloně virtuálního počítače](./media/how-to-create-manage-template/connect-template-vm.png)
    
1. Nainstalujte software, který studenti v testovacím prostředí potřebují (například sadu Visual Studio, Průzkumníka služby Azure Storage atd.). 
1. Odpojte se od šablony virtuálního počítače (ukončete relaci vzdálené plochy). 
1. Kliknutím na **zastavit šablonu** **zastavte** virtuální počítač šablony. 
1. Pokud chcete **publikovat** aktualizovaný virtuální počítač šablony, postupujte podle kroků v následující části. 

## <a name="publish-the-template-vm"></a>Publikování šablony virtuálního počítače  
V tomto kroku publikujete virtuální počítač šablony. Když publikujete virtuální počítač šablony, Azure Lab Services vytvoří virtuální počítače v testovacím prostředí pomocí šablony. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.


1. Na stránce **Šablona** vyberte **publikovat** na panelu nástrojů. 

    ![Tlačítko publikovat šablonu](./media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Publikování nejde vrátit zpět. 
2. Na stránce **publikovat šablonu** zadejte počet virtuálních počítačů, které chcete v testovacím prostředí vytvořit, a pak vyberte **publikovat**. 

    ![Šablona publikování – počet virtuálních počítačů](./media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. Na stránce se zobrazí **stav publikování** šablony. Tento proces může trvat až hodinu. 

    ![Publikování šablony – průběh](./media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Počkejte, než se publikování dokončí, a pak přepněte na stránku **fondu virtuálních počítačů** tak, že v nabídce vlevo vyberete **virtuální počítače** nebo dlaždici **virtuální počítače** . Ověřte, že se zobrazují virtuální počítače, které jsou v **nepřiřazeném** stavu. Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](./media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)
## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access Labs](how-to-use-classroom-lab.md)
