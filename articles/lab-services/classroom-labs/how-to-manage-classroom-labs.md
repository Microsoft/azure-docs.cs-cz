---
title: Spravovat učebny labs ve službě Azure Services testovacího prostředí | Microsoft Docs
description: Zobrazit všechny učebny labs, sdílet registrace propojit s uživatelem testovacího prostředí nebo odstranění testovacího prostředí, podívejte se, jak vytvořit a nakonfigurovat Učebna testovacího prostředí.
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
ms.date: 05/17/2018
ms.author: spelluru
ms.openlocfilehash: f8cf5a46e1d1e3242fd46900b56f7d8cf15d3fc5
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082518"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Spravovat učebny labs v prostředí služby Azure 
Tento článek popisuje, jak vytvořit a nakonfigurovat Učebna testovacího prostředí, zobrazit všechny učebny labs nebo odstranit Učebna testovacího prostředí.

## <a name="prerequisites"></a>Požadavky
Pokud chcete nastavit Učebna testovacího prostředí v účtu testovacího prostředí, musíte být členem skupiny **testovacím Creator** role v účtu testovacího prostředí. Vlastník testovacího prostředí můžete přidat uživatele k roli Creator testovacího prostředí, pomocí kroků v následujícím článku: [přidat uživatele k roli testovacím Creator](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com).
2. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí v učebně. 
    2. Vyberte **velikost** virtuálního počítače, který chcete v učebně použít.
    3. Vyberte **image**, která se má použít pro vytvoření virtuálního počítače.
    4. Zadejte **výchozí pověření** pro protokolování do virtuálních počítačů v testovacím prostředí.
    7. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](../media/how-to-manage-classroom-labs/new-lab-window.png)
