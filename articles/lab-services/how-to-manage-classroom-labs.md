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
ms.date: 04/20/2018
ms.author: spelluru
ms.openlocfilehash: 17544275f921486529518e37eb171cd0b4f26791
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="manage-classroom-labs-in-azure-lab-services-formerly-azure-devtest-labs"></a>Spravovat učebny labs v prostředí služby Azure (dříve Azure DevTest Labs)
Tento článek popisuje, jak vytvořit a nakonfigurovat Učebna testovacího prostředí, zobrazit všechny učebny labs nebo odstranit Učebna testovacího prostředí.

## <a name="create-a-classroom-lab"></a>Vytvoření Učebna testovacího prostředí

1. Přejděte na [Web Services testovacího prostředí Azure](https://labs.azure.com).
2. V **nového testovacího prostředí** okno, proveďte následující akce: 
    1. Zadejte **název** pro Učebna testovacího prostředí. 
    2. Vyberte **velikost** virtuálního počítače, který chcete použít v učebny.
    3. Vyberte **image** sloužící k vytvoření virtuálního počítače.
    4. Zadejte **výchozí pověření** pro protokolování do virtuálních počítačů v testovacím prostředí.
    7. Vyberte **Uložit**.

        ![Vytvoření Učebna testovacího prostředí](./media/how-to-manage-classroom-labs/new-lab-window.png)
1. Zobrazí **domovskou stránku** pro testovací prostředí. 
    
    ![Učebna testovacího prostředí domovské stránky](./media/how-to-manage-classroom-labs/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Nakonfigurujte zásady použití

1. Vyberte **zásady používání**. 
2. V **zásady používání**, nastavení, zadejte **počet uživatelů** může použít testovací prostředí.
3. Vyberte **Uložit**. 

    ![Použití zásad](./media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Nastavení šablony
Šablona v testovacím prostředí je z které všichni uživatelé virtuální počítače se vytvoří. Nastavení šablony virtuálního počítače tak, aby je nakonfigurován s přesně co byste chtěli poskytnout uživatelům testovacího prostředí. Můžete zadat název a popis šablony, kterou uvidí uživatelé testovacího prostředí a nastavit viditelnost na hodnotu "Veřejná", aby instance šablony virtuálních počítačů dostupné uživatelům testovacího prostředí.  

## <a name="set-template-title-and-description"></a>Sada šablonu název a popis
1. V **šablony** vyberte **upravit** (ikonu tužky) pro šablonu. 
2. V **zobrazení uživatelského** okno, zadejte **název** šablony.
3. Zadejte **popis** šablony.
4. Vyberte **Uložit**.

    ![Popis Učebna testovacího prostředí](./media/how-to-manage-classroom-labs/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Zveřejnit instance šablony 
Jakmile jednou nastavíte viditelnost šablonu, která má **veřejné**, testovacího prostředí služby Azure vytvoří virtuální počítače v testovacím prostředí pomocí šablony. Počet virtuálních počítačů, které jsou vytvořené v tomto procesu jsou stejné jako maximální počet uživatelů povolených v testovacím prostředí, kterou můžete nastavit v zásadách využití testovací prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablonu.  

1. Vyberte **viditelnost** v **šablony** části. 
2. V **dostupnosti** vyberte **veřejné**.
    
    > [!IMPORTANT]
    > Po veřejně dostupné šablony nelze změnit jeho přístup k privátním. 
3. Vyberte **Uložit**.

    ![Dostupnost](./media/how-to-manage-classroom-labs/public-access.png)

## <a name="send-registration-link-to-students"></a>Odeslat registrace odkaz pro studenty

1. Vyberte **registrace uživatele** dlaždici.
2. V **registrace uživatele** dialogové okno, vyberte **kopie** tlačítko. Odkaz zkopírován do schránky. Vložte jej v editoru e-mailu a odešlete e-mail na student. 

    ![Odkaz student registrace](./media/how-to-manage-classroom-labs/registration-link.png)

## <a name="view-all-classroom-labs"></a>Zobrazit všechny labs učebny
1. Přejděte na [portál Azure testovacího prostředí služeb](https://labs.azure.com).
2. Zkontrolujte, jestli všechny labs v účtu vybrané testovacího prostředí. 

    ![Všechny labs](./media/how-to-manage-classroom-labs/all-labs.png)
3. Pomocí rozevíracího seznamu v horní a vyberte účet jiné testovacího prostředí. Zobrazí labs v účtu vybrané testovacího prostředí. 

## <a name="delete-a-classroom-lab"></a>Odstranit Učebna testovacího prostředí
1. Na dlaždici pro testovací prostředí vyberte v horním tři tečky (...). 

    ![Vyberte testovací prostředí](./media/how-to-manage-classroom-labs/select-three-dots.png)
2. Vyberte **Odstranit**. 

    ![Odstranění tlačítka](./media/how-to-manage-classroom-labs/delete-button.png)
3. Na **odstranění testovacího prostředí** dialogové okno, vyberte **odstranit**. 

    ![Dialogové okno Odstranit](./media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)
 

## <a name="next-steps"></a>Další postup
Začínáme s nastavení laboratoře pomocí služby Azure testovacího prostředí:

- [Nastavit Učebna testovacího prostředí](how-to-manage-classroom-labs.md)
- [Nastavit vlastní testovací prostředí](tutorial-create-custom-lab.md)
