---
title: Přidání uživatele jako tvůrce testovacího prostředí ve službě Azure Lab Services
description: Tento článek ukazuje, jak přidat uživatele do role Tvůrce testovacího prostředí pro účet testovacího prostředí ve službě Azure Lab Services. Tvůrci testovacího prostředí můžete vytvořit testovací prostředí v rámci tohoto účtu testovacího prostředí.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444768"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Přidání tvůrců testovacího prostředí k testovacímu účtu ve službě Azure Lab Services
Tento článek ukazuje, jak přidat uživatele jako tvůrce testovacího prostředí do testovacího prostředí účtu ve službě Azure Lab Services. Tato použití pak můžete vytvořit učebny testovacích prostředí v účtu testovacího prostředí. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Přidání uživatelského účtu Microsoft u role Lab Creator
Pokud chcete v účtu testovacího prostředí nastavit testovací prostředí v učebně, musí být uživatel v účtu testovacího prostředí členem role **Autor testovacího prostředí**. Do této role se automaticky přidá účet, který jste použili k vytvoření účtu testovacího prostředí. Pokud máte v úmyslu použít k vytvoření testovacího prostředí v učebně stejný uživatelský účet, můžete tento krok přeskočit. Pokud chcete k vytvoření testovacího prostředí v učebně použít jiný uživatelský účet, postupujte takto: 

Chcete-li pedagogům poskytnout oprávnění k vytváření testovacích prostředí pro jejich třídy, přidejte je do role **Tvůrce testovacího prostředí:**

1. Na stránce **Účet testovacího prostředí** vyberte **ovládací prvek přístupu (IAM)** a na panelu nástrojů klepněte na **+ Přidat přiřazení role.** 

    ![Řízení přístupu -> tlačítko Přidat přiřazení role](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Na stránce **Přidat přiřazení role** vyberte Tvůrce **lab** pro **roli**, vyberte uživatele, kterého chcete přidat do role Tvůrci testovacího prostředí, a vyberte **Uložit**. 

    ![Přidat tvůrce testovacího prostředí](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Pokud přidáváte uživatele účtu, který není uživatelem Microsoft jako tvůrce testovacího prostředí, přečtěte si část [Přidání uživatele účtu jiného než Microsoft jako tvůrce testovacího prostředí.](#add-a-non-microsoft-account-user-as-a-lab-creator) 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Přidání uživatele účtu, který není uživatelem Microsoft, jako tvůrce testovacího prostředí
Chcete-li přidat uživatele jako tvůrce testovacího prostředí, použijte jeho e-mailové účty. Mohou být použity následující typy e-mailových účtů:

- E-mailový účet poskytovaný univerzitním Office 365 Azure Active Directory (AAD). 
- E-mailový účet `@outlook.com`Microsoft, například , `@hotmail.com`, `@msn.com`nebo `@live.com`.
- E-mailový účet jiných společností než Microsoft, například účet poskytovaný společností Yahoo nebo Google. Tyto typy účtů však musí být propojeny s účtem Microsoft.
- Účet GitHub. Tento účet musí být propojen s účtem Microsoft.

### <a name="using-a-non-microsoft-email-account"></a>Používání e-mailového účtu jiného než Microsoft
Tvůrci a instruktoři testovacího prostředí můžou k registraci a přihlášení do učebny používat e-mailové účty jiných společností než Microsoft.  Přihlášení k portálu Služeb testovacího prostředí však vyžaduje, aby instruktoři nejprve vytvořili účet Microsoft, který je propojený s jejich e-mailovou adresou jiného výrobce než Microsoft.

Mnoho instruktorů už může mít účet Microsoft propojený s e-mailovými adresami jiných společností než Microsoft. Instruktoři už například mají účet Microsoft, pokud svou e-mailovou adresu použili s dalšími produkty nebo službami microsoftu, jako je Office, Skype, OneDrive nebo Windows.  

Když se instruktoři přihlásí k portálu služeb Lab, zobrazí se jim výzva k zadání e-mailové adresy a hesla. Pokud se instruktor pokusí přihlásit pomocí účtu jiného, než je Microsoft, který nemá propojený účet Microsoft, zobrazí se následující chybová zpráva: 

![Chybová zpráva](../media/how-to-configure-student-usage/cant-find-account.png)

Pokud si chcete zaregistrovat účet Microsoft, [http://signup.live.com](http://signup.live.com)měli by instruktoři přejít na .  


### <a name="using-a-github-account"></a>Používání účtu GitHub
Instruktoři můžou taky použít existující účet GitHub k registraci a přihlášení do učebny. Pokud má instruktor již účet Microsoft propojený se svým účtem GitHub, může se přihlásit a zadat své heslo, jak je znázorněno v předchozí části. Pokud ještě svůj účet GitHub nepropojili s účtem Microsoft, měli by vybrat **možnosti přihlášení**:

![Odkaz na možnosti přihlášení](../media/how-to-configure-student-usage/signin-options.png)

Na stránce **Možnosti přihlášení** vyberte **Přihlásit se pomocí GitHubu**.

![Přihlášení pomocí odkazu GitHub](../media/how-to-configure-student-usage/signin-github.png)

Nakonec se zobrazí výzva k vytvoření účtu Microsoft, který je propojený s jejich účtem GitHub. To se stane automaticky, když instruktor vybere **Další**.  Instruktor je pak okamžitě přihlášen a připojen k učebně laboratoři.


## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Jako vlastník testovacího prostředí vytvářejte a spravujte testovací prostředí](how-to-manage-classroom-labs.md)
- [Jako vlastník testovacího prostředí nastavte a publikujte šablony](how-to-create-manage-template.md)
- [Jako vlastník testovacího prostředí konfigurace a řízení využití testovacího prostředí](how-to-configure-student-usage.md)
- [Jako uživatel laboratoře, přístup k učebně labs](how-to-use-classroom-lab.md)
