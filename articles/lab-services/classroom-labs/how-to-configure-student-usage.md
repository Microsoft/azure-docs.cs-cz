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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324379"
---
# <a name="add-and-manage-lab-users"></a>Přidat a spravovat uživatele testovacího prostředí
Tento článek popisuje, jak přidat uživatele do testovacího prostředí, získat je zaregistrované v testovacím prostředí a určit počet hodin, po které může virtuální počítač používat, a další. 


## <a name="add-users-to-the-lab"></a>Přidat uživatele do testovacího prostředí

1. V nabídce vlevo vyberte **Uživatelé** . Ve výchozím nastavení je povolená možnost **omezit přístup** . Pokud je toto nastavení zapnuté, nemůže se uživatel zaregistrovat v testovacím prostředí, i když uživatel nemá odkaz na registraci, pokud se uživatel nezobrazuje v seznamu uživatelů. Pomocí registračního odkazu, který odešlete, se můžou v testovacím prostředí zaregistrovat jenom uživatelé v seznamu. V tomto postupu přidáte uživatele do seznamu. Alternativně můžete vypnout možnost **omezit přístup**, která umožňuje uživatelům registraci v testovacím prostředí, pokud mají odkaz na registraci. 
2. Na panelu nástrojů vyberte **Přidat uživatele** a pak vyberte **Přidat e-mailové adresy**. 

    ![Tlačítko Přidat uživatele](../media/how-to-configure-student-usage/add-users-button.png)
