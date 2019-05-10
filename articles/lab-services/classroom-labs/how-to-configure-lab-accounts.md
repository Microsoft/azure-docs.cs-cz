---
title: Konfigurace účtů testovacího prostředí ve službě Azure Lab Services | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat účet testovacího prostředí po jeho vytvoření.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: ba469c038f04a31a57e798b97b5120bec573feae
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414039"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurace účtů testovacího prostředí ve službě Azure Lab Services 
Ve službě Azure Lab Services účet testovacího prostředí je kontejner pro testovací prostředí spravované typy, jako jsou testovací prostředí v učebnách. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytuje přístup k vlastníků testovacího prostředí, kteří můžou vytvářet testovací prostředí v rámci účtu. Tento článek popisuje, jak vytvořit účet testovacího prostředí, zobrazit všechny účty testovacího prostředí nebo odstranění účtu služby testovacího prostředí.

## <a name="connect-with-a-peer-virtual-network"></a>Spojte se s partnerská virtuální síť
Připojení virtuální sítě jako partnerské sítě k virtuální síti testovacího prostředí, postupujte podle těchto kroků:

1. Na **účet testovacího prostředí** stránce **Labs konfigurace** v nabídce vlevo.

    ![Stránka Konfigurace testovacích prostředí](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Pro **partnerská virtuální síť**vyberte **povoleno** nebo **zakázané**. Výchozí hodnota je **zakázané**. Pokud chcete povolit partnerská virtuální síť, proveďte následující kroky: 
    1. Vyberte **povolené**.
    2. Vyberte **VNet** z rozevíracího seznamu. 
3. Na panelu nástrojů vyberte **Uložit**. 

Testovací prostředí vytvořené v rámci tohoto účtu jsou připojené k vybrané virtuální síti. Přístupem k prostředkům ve vybrané virtuální síti. Další informace najdete v tématu [připojit síť testovacího prostředí s partnerská virtuální síť v Azure Lab Services](how-to-connect-peer-virtual-network.md).

Když vyberete virtuální síť pro **partnerská virtuální síť** pole, **Autor testovacího prostředí povolit vybrat umístění testovacího prostředí** možnost je zakázaná. Je to proto, že testovací prostředí v rámci účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí můžou připojit k prostředkům v partnerské virtuální sítě. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Povolit Autor testovacího prostředí a vyberte umístění pro testovací prostředí
Můžete povolit Autor testovacího prostředí vytvářet testovací prostředí v jiném umístění než umístění účtu testovacího prostředí pomocí následujících kroků: 

1. Na **účet testovacího prostředí** stránce **Labs konfigurace** v nabídce vlevo.
2. Pro **Autor testovacího prostředí povolit vybrat umístění testovacího prostředí**vyberte **povoleno** Pokud chcete, aby autor testovacího prostředí, abyste mohli vybrat umístění pro testovací prostředí. Pokud je zakázaná, tato praktická cvičení se automaticky vytvoří ve stejném umístění, ve které existuje účet testovacího prostředí. 
    
    Toto pole je zakázáno, pokud vyberete virtuální síť pro **partnerská virtuální síť** pole. Je to proto, že testovací prostředí v rámci účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí pro jejich přístupu k prostředkům v partnerské virtuální síti. 
1. Na panelu nástrojů vyberte **Uložit**. 

    ![Konfigurace nastavení umístění testovacího prostředí](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Zadejte rozsah adres pro virtuální počítače v testovacím prostředí
Následující postup obsahuje kroky k zadejte rozsah adres pro virtuální počítače v testovacím prostředí. Při aktualizaci rozsahu, který jste dříve zadali, rozsah adres upravené platí jenom pro virtuální počítače, které jsou vytvořeny po provedení změny. 

Při zadávání rozsahu adres, který by měl mít na paměti, tady jsou některá omezení. 

- Předpona, která musí být menší než nebo rovna hodnotě 23. 
- Pokud chcete účet testovacího prostředí je v partnerském vztahu virtuální sítě, rozsah zadaná adresa nesmí překrývat s rozsahem adres z partnerské virtuální síti.

1. Na **účet testovacího prostředí** stránce **Labs konfigurace** v nabídce vlevo.
2. Pro **rozsah adres** pole, zadejte rozsah adres pro virtuální počítače, které budou vytvořeny v testovacím prostředí. Rozsah adres by měl být v zápisu classless Inter-Domain routing (CIDR) (Příklad: 10.20.0.0/23). Virtuální počítače v testovacím prostředí se vytvoří v tomto rozsahu adres.
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Konfigurovat rozsah adres](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud máte v úmyslu použít k vytvoření testovacího prostředí v učebně stejný uživatelský účet, můžete tento krok přeskočit. Pokud chcete k vytvoření testovacího prostředí v učebně použít jiný uživatelský účet, postupujte takto: 

Přidejte vyučující do role **Autor testovacího prostředí**, abyste jim poskytli oprávnění k vytvoření testovacích prostředí pro jejich třídy:

1. Na **účet testovacího prostředí** stránce **řízení přístupu (IAM)** a klikněte na tlačítko **+ přidat přiřazení role** na panelu nástrojů. 

    ![Řízení přístupu -> tlačítko Přidat přiřazení Role](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na **přidat přiřazení role** stránce **Autor testovacího prostředí** pro **Role**, vyberte uživatele, kterou chcete přidat do role Tvůrce prostředí a vyberte **Uložit**. 

    ![Přidat Autor testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-creator.png)

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Zadejte Image z Marketplace k dispozici pro tvůrce prostředí
Jako vlastník účtu testovacího prostředí můžete určit image z Marketplace, které můžou autoři testovacích prostředí použít k vytváření testovacích prostředí v tomto účtu testovacího prostředí. 

1. V nabídce vlevo vyberte **Marketplace images** (Image z Marketplace). Ve výchozím nastavení se zobrazí úplný seznam imagí (povolených i zakázaných). Pomocí možnosti **Enabled only**/**Disabled only** (Jenom povolené / Jenom zakázané) v rozevíracím seznamu v horní části můžete seznam filtrovat, aby se v něm zobrazovaly jen povolené/zakázané image. 
    
    ![Stránka imagí v Marketplace](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    V tomto seznamu se zobrazí pouze image z Marketplace, které splňují následující podmínky:
        
    - Vytváří jeden virtuální počítač.
    - Ke zřízení virtuálních počítačů používají Azure Resource Manager.
    - Nevyžadují zakoupení dalšího licenčního plánu.
2. Pokud chcete povolenou image v Marketplace **zakázat**, proveďte některý z těchto kroků: 
    1. V posledním sloupci vyberte **…** (tři tečky) a zvolte **Disable image** (Zakázat image). 

        ![Zakázání jedné image](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Zaškrtnutím políček před názvy imagí v seznamu můžete vybrat několik imagí. Potom zvolte **Disable selected images** (Zakázat vybrané image). 

        ![Zakázání několika imagí](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Stejně tak pokud chcete některou image z Marketplace **povolit**, proveďte některý z těchto kroků: 
    1. V posledním sloupci vyberte **…** (tři tečky) a zvolte **Enable image** (Povolit image). 
    2. Zaškrtnutím políček před názvy imagí v seznamu můžete vybrat několik imagí. Potom zvolte **Enable selected images** (Povolit vybrané image). 




## <a name="next-steps"></a>Další postup
Viz následující články:

- [Jako vlastník testovacího prostředí vytvoření a správa testovacích prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurovat a spravovat využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel, který testovací prostředí přístup k testovacím prostředím v učebnách](how-to-use-classroom-lab.md)
