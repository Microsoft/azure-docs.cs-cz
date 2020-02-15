---
title: Konfigurace účtů testovacího prostředí v Azure Lab Services | Microsoft Docs
description: Tento článek popisuje, jak vytvořit účet testovacího prostředí, Zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí v Azure Lab Services.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 37a657093fd55ce752095417fe744f83946962db
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210567"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Konfigurace účtů testovacího prostředí v Azure Lab Services 
V Azure Lab Services účet testovacího prostředí je kontejner pro spravované typy testovacích prostředí, jako je například učeben Labs. Správce nastaví účet testovacího prostředí pomocí Azure Lab Services a poskytne přístup k vlastníkům testovacího prostředí, kteří můžou v účtu vytvářet Labs. Tento článek popisuje, jak vytvořit účet testovacího prostředí, Zobrazit všechny účty testovacího prostředí nebo odstranit účet testovacího prostředí.

## <a name="connect-with-a-peer-virtual-network"></a>Připojení k partnerské virtuální síti
Pokud chcete připojit virtuální síť jako rovnocennou síť k virtuální síti testovacího prostředí, postupujte podle těchto kroků:

1. Na stránce **účet testovacího prostředí** v nabídce vlevo vyberte **Konfigurace Labs** .

    ![Konfigurační stránka Labs](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. V případě **partnerské virtuální sítě**vyberte možnost **povoleno** nebo **zakázáno**. Výchozí hodnota je **zakázaná**. Pokud chcete povolit partnerský virtuální síť, proveďte následující kroky: 
    1. Vyberte **Povoleno**.
    2. Z rozevíracího seznamu vyberte **virtuální síť** . 
3. Na panelu nástrojů vyberte **Uložit**. 

Laboratoře vytvořené v tomto účtu jsou připojené k vybrané virtuální síti. Můžou mít přístup k prostředkům ve vybrané virtuální síti. Další informace najdete v tématu [připojení sítě testovacího prostředí k partnerské virtuální síti v Azure Lab Services](how-to-connect-peer-virtual-network.md).

Když vyberete virtuální síť pro pole **rovnocenná virtuální síť** , možnost **Povolení testovacího prostředí pro výběr umístění testovacího** prostředí je zakázaná. Je to proto, že laboratoře v účtu testovacího prostředí musí být ve stejné oblasti jako účet testovacího prostředí, aby se připojovaly k prostředkům v partnerské virtuální síti. 

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Zadejte rozsah adres pro virtuální počítače v testovacím prostředí.
Následující postup obsahuje kroky k určení rozsahu adres pro virtuální počítače v testovacím prostředí. Pokud aktualizujete rozsah, který jste předtím zadali, vztahuje se upravený rozsah adres pouze na virtuální počítače, které byly vytvořeny po provedení změny. 

Tady jsou některá omezení, která určují rozsah adres, které byste měli mít na paměti. 

- Předpona musí být menší nebo rovna 23. 
- Pokud je virtuální síť v partnerském vztahu k účtu testovacího prostředí, zadaný rozsah adres se nemůže překrývat s rozsahem adres z partnerské virtuální sítě.

1. Na stránce **účet testovacího prostředí** v nabídce vlevo vyberte **Konfigurace Labs** .
2. V poli **Rozsah adres** zadejte rozsah adres pro virtuální počítače, které budou vytvořeny v testovacím prostředí. Rozsah adres by měl být v notaci směrování mezi doménami (například: 10.20.0.0/23). Virtuální počítače v testovacím prostředí se vytvoří v tomto rozsahu adres.
3. Na panelu nástrojů vyberte **Uložit**. 

    ![Konfigurace rozsahu adres](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Přidání uživatele do role Autor testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud máte v úmyslu použít k vytvoření testovacího prostředí v učebně stejný uživatelský účet, můžete tento krok přeskočit. Pokud chcete k vytvoření testovacího prostředí v učebně použít jiný uživatelský účet, postupujte takto: 

Přidejte vyučující do role **Autor testovacího prostředí**, abyste jim poskytli oprávnění k vytvoření testovacích prostředí pro jejich třídy:

1. Na stránce **účet testovacího prostředí** vyberte **řízení přístupu (IAM)** a klikněte na tlačítko **+ Přidat přiřazení role** na panelu nástrojů. 

    ![Access Control – tlačítko Přidat přiřazení role >](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stránce **Přidat přiřazení role** vyberte možnost **Autor testovacího prostředí** pro **role**, vyberte uživatele, kterého chcete přidat do role testovací tvůrci, a vyberte **Uložit**. 

    ![Přidat autora testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Pokud přidáváte neúčet Microsoftého uživatele jako tvůrce testovacího prostředí, přečtěte si část [Přidání uživatele bez účet Microsoft jako tvůrce testovacího prostředí](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Určení imagí Marketplace dostupných pro tvůrci testovacího prostředí
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

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Přidání uživatele bez účet Microsoft jako autora testovacího prostředí
Pokud chcete přidat uživatele jako tvůrce testovacího prostředí, použijte své e-mailové účty. Můžou se používat tyto typy e-mailových účtů:

- E-mailový účet, který poskytuje Office 365 Azure Active Directory vaší univerzity (AAD). 
- E-mailový účet Microsoftu, například `@outlook.com`, `@hotmail.com`, `@msn.com`nebo `@live.com`.
- E-mailový účet, který není od Microsoftu, který poskytuje společnost Yahoo nebo Google. Tyto typy účtů však musí být propojeny s účet Microsoft.
- Účet GitHub. Tento účet musí být propojený s účet Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Použití e-mailového účtu jiného než Microsoftu
Tvůrci a instruktoři testovacího prostředí můžou používat e-mailové účty jiných společností než Microsoftu k registraci a přihlášení k prostředí učebny.  Přihlášení k portálu služby testovacího prostředí však vyžaduje, aby instruktory nejprve vytvořily účet Microsoft propojená s jejich e-mailovou adresou jinou než Microsoftu.

Mnoho instruktorů už může mít účet Microsoft propojených s vašimi e-mailovými adresami, které nejsou od Microsoftu. Například instruktori již účet Microsoft, pokud používal svou e-mailovou adresu s jinými produkty nebo službami společnosti Microsoft, jako je Office, Skype, OneDrive nebo Windows.  

Když se instruktor přihlásí na portál služby Lab Services, zobrazí se výzva k zadání e-mailové adresy a hesla. Pokud se instruktor pokusí přihlásit pomocí neúčet Microsoft, který nemá účet Microsoft propojený, instruktor dostane následující chybovou zprávu: 

![Chybová zpráva](../media/how-to-configure-student-usage/cant-find-account.png)

Chcete-li se zaregistrovat účet Microsoft, instruktory by měly přejít na [http://signup.live.com](http://signup.live.com).  


### <a name="using-a-github-account"></a>Použití účtu GitHub
Instruktoři můžou k registraci a přihlášení k prostředí učebny použít taky existující účet GitHubu. Pokud už instruktor má účet Microsoft propojený se svým účtem GitHubu, může se přihlásit a zadat heslo, jak je znázorněno v předchozí části. Pokud ještě neodkazují svůj účet GitHubu na účet Microsoft, měly by si vybrat **Možnosti přihlášení**:

![Odkaz Možnosti přihlášení](../media/how-to-configure-student-usage/signin-options.png)

Na stránce **Možnosti přihlášení** vyberte možnost **Přihlásit se pomocí GitHubu**.

![Přihlaste se pomocí odkazu na GitHub](../media/how-to-configure-student-usage/signin-github.png)

Nakonec se zobrazí výzva k vytvoření účet Microsoft, která je propojená s jejich účtem GitHubu. K tomu dochází automaticky, když instruktor vybere **Next (další**).  Instruktor se pak hned přihlásí a připojí k vývojovému prostředí učebny.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatické vypnutí virtuálních počítačů při odpojení
Po odpojení připojení ke vzdálené ploše můžete povolit nebo zakázat automatické vypnutí virtuálních počítačů s Windows Lab (šablona nebo student). Můžete také určit, jak dlouho by měly virtuální počítače čekat na opětovné připojení uživatele, než se automaticky vypíná.

![Nastavení automatického vypnutí v účtu testovacího prostředí](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Toto nastavení se vztahuje na všechny laboratoře vytvořené v účtu testovacího prostředí. Vlastník testovacího prostředí může toto nastavení přepsat na úrovni testovacího prostředí. Změna tohoto nastavení v účtu testovacího prostředí bude mít vliv jenom na laboratoře, které se vytvoří po provedení změny.

Další informace o tom, jak vlastník testovacího prostředí může nakonfigurovat toto nastavení na úrovni testovacího prostředí, najdete v [tomto článku](how-to-enable-shutdown-disconnect.md) .

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