1. Na stránce **Přidat uživatele** zadejte e-mailové adresy uživatelů na samostatných řádcích nebo na jeden řádek oddělený středníky. 

    ![Přidat e-mailové adresy uživatele](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Save** (Uložit). V seznamu se zobrazí e-mailové adresy uživatelů a jejich stavů (registrovaných nebo ne). 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů odesláním souboru CSV
Uživatele můžete také přidat tak, že nahrajete soubor CSV s e-mailovými adresami uživatelů.

1. Vytvořte soubor CSV s e-mailovými adresami uživatelů v jednom sloupci.

    ![Soubor CSV s uživateli](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na stránce **Uživatelé** testovacího prostředí vyberte **Přidat uživatele** na panelu nástrojů a pak vyberte **nahrát sdílený svazek clusteru**.

    ![Tlačítko nahrát sdílený svazek clusteru](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Vyberte soubor CSV s e-mailovými adresami uživatele. Po výběru možnosti **otevřít** po výběru souboru CSV se zobrazí následující okno **Přidat uživatele** . Seznam e-mailových adres je vyplněn e-mailovými adresami ze souboru CSV. 

    ![Přidat uživatele, který se naplní pomocí e-mailových adres ze souboru CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. V okně **Přidat uživatele** vyberte **Uložit** . 
5. Ověřte, že se v seznamu uživatelů zobrazí uživatelé. 

    ![Seznam přidaných uživatelů](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Odesílat pozvánky uživatelům
Chcete-li odeslat odkaz na registraci studentům, použijte jednu z následujících metod. První metoda ukazuje, jak odeslat e-maily studentům pomocí odkazu na registraci a volitelné zprávy. Druhá metoda vám ukáže, jak získat odkaz na registraci, který můžete sdílet s ostatními způsobem, jaký požadujete. 

Pokud je pro testovací prostředí povolený **přístup s omezením** , můžou k registraci do testovacího prostředí používat jenom uživatelé v seznamu uživatelů. Tato možnost je ve výchozím nastavení povolená. 

### <a name="invite-all-users"></a>Pozvat všechny uživatele

1. Přepněte na stránku **Uživatelé** v testovacím prostředí. 
2. Na panelu nástrojů vyberte **pozvat vše** . 
3. Zadejte **zprávu** pro uživatele. Toto je volitelný krok.
4. Pak vyberte **Odeslat**.

    ![Pozvat všechny uživatele](../media/how-to-configure-student-usage/invite-all.png)

    Stav této operace se zobrazí ve sloupci **Pozvánka** v seznamu **Uživatelé** . E-mail pozvánky bude obsahovat odkaz pro registraci, který mohou uživatelé použít k registraci v testovacím prostředí. 

### <a name="invite-selected-users"></a>Pozvat vybrané uživatele

1. V seznamu vyberte uživatele nebo více uživatelů. 
2. Pak vyberte ikonu **obálky** , kterou vidíte ve vybraném řádku (nebo) na panelu nástrojů vyberte **pozvat** . 

    ![Pozvat vybrané uživatele](../media/how-to-configure-student-usage/invite-selected-users.png)
3. V okně **poslat pozvánku e-mailem** zadejte volitelnou **zprávu**a pak vyberte **Odeslat**. 

    ![Odeslat e-mail vybraným uživatelům](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    Stav této operace se zobrazí ve sloupci **Pozvánka** v seznamu **Uživatelé** . E-mail pozvánky bude obsahovat odkaz pro registraci, který mohou uživatelé použít k registraci v testovacím prostředí.

1. Pokud na stránce již nejste, přepněte do zobrazení **Uživatelé** . 

## <a name="get-registration-link"></a>Získat odkaz na registraci
Můžete taky získat odkaz na registraci z portálu a odeslat ho pomocí vlastní e-mailové aplikace klienta. 

1. Přepněte do zobrazení **Uživatelé** výběrem možnosti **Uživatelé** v nabídce vlevo. 
2. Vybrat **... (tři tečky)** na panelu nástrojů a pak vyberte **odkaz registrace**.

    ![Odkaz pro registraci studenta](../media/how-to-configure-student-usage/registration-link-button.png)
1. V dialogovém okně **registrace uživatele** vyberte tlačítko **Kopírovat** . Odkaz se zkopíruje do schránky. Vložte ho do editoru e-mailů a e-mail odešlete studentovi. 

    ![Odkaz pro registraci studenta](../media/how-to-configure-student-usage/registration-link.png)
2. V dialogovém okně **registrace uživatele** vyberte možnost **Hotovo**. 
4. Pošlete **registrační odkaz** studentovi, aby se mohl student zaregistrovat pro třídu. 

## <a name="view-users-registered-with-the-lab"></a>Zobrazení uživatelů zaregistrovaných do testovacího prostředí

V nabídce vlevo vyberte **Uživatelé** a zobrazí se seznam uživatelů registrovaných v testovacím prostředí. 

![Seznam uživatelů registrovaných v testovacím prostředí](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Nastavit kvóty pro uživatele
Kvóty na uživatele můžete nastavit pomocí následujících kroků: 

1. V nabídce vlevo vyberte **Uživatelé** , pokud už stránka není aktivní. 
2. Vyberte **kvótu na uživatele: &lt;number @ no__t-2 hodiny** na panelu nástrojů. 
3. Na stránce **kvóta na uživatele** zadejte počet hodin, který chcete každému uživateli (studentovi) poskytnout mimo plánovaný čas třídy, a pak vyberte **Uložit**.

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)    
5. Změněné hodnoty na panelu nástrojů teď vidíte: **kvóta pro uživatele: &lt;number of hod. @ no__t-2**. 

    ![Kvóta na uživatele – za](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > [Plánovaná doba spuštění virtuálních počítačů](how-to-create-schedules.md) se nepočítá s kvótou přidělenou uživateli. Kvóta je určena pro dobu mimo plánované hodiny, kterou student stráví na virtuálních počítačích. 

## <a name="set-additional-quota-for-a-specific-user"></a>Nastavení dodatečné kvóty pro konkrétního uživatele
Pro uživatele můžete nastavit samostatnou kvótu. Uděláte to takto:

1. Vyberte uživatele (student) ze seznamu uživatelů na stránce **Uživatelé** .
2. Pak na panelu nástrojů vyberte **Upravit kvótu** . 

    ![Tlačítko Upravit kvótu](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Zadejte počet **dalších hodin** pro vybraného uživatele nebo uživatele a pak vyberte **použít**. 

    ![Dodatečná kvóta pro uživatele](../media/how-to-configure-student-usage/additional-quota.png)
4. Pro uživatele se ve sloupci **využití** zobrazí aktualizované využití. 

    ![Nové použití pro uživatele](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Účty testovacího prostředí se vytvářejí a spravují jako správce.](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
