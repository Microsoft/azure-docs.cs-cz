---
title: Přidat uživatele jako autora testovacího prostředí v Azure Lab Services
description: Tento článek ukazuje, jak přidat uživatele do role testovacího prostředí pro účet testovacího prostředí v Azure Lab Services. Tvůrci testovacího prostředí můžou vytvářet Labs v rámci tohoto účtu testovacího prostředí.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5fdeb33dab60a2fb9f1680295c355e2c7c0e775a
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91399478"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Přidání tvůrců testovacího prostředí do účtu testovacího prostředí v Azure Lab Services
V tomto článku se dozvíte, jak přidat uživatele jako tvůrci testovacích prostředí do účtu testovacího prostředí v Azure Lab Services. Tato použití pak mohou v účtu testovacího prostředí vytvořit učebnu. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Přidat uživatelský účet Microsoft do role testovacího prostředí
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud máte v úmyslu použít k vytvoření testovacího prostředí v učebně stejný uživatelský účet, můžete tento krok přeskočit. Pokud chcete k vytvoření testovacího prostředí v učebně použít jiný uživatelský účet, postupujte takto: 

Chcete-li poskytnout pedagogům oprávnění k vytváření cvičení pro své třídy, přidejte je do role **Tvůrce testovacího prostředí** :

1. Na stránce **účet testovacího prostředí** vyberte **řízení přístupu (IAM)** a klikněte na tlačítko **+ Přidat přiřazení role** na panelu nástrojů. 

    ![Access Control – tlačítko Přidat přiřazení role >](./media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stránce **Přidat přiřazení role** vyberte možnost **Autor testovacího prostředí** pro **role**, vyberte uživatele, kterého chcete přidat do role testovací tvůrci, a vyberte **Uložit**. 

    ![Přidat autora testovacího prostředí](./media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Pokud přidáváte neúčet Microsoftého uživatele jako tvůrce testovacího prostředí, přečtěte si část [Přidání uživatele bez účet Microsoft jako tvůrce testovacího prostředí](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Přidání uživatele bez účet Microsoft jako autora testovacího prostředí
Pokud chcete přidat uživatele jako tvůrce testovacího prostředí, použijte své e-mailové účty. Můžou se používat tyto typy e-mailových účtů:

- E-mailový účet, který vám poskytla vaše Azure Active Directoryá univerzita.
- E-mailový účet Microsoftu, jako `@outlook.com` je,, `@hotmail.com` `@msn.com` nebo `@live.com` .
- E-mailový účet, který není od Microsoftu, který poskytuje společnost Yahoo nebo Google. Tyto typy účtů však musí být propojeny s účet Microsoft.
- Účet GitHub. Tento účet musí být propojený s účet Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Použití e-mailového účtu jiného než Microsoftu
Tvůrci a instruktoři testovacího prostředí můžou používat e-mailové účty jiných společností než Microsoftu k registraci a přihlášení k prostředí učebny.  Přihlášení k portálu služby testovacího prostředí však vyžaduje, aby instruktory nejprve vytvořily účet Microsoft propojená s jejich e-mailovou adresou jinou než Microsoftu.

Mnoho instruktorů už může mít účet Microsoft propojených s vašimi e-mailovými adresami, které nejsou od Microsoftu. Například instruktori již účet Microsoft, pokud používal svou e-mailovou adresu s jinými produkty nebo službami společnosti Microsoft, jako je Office, Skype, OneDrive nebo Windows.  

Když se instruktor přihlásí na portál služby Lab Services, zobrazí se výzva k zadání e-mailové adresy a hesla. Pokud se instruktor pokusí přihlásit pomocí neúčet Microsoft, který nemá účet Microsoft propojený, instruktor dostane následující chybovou zprávu: 

![Chybová zpráva](./media/how-to-configure-student-usage/cant-find-account.png)

Chcete-li se zaregistrovat k účet Microsoft, instruktory by měly přejít na [http://signup.live.com](http://signup.live.com) .  


### <a name="using-a-github-account"></a>Použití účtu GitHub
Instruktoři můžou k registraci a přihlášení k prostředí učebny použít taky existující účet GitHubu. Pokud už instruktor má účet Microsoft propojený se svým účtem GitHubu, může se přihlásit a zadat heslo, jak je znázorněno v předchozí části. Pokud ještě neodkazují svůj účet GitHubu na účet Microsoft, měly by si vybrat **Možnosti přihlášení**:

![Odkaz Možnosti přihlášení](./media/how-to-configure-student-usage/signin-options.png)

Na stránce **Možnosti přihlášení** vyberte možnost **Přihlásit se pomocí GitHubu**.

![Přihlaste se pomocí odkazu na GitHub](./media/how-to-configure-student-usage/signin-github.png)

Nakonec se zobrazí výzva k vytvoření účet Microsoft, která je propojená s jejich účtem GitHubu. K tomu dochází automaticky, když instruktor vybere **Next (další**).  Instruktor se pak hned přihlásí a připojí k vývojovému prostředí učebny.


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí vytvářet a spravovat cvičení](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí, nastavení a publikování šablon](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel testovacího prostředí, Access učeben Labs](how-to-use-classroom-lab.md)
