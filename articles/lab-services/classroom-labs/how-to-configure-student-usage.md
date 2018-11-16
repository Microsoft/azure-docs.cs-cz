---
title: Konfigurovat nastavení použití v testovacím prostředím v učebnách Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat počet uživatelů pro testovací prostředí, je registrován v testovacím, řídí počet hodin, můžete použít virtuální počítač a další.
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
ms.date: 11/14/2018
ms.author: spelluru
ms.openlocfilehash: 30c033b487fe58d017080b02c257502f82338164
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710036"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurace nastavení použití a zásady
Tento článek popisuje, jak nakonfigurovat počet uživatelů pro testovací prostředí, je registrován v testovacím, řídí počet hodin, můžete použít virtuální počítač a další. 


## <a name="specify-the-number-of-users-allowed-into-the-lab"></a>Zadejte počet uživatelů povolený do testovacího prostředí

1. Vyberte **Usage policy** (Zásady používání). 
2. V nastavení **Usage policy** (Zásady používání) zadejte **počet uživatelů**, kteří mohou testovací prostředí používat.
3. Vyberte **Save** (Uložit). 

    ![Zásady používání](../media/how-to-manage-classroom-labs/usage-policy-settings.png)

## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům

1. Přepněte **uživatelé** zobrazení tak, že vyberete **uživatelé** v nabídce vlevo. 
2. Vyberte **odkazu registrace** dlaždici.

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/registration-link.png)
2. V dialogovém okně **User registration** (Registrace uživatelů) vyberte **Close** (Zavřít). 
4. Sdílejte odkaz pro registraci se studentem, aby se mohl zaregistrovat do třídy. Pokud máte **omezit možnost** povoleno nastavení a v seznamu k dispozici seznam uživatelů, proveďte následující akce:
    1. Vyberte **e-mailová adresa** uživatele v seznamu. 
    2. Zobrazit okno ve svém programu výchozí e-mailu s **na** adresu vyplněna. 
    3. Vložit **adresa URL pro registraci** jste si zkopírovali dříve. 
    4. Odeslat **e-mailu**. 

## <a name="view-users-registered-with-the-lab"></a>Zobrazení uživatelů zaregistrovaných do testovacího prostředí

Vyberte **uživatelé** v nabídce vlevo, pokud chcete zobrazit seznam uživatelů zaregistrované testovací prostředí. 

![Seznam uživatelů zaregistrovaného testovacího prostředí](../media/how-to-configure-student-usage/users-list.png)

## <a name="set-quotas-per-user"></a>Nastavení kvót na uživatele

1. Vyberte **uživatelé** v nabídce vlevo.
2. Vyberte **kvóty na uživatele: neomezená** na panelu nástrojů. 
3. Na **kvóty uživatele** stránce **omezit počet hodin, může uživatel používat virtuální počítač**. 
4. Pro **kolik hodin chcete poskytnout všem uživatelům**, zadejte počet hodin a vyberte **Uložit**. 

    ![Počet hodin na uživatele](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
5. Nyní zobrazit počet hodin na panelu nástrojů: **kvóty na uživatele: &lt;počet hodin&gt;**. 

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)

## <a name="add-users-to-the-lab"></a>Přidání uživatelů do testovacího prostředí
Pokud máte **omezit přístup** povolena, přidejte do seznamu uživatelé (e-mailové adresy).

1. Vyberte **uživatelé** v nabídce vlevo.
2. Vyberte **přidat uživatele** na panelu nástrojů. 
3. Na **přidat uživatele** stránky, zadejte e-mailové adresy uživatele na samostatných řádcích nebo na jednom řádku, oddělené středníky. 

    ![Přidat e-mailové adresy uživatele](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Uložit**. Zobrazí se e-mailové adresy uživatelů a jejich stavy (zaregistrované, nebo ne) v seznamu. 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů tím, že nahrajete soubor CSV
Nahrajete soubor .csv s e-mailové adresy uživatelů, můžete také přidat uživatele.

1. Vyberte **nahrát CSV** na panelu nástrojů.
2. Vyberte soubor CSV s e-mailové adresy uživatele. E-mailových adres by měl být v jednom sloupci otevřít pomocí aplikace Excel. 

## <a name="manage-user-vms"></a>Správa virtuálních počítačů uživatele
Jakmile studenty registru pomocí Azure Lab Services pomocí registrace propojení za předpokladu, uvidíte virtuální počítače přiřazené pro studenty na **virtuálních počítačů** kartu. 

![Virtuální počítače přiřazené pro studenty](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Můžete provádět následující úlohy na studenta virtuálního počítače: 

- Zastavení virtuálního počítače, pokud je virtuální počítač spuštěný. 
- Spuštění virtuálního počítače, pokud je virtuální počítač zastavený. 
- Připojte se k virtuálnímu počítači. 
- Odstranění virtuálního počítače. 
- Zobrazí počet hodin, že uživatelé používat virtuální počítač. 


## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)
