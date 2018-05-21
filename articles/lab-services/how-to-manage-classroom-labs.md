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
ms.openlocfilehash: e388045e839c19c68ad2c00f31d74c73e107872c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Spravovat učebny labs v prostředí služby Azure (dříve Azure DevTest Labs)
Tento článek popisuje, jak vytvořit a nakonfigurovat Učebna testovacího prostředí, zobrazit všechny učebny labs nebo odstranit Učebna testovacího prostředí.

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com).
2. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí v učebně. 
    2. Vyberte **velikost** virtuálního počítače, který chcete v učebně použít.
    3. Vyberte **image**, která se má použít pro vytvoření virtuálního počítače.
    4. Zadejte **výchozí pověření** pro protokolování do virtuálních počítačů v testovacím prostředí.
    7. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Zobrazí se **domovská stránka** testovacího prostředí. 
    
    ![Domovská stránka testovacího prostředí v učebně](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurace zásad používání

1. Vyberte **Usage policy** (Zásady používání). 
2. V nastavení **Usage policy** (Zásady používání) zadejte **počet uživatelů**, kteří mohou testovací prostředí používat.
3. Vyberte **Uložit**. 

    ![Zásady používání](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Nastavení šablony
Šablonu v testovacím prostředí je to image základní virtuální počítač, ze kterého se všichni uživatelé virtuální počítače se vytvoří. Nastavte virtuální počítač šablony tak, aby byl nakonfigurován přesně podle toho, co chcete uživatelům testovacího prostředí poskytovat. Můžete zadat název a popis šablony, kterou uvidí uživatelé testovacího prostředí. Nastavte viditelnost šablony pro veřejné instance šablony virtuálního počítače zpřístupnit uživatelům testovacího prostředí.  

### <a name="set-template-title-and-description"></a>Sada šablonu název a popis
1. V části **Template** (Šablona) vyberte **Edit** (Upravit) (ikona tužky) pro šablonu. 
2. V okně **User view** (Zobrazení uživatele) zadejte **název** pro šablonu.
3. Zadejte **popis** pro šablonu.
4. Vyberte **Uložit**.

    ![Popis testovacího prostředí v učebně](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Zveřejnění instancí šablony 
Jakmile nastavíte viditelnost šablony na **veřejnou**, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které jsou vytvořené v tomto procesu je stejné jako maximální počet uživatelů povolených v testovacím prostředí, kterou můžete nastavit v zásadách využití testovací prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona.  

1. V části **Template** (Šablona) vyberte **Visibility** (Viditelnost). 
2. Na stránce **Availability** (Dostupnost) vyberte **Public** (Veřejné).
    
    > [!IMPORTANT]
    > Jakmile je šablona veřejně dostupná, nemůže být přístup k ní změněn na privátní. 
3. Vyberte **Uložit**.

    ![Dostupnost](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům

1. Vyberte dlaždici **User registration** (Registrace uživatelů).
2. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Zobrazit všechny labs učebny
1. Přejděte na [portál Azure testovacího prostředí služeb](https://labs.azure.com).
2. Zkontrolujte, jestli všechny labs v účtu vybrané testovacího prostředí. 

    ![Všechny labs](./media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní a vyberte účet jiné testovacího prostředí. Zobrazí labs v účtu vybrané testovacího prostředí. 

## <a name="delete-a-classroom-lab"></a>Odstranit Učebna testovacího prostředí
1. Na dlaždici pro testovací prostředí vyberte v horním tři tečky (...). 

    ![Výběr testovacího prostředí](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Vyberte **Odstranit**. 

    ![Odstranění tlačítka](./media/how-to-manage-classroom-labs/delete-button.png)
3. Na **odstranění testovacího prostředí** dialogové okno, vyberte **odstranit**. 

    ![Dialogové okno Odstranit](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Další postup
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](how-to-manage-classroom-labs.md)
- [Nastavení vlastního testovacího prostředí](tutorial-create-custom-lab.md)
