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
ms.date: 02/07/2019
ms.author: spelluru
ms.openlocfilehash: 834674eb63af75088434db0f614b11c7a36e7adf
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964810"
---
# <a name="configure-usage-settings-and-policies"></a>Konfigurace nastavení použití a zásady
Tento článek popisuje, jak přidat uživatele do testovacího prostředí, je registrován v testovacím, řídí počet hodin, můžete použít virtuální počítač a další. 


## <a name="add-users-to-the-lab"></a>Přidání uživatelů do testovacího prostředí
Pokud máte **omezit přístup** povolena, přidejte do seznamu uživatelé (e-mailové adresy).

1. Vyberte **uživatelé** v nabídce vlevo.
2. Vyberte **přidat uživatele** na panelu nástrojů. 

    ![Přidání uživatelů tlačítka](../media/how-to-configure-student-usage/add-users-button.png)
1. Na **přidat uživatele** stránky, zadejte e-mailové adresy uživatele na samostatných řádcích nebo na jednom řádku, oddělené středníky. 

    ![Přidat e-mailové adresy uživatele](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Vyberte **Uložit**. Zobrazí se e-mailové adresy uživatelů a jejich stavy (zaregistrované, nebo ne) v seznamu. 

    ![Seznam uživatelů](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="send-registration-link-to-students"></a>Odeslání odkazu pro registraci studentům
Následující postup obsahuje kroky k odeslání odkazu registrace pro uživatele. Pokud **omezit přístup** je povolená pro testovací prostředí, pouze uživatelé ze seznamu uživatelů můžete použít odkaz na registraci k zaregistrování do testovacího prostředí. 

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

![Seznam uživatelů zaregistrovaného testovacího prostředí](../media/how-to-configure-student-usage/users-list-new.png)

## <a name="set-quotas-per-user"></a>Nastavení kvót na uživatele
Kvóty uživatele můžete nastavit pomocí následujících kroků: 

1. Vyberte **uživatelé** v nabídce vlevo.
2. Vyberte **kvóty na uživatele: neomezená** na panelu nástrojů. 
3. Na **kvóty uživatele** stránky, vyberte jednu z následujících možností: 
    1. **Žádný**. Uživatelé mohou používat své virtuální počítače pouze při naplánovaném čase nebo když vlastník testovacího prostředí se změní na virtuálních počítačích pro ně.
    2. **Neomezené (výchozí)**. Uživatelé mohou používat své virtuální počítače bez jakýchkoli časových omezení.
    3. **Zadejte počet hodin na uživatele**. Uživatelé můžou používat své virtuální počítače pro zadaného počtu hodin (níže uvedených) kromě naplánovaném čase. Pokud vyberete tuto možnost, zadejte **počet hodin** v textovém poli. 

        ![Počet hodin na uživatele](../media/how-to-configure-student-usage/number-of-hours-per-user.png)
    4. Vyberte **Uložit**. 
5. Nyní uvidíte změněné hodnoty na panelu nástrojů: **Kvóta na uživatele: &lt;počet hodin&gt;**. 

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/quota-per-user.png)

> [!IMPORTANT]
> [Naplánované spuštění virtuálních počítačů](how-to-create-schedules.md) nepočítá proti kvóty přidělený na uživatele. Kvóta je na dobu mimo hodiny plánu, které student stráví na virtuálních počítačích. 

### <a name="add-users-by-uploading-a-csv-file"></a>Přidání uživatelů tím, že nahrajete soubor CSV
Nahrajete soubor .csv s e-mailové adresy uživatelů, můžete také přidat uživatele.

1. Vytvořte soubor CSV s e-mailové adresy uživatelů do jednoho sloupce.

    ![Kvóta na uživatele](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Na **uživatelé** stránce testovacího prostředí, vyberte **nahrát CSV** na panelu nástrojů.

    ![Sdílený svazek clusteru tlačítko Nahrát](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Vyberte soubor CSV s e-mailové adresy uživatele. Když vyberete **otevřít** po výběru souboru CSV, zobrazí se následující **přidat uživatele** okna. Seznamu e-mailová adresa je vyplněna e-mailové adresy ze souboru CSV. 

    ![Přidat okno Uživatelé naplněný e-mailové adresy ze souboru CSV](../media/how-to-configure-student-usage/add-users-window.png)
4. Vyberte **Uložit** v **přidat uživatele** okna. 
5. Ověřte, že se uživatelé ze seznamu uživatelů. 

    ![Seznam přidaných uživatelů](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="manage-user-vms"></a>Správa virtuálních počítačů uživatele
Jakmile studenty registru pomocí Azure Lab Services pomocí registrace propojení za předpokladu, uvidíte virtuální počítače přiřazené pro studenty na **virtuálních počítačů** kartu. 

![Virtuální počítače přiřazené pro studenty](../media/how-to-manage-classroom-labs/virtual-machines-students.png)

Můžete provádět následující úlohy na studenta virtuálního počítače: 

- Zastavení virtuálního počítače, pokud je virtuální počítač spuštěný. 
- Spuštění virtuálního počítače, pokud je virtuální počítač zastavený. 
- Připojte se k virtuálnímu počítači. 
- Odstranění virtuálního počítače. 
- Zobrazí počet hodin, že uživatelé používat virtuální počítač. 

## <a name="update-number-of-virtual-machines-in-lab"></a>Aktualizace počtu virtuálních počítačů v testovacím prostředí
Pokud chcete aktualizovat počet virtuálních počítačů v testovacím prostředí, proveďte následující kroky **virtuálních počítačů** stránky:

1. Vyberte **virtuálních počítačů** v nabídce vlevo. 
2. Vyberte **kapacity testovacího prostředí: &lt;číslo&gt; tento počet počítačů:** na panelu nástrojů. 
3. Zadejte **číslo** virtuálních počítačů.
4. Vyberte **Uložit**.

    ![Virtuální počítače v testovacím prostředí](../media/how-to-configure-student-usage/number-virtual-machines.png)


## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako správce vytvářet a spravovat účty pro testovací prostředí](how-to-manage-lab-accounts.md)
- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)