1. Zobrazí **řídicí panel** pro testovací prostředí. 
    
    ![Řídicí panel Učebna testovacího prostředí](../media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurace zásad používání

1. Vyberte **Usage policy** (Zásady používání). 
2. V nastavení **Usage policy** (Zásady používání) zadejte **počet uživatelů**, kteří mohou testovací prostředí používat.
3. Vyberte **Uložit**. 

    ![Zásady používání](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Nastavení šablony
Šablona v testovacím prostředí je základní image virtuálního počítače, ze které se vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurován přesně podle toho, co chcete uživatelům testovacího prostředí poskytovat. Můžete zadat název a popis šablony, které uvidí uživatelé testovacího prostředí. Publikujte šablonu, kterou chcete zpřístupnit instance šablony virtuálních počítačů pro vaše prostředí uživatele.  

### <a name="set-template-title-and-description"></a>Sada šablonu název a popis
1. V části **Template** (Šablona) vyberte **Edit** (Upravit) (ikona tužky) pro šablonu. 
2. V okně **User view** (Zobrazení uživatele) zadejte **název** pro šablonu.
3. Zadejte **popis** pro šablonu.
4. Vyberte **Uložit**.

    ![Popis testovacího prostředí v učebně](../media/how-to-manage-classroom-labs/lab-description.png)

### <a name="set-up-the-template-vm"></a>Nastavení šablony virtuálního počítače
 Připojit k šabloně virtuálního počítače a na něj nainstalovat před zpřístupněním studenty veškerý požadovaný software. 

1. Počkejte, dokud šablony virtuálního počítače není připravena. Až bude připravený, **spustit** tlačítko by měla být povolená. Chcete-li spustit virtuální počítač, vyberte **spustit**.

    ![Spuštění šablony virtuálních počítačů](../media/tutorial-setup-classroom-lab/start-template-vm.png)
1. Chcete-li se připojit k virtuálnímu počítači, vyberte **Connect**a postupujte podle pokynů. 

    ![Připojit k šabloně virtuálního počítače](../media/tutorial-setup-classroom-lab/connect-template-vm.png)
1. Instalujte žádný software, který je požadovaný pro studenty udělat v prostředí (například Visual Studio, Azure Storage Explorer atd.). 
2. Odpojit (zavřete relaci vzdálené plochy) z šablony virtuálního počítače. 
3. **Zastavit** šablony virtuálního počítače tak, že vyberete **Zastavit**. 

    ![Zastavit šablony virtuálních počítačů](../media/tutorial-setup-classroom-lab/stop-template-vm.png)


### <a name="publish-the-template"></a>Publikovat šablony 
Při publikování šablonu služby testovacího prostředí Azure vytvoří virtuální počítače v testovacím prostředí pomocí šablony. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. 

1. Vyberte **publikovat** v **šablony** části. 

    ![Publikujete šablonu virtuálního počítače](../media/tutorial-setup-classroom-lab/public-access.png)
1. V **publikovat** vyberte **publikováno** možnost. 
2. Nyní, vyberte **publikovat** tlačítko. Tento proces může trvat čas v závislosti na tom, kolik virtuálních počítačů během vytváření, což je stejný jako počet uživatelů povolených v testovacím prostředí.
    
    > [!IMPORTANT]
    > Po publikování šablonu ji nelze zrušit jeho publikování. 
4. Přepnout **virtuální počítače** stránky a zkontrolujte, jestli virtuální počítače, které jsou v **vyřazení** stavu. Studenti, kteří nejsou přiřazeni tyto virtuální počítače ještě. 

    ![Virtuální počítače](../media/tutorial-setup-classroom-lab/virtual-machines.png)
5. Počkejte, dokud se vytvoří virtuální počítače. Musí být v **Zastaveno** stavu. Můžete spustit student virtuálního počítače, připojení k virtuálnímu počítači, zastavte virtuální počítač a odstranit virtuální počítač na této stránce. Můžete spustit na této stránce nebo to studenty spustit virtuální počítače. 

    ![Virtuální počítače v zastaveném stavu](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům

1. Přepnout **řídicí panel** zobrazení. 
2. Vyberte dlaždici **User registration** (Registrace uživatelů).

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/registration-link.png)
2. Na **registrace uživatele** dialogové okno, vyberte **Zavřít**. 

## <a name="view-all-classroom-labs"></a>Zobrazit všechny labs učebny
1. Přejděte na [portál Azure testovacího prostředí služeb](https://labs.azure.com).
2. Zkontrolujte, jestli všechny labs v účtu vybrané testovacího prostředí. 

    ![Všechny labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní a vyberte účet jiné testovacího prostředí. Zobrazí labs v účtu vybrané testovacího prostředí. 

## <a name="delete-a-classroom-lab"></a>Odstranit Učebna testovacího prostředí
1. Na dlaždici pro testovací prostředí vyberte v horním tři tečky (...). 

    ![Výběr testovacího prostředí](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Vyberte **Odstranit**. 

    ![Odstranění tlačítka](../media/how-to-manage-classroom-labs/delete-button.png)
3. Na **odstranění testovacího prostředí** dialogové okno, vyberte **odstranit**. 

    ![Dialogové okno Odstranit](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 
## <a name="manage-student-vms"></a>Správa virtuálních počítačů pro studenty
Jakmile studenty registrace s testovacím služby Azure pomocí registrace propojit zadaný, uvidíte virtuální počítače přiřazené studenty na **virtuální počítače** kartě. 

![Virtuální počítače přiřazené pro studenty](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Můžete provést následující úlohy na student virtuálních počítačů: 

- Zastavení virtuálního počítače, pokud je virtuální počítač spuštěný. 
- Spuštění virtuálního počítače, pokud je virtuální počítač je zastavena. 
- Připojte se k virtuálnímu počítači. 
- Odstraňte virtuální počítač. 

## <a name="next-steps"></a>Další postup
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](how-to-manage-classroom-labs.md)
- [Nastavení testovacího prostředí](../tutorial-create-custom-lab.md)
