---
title: Nastavit hesla aplikací pomocí informací o zabezpečení – Azure Active Directory | Dokumentace Microsoftu
description: Nastavit automaticky generované hesla (hesla aplikací) pro použití s každou aplikaci nezaložené na prohlížeči, nezávislá na normální heslo pomocí bezpečnostních údajů.
services: active-directory
author: eross-msft
manager: mtillman
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.component: user-help
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: lizross
ms.openlocfilehash: 56d207dc47b9e055782568f1c4a4cedc4d19a288
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348305"
---
# <a name="manage-app-passwords-using-security-info-preview"></a>Správa hesel aplikací pomocí informací o zabezpečení (preview)

[!INCLUDE[preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

Některé aplikace nezaložené na prohlížeči, jako je například Outlook 2010, nepodporuje dvoustupňové ověřování. Tato nedostatečná podpora znamená, že pokud používáte dvoustupňové ověřování, aplikace nebude fungovat. Chcete-li tento problém vyřešit, můžete vytvořit automaticky vytvořené heslo pro použití s každou aplikaci nezaložené na prohlížeči, nezávisle na normální heslo.

Při používání hesel aplikací, je důležité pamatovat:

- Hesla aplikací jsou automaticky generované a pouze jednou zadali jednu aplikaci.

- Platí omezení 40 hesel na uživatele. Pokud se pokusíte vytvořit po tohoto limitu, budete vyzváni k odstranění stávajícího hesla před nebudou moct vytvořit nový.

- Používejte jedno heslo aplikace na zařízení, ne podle aplikací. Například vytvořte jedno heslo pro všechny aplikace na svém přenosném počítači a pak opět jedno heslo pro všechny aplikace na ploše.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové protokoly pro ověřování a je možné s dvoustupňovým ověřováním. Tato podpora znamená, že po zapnutí dvoustupňového ověřování, budete už nepotřebujete hesla aplikací pro klienty Office 2013. Další informace najdete v tématu [jak moderní ověřování funguje pro klientské aplikace Office 2013 a Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) článku.

## <a name="create-and-delete-app-passwords-using-security-info"></a>Vytvoření a odstranění hesla aplikace pomocí bezpečnostních údajů

Pokud používáte dvoustupňové ověřování přes svůj pracovní nebo školní účet a správce zapnul prostředí informace o zabezpečení, můžete vytvořit a odstranit hesel aplikací na portálu Moje aplikace.

Pokud správce není zapnuté prostředí informace o zabezpečení, je třeba dodržet následující pokyny a informace v [spravovat hesla aplikací pro dvoustupňové ověřování](multi-factor-authentication-end-user-app-passwords.md) oddílu.

### <a name="to-create-app-passwords-using-the-my-apps-portal"></a>Chcete-li vytvořit hesla aplikací na portálu Moje aplikace

1. Přihlaste se ke svému pracovnímu nebo školnímu účtu.

2. Přejděte na myapps.microsoft.com, vyberte své jméno v pravém horním rohu stránky a pak vyberte **profilu**.

3. V **spravovat účet** vyberte **upravit bezpečnostní údaje**.

    ![Profil obrazovky se zvýrazněným odkazem informace o zabezpečení úpravy](media/security-info/security-info-profile.png)

4. V **bezpečnost vašeho účtu** obrazovky, vyberte **přidejte bezpečnostní údaje**.

    ![Informace o okně zabezpečení s existující, upravovat informace](media/security-info/security-info-edit-add-info.png)

5. V **přidejte bezpečnostní údaje** obrazovky, vyberte **heslo aplikace**.

6. V **vytvořit heslo aplikace** obrazovky, zadejte název hesla aplikace a pak vyberte **Další**.

    ![Obrazovky, ve kterém zadáte název hesla aplikace](media/security-info/security-info-name-app-password.png)

7. Vyberte **kopírování** zkopírovat heslo do schránky, a potom vyberte **Další**.

    ![Obrazovky pomocí hesla aplikace pro kopírování](media/security-info/security-info-create-app-password.png)
    
8. Ujistěte se, že se zobrazí na heslo aplikace **bezpečnost vašeho účtu** obrazovky.

    ![Zachovat zabezpečené obrazovky pomocí hesla aplikace](media/security-info/security-info-keep-secure-app-password.png)

### <a name="to-delete-app-passwords-using-the-my-apps-portal"></a>Odstranění hesla aplikace na portálu Moje aplikace

1. Na **bezpečnost vašeho účtu** obrazovky, vyberte **X** vedle heslo aplikace odstranit.

    ![Zachovat zabezpečené obrazovky, odstranit heslo aplikace](media/security-info/security-info-keep-secure-delete-app-password.png)

2. V **heslo aplikace odstranit** obrazovky, vyberte **odstranit**.

    ![Odstraňte obrazovku pro zadání hesla aplikace](media/security-info/security-info-keep-secure-delete-app-password2.png)

## <a name="next-steps"></a>Další postup

- Pokud je potřeba aktualizovat svoje bezpečnostní údaje, postupujte podle pokynů [spravovat svoje bezpečnostní údaje](security-info-manage-settings.md) článku.

- Další obecné informace o informace o zabezpečení a co můžete dělat, najdete v části [přehled informací o zabezpečení](user-help-security-info-overview.md) 