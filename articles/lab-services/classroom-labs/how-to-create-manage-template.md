---
title: Správa šablony testovacího prostředí učebny v Azure Lab Services | Microsoft Docs
description: Naučte se, jak vytvořit a spravovat šablonu prostředí učebny v Azure Lab Services.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: f0fc1e143ce7d271d5faaa8dda0eb40cdfc9e006
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332751"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Vytvoření a Správa šablony učebny v Azure Lab Services
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurovaný přesně podle toho, co chcete uživatelům testovacího prostředí poskytnout. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Pak publikujete šablonu, aby byly k dispozici instance virtuálního počítače šablony pro vaše uživatele testovacího prostředí. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.

Tento článek popisuje, jak vytvořit a spravovat virtuální počítač šablony v laboratorním prostředí pro učebnu Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Publikování šablony při vytváření testovacího prostředí učebny
Informace o publikování šablony při vytváření testovacího prostředí pro učebnu najdete v tématu [Vytvoření testovacího prostředí pro učebnu](how-to-manage-classroom-labs.md#create-a-classroom-lab) .
 
## <a name="set-or-update-template-title-and-description"></a>Nastavit nebo aktualizovat název a popis šablony
Pomocí následujícího postupu můžete nastavit název a popis poprvé a později je aktualizovat. 

1. Na stránce **Šablona** zadejte nový **název** testovacího prostředí.  
2. Zadejte nový **Popis** šablony. Když přesunete fokus z textového pole, bude automaticky uložen. 

    ![Název a popis šablony](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Aktualizace virtuálního počítače šablony
Pomocí následujících kroků aktualizujte virtuální počítač šablony.  

1. Na stránce **Šablona** vyberte **přizpůsobit šablonu** na panelu nástrojů. 

    ![Tlačítko přizpůsobit šablonu](../media/how-to-create-manage-template/customize-template-button.png)
2. V dialogovém okně **přizpůsobit šablonu** vyberte **pokračovat**. Po spuštění šablony a provedení změn už nebude mít stejné nastavení jako virtuální počítače, které jsou naposledy publikované pro vaše uživatele. Změny šablony se neprojeví na stávajících virtuálních počítačích vašich uživatelů, dokud je znovu nepublikujete.

    ![Dialogové okno přizpůsobit](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Kliknutím na tlačítko **připojit k šabloně** na panelu nástrojů se připojte k virtuálnímu počítači šablony a postupujte podle pokynů. Pokud se jedná o počítač s Windows, zobrazí se možnost stáhnout soubor RDP. 

    ![Připojení k šabloně virtuálního počítače](../media/how-to-create-manage-template/connect-template-vm.png)
1. Nainstalujte software, který studenti v testovacím prostředí potřebují (například sadu Visual Studio, Průzkumníka služby Azure Storage atd.). 
2. Odpojte se od šablony virtuálního počítače (ukončete relaci vzdálené plochy). 
3. Kliknutím na **zastavit šablonu** **zastavte** virtuální počítač šablony. 
4. Pokud chcete **publikovat** aktualizovaný virtuální počítač šablony, postupujte podle kroků v následující části. 

## <a name="publish-the-template-vm"></a>Publikování šablony virtuálního počítače  
Pokud šablonu nepublikujete během vytváření testovacího prostředí, můžete ji publikovat později. Před publikováním se můžete chtít připojit k virtuálnímu počítači šablony a aktualizovat ho pomocí libovolného softwaru. Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů vytvořených v tomto procesu je počet virtuálních počítačů, které jste zadali při prvním publikování nebo které jste zadali na stránce fondu virtuálních počítačů. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. 

1. Na stránce **Šablona** vyberte **publikovat** na panelu nástrojů. 
1. V okně **publikování zprávy šablony** Zkontrolujte zprávu a vyberte **publikovat**. Tento proces může určitou dobu trvat v závislosti na tom, kolik virtuálních počítačů se vytváří.

    ![Tlačítko Publikovat](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Jakmile je šablona publikovaná, nejde to vrátit. Šablonu můžete publikovat i v takovém případě. 
4. Stav procesu publikování můžete zobrazit na stránce šablony. Počkejte, až se stav šablony změní na **Publikováno**. 

    ![Stav publikování](../media/how-to-create-manage-template/publish-status.png)
1. Přepněte na stránku **Virtual machines** (Virtuální počítače) a zkontrolujte, že se zobrazí virtuální počítače ve stavu **Unassigned** (Nepřiřazeno). Tyto virtuální počítače ještě nejsou přiřazené ke studentům. Počkejte na vytvoření virtuálních počítačů. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Můžete je spustit na této stránce nebo nechat své studenty začít s virtuálními počítači. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
