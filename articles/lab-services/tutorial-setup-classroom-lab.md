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
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: 3e9f8d118c4413ec93b7dffcfa41b6ad4381b052
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Kurz: Nastavení testovacího prostředí v učebně 
V tomto kurzu nastavíte testovací prostředí v učebně pomocí sady virtuálních počítačů, které používají studenti v učebně.  

V tomto kurzu provedete následující akce:

> [!div class="checklist"]
> * Vytvoření testovacího prostředí v učebně
> * Konfigurace testovacího prostředí v učebně
> * Odeslání odkazu pro registraci studentům

## <a name="create-a-classroom-lab"></a>Vytvoření testovacího prostředí v učebně

1. Přejděte na [web Azure Lab Services](https://labs.azure.com).
2. V okně **New Lab** (Nové testovací prostředí) proveďte následující akce: 
    1. Zadejte **název** testovacího prostředí v učebně. 
    2. Vyberte **velikost** virtuálního počítače, který chcete v učebně použít.
    3. Vyberte **image**, která se má použít pro vytvoření virtuálního počítače.
    4. Zadejte **výchozí přihlašovací údaje** pro přihlašování do virtuálních počítačů v testovacím prostředí. 
    7. Vyberte **Uložit**.

        ![Vytvoření testovacího prostředí v učebně](./media/tutorial-setup-classroom-lab/new-lab-window.png)
1. Zobrazí se **domovská stránka** testovacího prostředí. 
    
    ![Domovská stránka testovacího prostředí v učebně](./media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)

## <a name="configure-usage-policy"></a>Konfigurace zásad používání

1. Vyberte **Usage policy** (Zásady používání). 
2. V nastavení **Usage policy** (Zásady používání) zadejte **počet uživatelů**, kteří mohou testovací prostředí používat.
3. Vyberte **Uložit**. 

    ![Zásady používání](./media/tutorial-setup-classroom-lab/usage-policy-settings.png)

## <a name="set-up-the-template"></a>Nastavení šablony 
Ze šablony se v testovacím prostředí vytváří všechny virtuální počítače uživatelů. Nastavte virtuální počítač šablony tak, aby byl nakonfigurován přesně podle toho, co chcete uživatelům testovacího prostředí poskytovat. Můžete zadat název a popis šablony, které se uživatelům testovacího prostředí zobrazí, a nastavit viditelnost na „veřejný“, aby byly instance virtuálního počítače šablony dostupné pro uživatele vašeho testovacího prostředí. 

### <a name="set-title-and-description"></a>Nastavení názvu a popisu
1. V části **Template** (Šablona) vyberte **Edit** (Upravit) (ikona tužky) pro šablonu. 
2. V okně **User view** (Zobrazení uživatele) zadejte **název** pro šablonu.
3. Zadejte **popis** pro šablonu.
4. Vyberte **Uložit**.

    ![Popis testovacího prostředí v učebně](./media/tutorial-setup-classroom-lab/lab-description.png)

### <a name="make-instances-of-the-template-public"></a>Zveřejnění instancí šablony
Jakmile nastavíte viditelnost šablony na **veřejnou**, vytvoří služba Azure Lab Services pomocí této šablony virtuální počítače v testovacím prostředí. Počet virtuálních počítačů, které se v tomto procesu vytvoří, se rovná maximálnímu počtu uživatelů, kteří mohou k testovacímu prostředí přistupovat. Tento počet můžete nastavit v zásadách používání testovacího prostředí. Všechny virtuální počítače mají stejnou konfiguraci jako šablona. 

1. V části **Template** (Šablona) vyberte **Visibility** (Viditelnost). 
2. Na stránce **Availability** (Dostupnost) vyberte **Public** (Veřejné).
    
    > [!IMPORTANT]
    > Jakmile je šablona veřejně dostupná, nemůže být přístup k ní změněn na privátní. 
3. Vyberte **Uložit**.

    ![Dostupnost](./media/tutorial-setup-classroom-lab/public-access.png)

## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům

1. Vyberte dlaždici **User registration** (Registrace uživatelů).
2. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](./media/tutorial-setup-classroom-lab/registration-link.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili testovací prostředí v učebně a nakonfigurovali ho. Pokud chcete získat informace o tom, jak může student přistupovat k virtuálnímu počítači v testovacím prostředí pomocí odkazu pro registraci, přejděte na další kurz:

> [!div class="nextstepaction"]
> [Připojení k virtuálnímu počítači v testovacím prostředí v učebně](tutorial-connect-virtual-machine-classroom-lab.md)

