---
title: Nastavení testovacího prostředí v učebně v Azure Lab Services | Microsoft Docs
description: V tomto kurzu nastavíte testovací prostředí pro použití v učebně.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: 163763bf1203a045326c7163b5f6da9aa417d8cf
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081852"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Konfigurace testovacího prostředí v učebně
> * Odeslání odkazu pro registraci studentům

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit testovací prostředí v učebně v účtu testovacího prostředí, musíte v účtu testovacího prostředí být členem role **Autor testovacího prostředí**. Vlastník testovacího prostředí může přidat uživatele do role Autor testovacího prostředí podle postupu v následujícím článku: [Přidání uživatele do role Autor testovacího prostředí](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com).
2. Vyberte **Sign in** (Přihlásit se) a zadejte své přihlašovací údaje. 
3. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí v učebně. 
    2. Vyberte **velikost** virtuálního počítače, který chcete v učebně použít.
    3. Vyberte **image**, která se má použít k vytvoření virtuálního počítače.
    4. Zadejte **výchozí přihlašovací údaje** pro přihlašování k virtuálním počítačům v testovacím prostředí. 
    7. Vyberte **Save** (Uložit).

        ![Vytvoření testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Zobrazí se **řídicí panel** testovacího prostředí. 
    
    ![Řídicí panel testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurace zásad používání

1. Vyberte **Usage policy** (Zásady používání). 
2. V nastavení **Usage policy** (Zásady používání) zadejte **počet uživatelů**, kteří mohou testovací prostředí používat.
3. Vyberte **Save** (Uložit). 

    ![Zásady používání](../media/tutorial-setup-classroom-lab/usage-policy-settings.png)


## <a name="set-up-the-template"></a>Nastavení šablony 
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurovaný přesně podle toho, co chcete uživatelům testovacího prostředí poskytnout. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Publikujte šablonu, aby instance šablony virtuálního počítače byly dostupné všem uživatelům testovacího prostředí. 

### <a name="set-title-and-description"></a>Nastavení názvu a popisu
1. V části **Template** (Šablona) vyberte **Edit** (Upravit) (ikona tužky) u šablony. 
2. V okně **User view** (Zobrazení uživatele) zadejte **název** šablony.
3. Zadejte **popis** šablony.
4. Vyberte **Save** (Uložit).

    ![Popis testovacího prostředí v učebně](../media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="set-up-the-template-vm"></a>Nastavení šablony virtuálního počítače
 Než šablonu virtuálního počítače zpřístupníte studentům, připojíte se k ní a nainstalujete na ní požadovaný software. 

1. Počkejte, až bude šablona virtuálního počítače připravená. Jakmile bude připravená, mělo by se aktivovat tlačítko **Start** (Spustit). Pokud chcete virtuální počítač spustit, vyberte **Start** (Spustit).

    ![Spuštění šablony virtuálního počítače](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Pokud se chcete k virtuálnímu počítači připojit, vyberte **Connect** (Připojit) a postupujte podle pokynů. 

    ![Připojení k šabloně virtuálního počítače](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Nainstalujte software, který studenti v testovacím prostředí potřebují (například sadu Visual Studio, Průzkumníka služby Azure Storage atd.). 
2. Odpojte se od šablony virtuálního počítače (ukončete relaci vzdálené plochy). 
3. **Zastavte** šablonu virtuálního počítače výběrem **Stop** (Zastavit). 

    ![Zastavení šablony virtuálního počítače](../media/tutorial-setup-classroom-lab/stop-template-vm.png)

### <a name="publish-the-template"></a>Publikování šablony 
Jakmile publikujete šablonu, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. 

1. V části **Template** (Šablona) vyberte **Publish** (Publikovat). 

    ![Publikování šablony virtuálního počítače](../media/tutorial-setup-classroom-lab/public-access.png)
1. V okně **Publish** (Publikovat) vyberte možnost **Published** (Publikováno). 
2. Teď vyberte tlačítko **Publish** (Publikovat). Tento proces může chvíli trvat v závislosti na počtu vytvářených virtuálních počítačů, který je stejný jako počet uživatelů s povoleným přístupem k testovacímu prostředí.
    
    > [!IMPORTANT]
    > Jakmile je šablona publikovaná, nejde to vrátit. 
4. Přepněte na stránku **Virtual machines** (Virtuální počítače) a zkontrolujte, že se zobrazí virtuální počítače ve stavu **Unassigned** (Nepřiřazeno). Tyto virtuální počítače ještě nejsou přiřazené ke studentům. 

    ![Virtuální počítače](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Počkejte na vytvoření virtuálních počítačů. Měly by být ve stavu **Stopped** (Zastaveno). Na této stránce můžete spustit studentský virtuální počítač, připojit se k němu, zastavit ho a odstranit ho. Virtuální počítače můžete spustit na této stránce nebo jejich spuštění můžete nechat na studentech. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům

1. Přepněte na zobrazení **Dashboard** (Řídicí panel). 
2. Vyberte dlaždici **User registration** (Registrace uživatelů).

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/registration-link.png)
2. V dialogovém okně **User registration** (Registrace uživatelů) vyberte **Close** (Zavřít). 


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili testovací prostředí v učebně a nakonfigurovali ho. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

