---
title: Konfigurace nastavení využití v učebně Labs Azure Lab Services | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat počet uživatelů pro testovací prostředí, získat je zaregistrovaných v testovacím prostředí, určit počet hodin, po které může virtuální počítač používat, a další.
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
ms.date: 06/11/2019
ms.author: spelluru
ms.openlocfilehash: 86f22864c416ad2a90bea09c02675d6eb3322308
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385619"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurace nastavení a zásad použití
Tento článek popisuje, jak přidat uživatele do testovacího prostředí, získat je zaregistrované v testovacím prostředí a určit počet hodin, po které může virtuální počítač používat, a další. 


## <a name="add-users-to-the-lab"></a>Přidat uživatele do testovacího prostředí
Pokud máte povolený **přístup omezit** , přidejte uživatele (e-mailové adresy) do seznamu.

1. V nabídce vlevo vyberte **Uživatelé** .
2. Na panelu nástrojů vyberte **Přidat uživatele** . 

    ![Tlačítko Přidat uživatele](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stránce **Přidat uživatele** zadejte e-mailové adresy uživatelů na samostatných řádcích nebo na jeden řádek oddělený středníky. 

    ![Přidat e-mailové adresy uživatele](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Uložit**. V seznamu se zobrazí e-mailové adresy uživatelů a jejich stavů (registrovaných nebo ne). 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="share-registration-link-with-students"></a>Sdílet odkaz na registraci s studenty
Chcete-li odeslat odkaz na registraci studentům, použijte jednu z následujících metod. První metoda ukazuje, jak odeslat e-maily studentům pomocí odkazu na registraci a volitelné zprávy. Druhá metoda vám ukáže, jak získat odkaz na registraci, který můžete sdílet s ostatními způsobem, jaký požadujete. 

Pokud je pro testovací prostředí povolený **přístup** s omezením, můžou k registraci do testovacího prostředí používat jenom uživatelé v seznamu uživatelů. Tato možnost je ve výchozím nastavení povolená. 

### <a name="send-email-to-users"></a>Odeslání e-mailu uživatelům
Azure Lab Services umožní učitelům e-mailové prostředí pozvánky všem nebo vybraným studentům bez použití jiného e-mailového klienta. Učitelé si můžou v seznamu najetí myší na jednotlivé studenty a zobrazit tak ikonu e-mailu pro každého studenta nebo vybrat jednoho nebo více studentů a na panelu nástrojů použít **poslat pozvánku** . Tato funkce pošle e-mail s odkazem na registraci a zprávou (pokud existuje), kterou přidal učitel. Po odeslání pozvánky se stav pozvánky změní na **pozvánku odeslanou** , aby učitelé mohli sledovat, kteří studenti již obdrželi registrační odkaz a datum odeslání.

1. Pokud na stránce již nejste, přepněte do zobrazení **Uživatelé** . 
2. Vyberte konkrétní nebo všechny uživatele ze seznamu. Chcete-li vybrat konkrétní uživatele, zaškrtněte políčka v prvním sloupci seznamu. Chcete-li vybrat možnost všichni uživatelé, zaškrtněte políčko před nadpisem prvního sloupce (**název**) nebo zaškrtněte všechna políčka pro všechny uživatele v seznamu. Stav pozvánky můžete zobrazit v tomto  seznamu.  Na následujícím obrázku je stav pozvánky pro všechny studenty nastavený na pozvánku Neodesláno. 

    ![Vybrat studenty](../media/tutorial-setup-classroom-lab/select-students.png)
1. Vyberte **ikonu e-mailu (obálka)** v jednom z řádků (nebo) vyberte **Odeslat pozvánku** na panelu nástrojů. Chcete-li zobrazit ikonu e-mailu, můžete také umístit ukazatel myši na jméno studenta v seznamu. 

    ![Poslat odkaz na registraci e-mailem](../media/tutorial-setup-classroom-lab/send-email.png)
4. Na stránce **Odeslat odkaz pro registraci e-mailem** proveďte tyto kroky: 
    1. Zadejte **volitelnou zprávu** , kterou chcete odeslat studentům. E-mail automaticky obsahuje odkaz na registraci. 
    2. Na stránce **Odeslat odkaz pro registraci e-mailem** vyberte **Odeslat**. Zobrazí se stav pozvánky, která se mění a **odesílá Pozvánka** , a pak na **pozvánku odeslanou**. 
        
        ![Odeslané pozvánky](../media/tutorial-setup-classroom-lab/invitations-sent.png)

## <a name="get-registration-link"></a>Získat odkaz na registraci
1. Přepněte do zobrazení **Uživatelé** výběrem možnosti **Uživatelé** v nabídce vlevo. 
2. Vyberte ikonu **získat registrační odkaz** .

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/dashboard-user-registration-link.png)
1. V dialogovém okně **User registration** (Registrace uživatelů) vyberte tlačítko **Copy** (Kopírovat). Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](../media/tutorial-setup-classroom-lab/registration-link.png)
2. V dialogovém okně **User registration** (Registrace uživatelů) vyberte **Close** (Zavřít). 
4. Sdílejte **registrační odkaz** s studentem, aby se mohl student zaregistrovat pro třídu. 

## <a name="view-users-registered-with-the-lab"></a>Zobrazení uživatelů zaregistrovaných do testovacího prostředí

V nabídce vlevo vyberte **Uživatelé** a zobrazí se seznam uživatelů registrovaných v testovacím prostředí. 

![Seznam uživatelů registrovaných v testovacím prostředí](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-for-users"></a>Nastavit kvóty pro uživatele
Kvóty na uživatele můžete nastavit pomocí následujících kroků: 

1. V nabídce vlevo vyberte **Uživatelé** , pokud už stránka není aktivní. 
2. Vybrat **kvótu pro jednotlivé uživatele: 10 hodin** na panelu nástrojů. 
3. Na stránce **kvóta na uživatele** zadejte počet hodin, který chcete každému uživateli (studentovi) přidělit: 
    1. **Celkový počet hodin testovacího prostředí na uživatele** Uživatelé můžou použít svoje virtuální počítače pro nastavený počet hodin (určených pro toto pole) **kromě naplánovaného času**. Pokud vyberete tuto možnost, zadejte do textového pole **počet hodin** . 

        ![Počet hodin na uživatele](../media/how-to-configure-student-usage/number-of-hours-per-user.png). 
    1. **0 hodin (pouze plán)** . Uživatelé můžou svoje virtuální počítače používat jenom během naplánovaného času nebo když se na nich stane vlastník testovacího prostředí na virtuálních počítačích.

        ![Nula hodin – pouze plánovaný čas](../media/how-to-configure-student-usage/zero-hours.png)
    4. Vyberte **Uložit**. 
5. Změněné hodnoty se zobrazí na panelu nástrojů nyní: **Kvóta na uživatele &lt;: počet hodin&gt;** 

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)



> [!IMPORTANT]
> Před odesláním odkazu na registraci studentům musí učitelům buď nastavit plán pro třídu, pokud si vyberou 0 kvótové hodiny, nebo zadat kvóty pro testovací prostředí.
>
> [Plánovaná doba spuštění virtuálních počítačů](how-to-create-schedules.md) se nepočítá s kvótou přidělenou uživateli. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů odesláním souboru CSV
Uživatele můžete také přidat tak, že nahrajete soubor CSV s e-mailovými adresami uživatelů.

1. Vytvořte soubor CSV s e-mailovými adresami uživatelů v jednom sloupci.

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na stránce **Uživatelé** testovacího prostředí vyberte **nahrát sdílený svazek clusteru** na panelu nástrojů.

    ![Tlačítko nahrát sdílený svazek clusteru](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Vyberte soubor CSV s e-mailovými adresami uživatele. Po výběru možnosti **otevřít** po výběru souboru CSV se zobrazí následující okno **Přidat uživatele** . Seznam e-mailových adres je vyplněn e-mailovými adresami ze souboru CSV. 

    ![Přidat uživatele, který se naplní pomocí e-mailových adres ze souboru CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. V okně **Přidat uživatele** vyberte **Uložit** . 
5. Ověřte, že se v seznamu uživatelů zobrazí uživatelé. 

    ![Seznam přidaných uživatelů](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Správa virtuálních počítačů uživatele
Jakmile Students zaregistruje Azure Lab Services pomocí registračního odkazu, který jste jim zadali, zobrazí se na kartě **virtuální počítače** virtuální počítače přiřazené k studentům. 

![Virtuální počítače přiřazené studentům](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Na virtuálním počítači studenta můžete provádět následující úlohy: 

- Pokud je virtuální počítač spuštěný, zastavte virtuální počítač. 
- Pokud je virtuální počítač zastavený, spusťte virtuální počítač. 
- Připojte se k virtuálnímu počítači. 
- Odstraňte virtuální počítač. 
- Zobrazuje počet hodin, po které uživatelé virtuální počítač použili. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Aktualizovat počet virtuálních počítačů v testovacím prostředí
Pokud chcete aktualizovat počet virtuálních počítačů v testovacím prostředí, proveďte na stránce **Virtual Machines** následující kroky:

1. V nabídce vlevo vyberte **virtuální počítače** . 
2. Vyberte možnost **kapacita testovacího&gt; prostředí: &lt;počet počítačů** na panelu nástrojů. 
3. Zadejte **počet** virtuálních počítačů.
4. Vyberte **Uložit**.

    ![Virtuální počítače v testovacím prostředí](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
