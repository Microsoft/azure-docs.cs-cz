---
title: Nastavit hesla aplikací na stránce zabezpečení informací (preview) – Azure Active Directory | Dokumentace Microsoftu
description: Nastavte automaticky generované hesla (hesla aplikací) pro použití s libovolnou aplikaci nezaložené na prohlížeči nebo jakékoli aplikaci, která nepodporuje dvoufaktorového ověřování ve vaší organizaci. Toto heslo aplikace je oddělená od normální heslo a můžete nastavit na stránce informace o zabezpečení.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55dfab0c60e77b86157a005db34c37917a5e08d2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60475087"
---
# <a name="manage-app-passwords-from-your-security-info-preview-page"></a>Správa hesel aplikací ze stránky zabezpečení informací (preview)
Některé aplikace, jako je například Outlook 2010, nepodporují dvoustupňové ověřování. Tato nedostatečná podpora znamená, že pokud dvoustupňové ověřování při použití ve vaší organizaci, aplikace nebude fungovat. Chcete-li tento problém vyřešit, můžete vytvořit automaticky vytvořené heslo pro použití s každou aplikaci nezaložené na prohlížeči, nezávisle na normální heslo.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Správce neumožňují používat hesla aplikací. Pokud nevidíte **hesla aplikací** jako možnost, nejsou k dispozici ve vaší organizaci.

Při používání hesel aplikací, je důležité pamatovat:

- Hesla aplikací jsou automaticky generované a pouze jednou zadali jednu aplikaci.

- Platí omezení 40 hesel na uživatele. Pokud se pokusíte vytvořit po tohoto limitu, budete vyzváni k odstranění stávajícího hesla před nebudou moct vytvořit nový.

- Používejte jedno heslo aplikace na zařízení, ne podle aplikací. Například vytvořte jedno heslo pro všechny aplikace na svém přenosném počítači a pak opět jedno heslo pro všechny aplikace na ploše.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové protokoly pro ověřování a je možné s dvoustupňovým ověřováním. Tato podpora znamená, že po zapnutí dvoustupňového ověřování, budete už nepotřebujete hesla aplikací pro klienty Office 2013. Další informace najdete v tématu [jak moderní ověřování funguje pro klientské aplikace Office 2013 a Office 2016](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) článku.

## <a name="create-new-app-passwords"></a>Vytvořit nová hesla aplikací
Pokud používáte dvoustupňové ověření pomocí svého pracovního nebo školního účtu a správce zapnul prostředí informace o zabezpečení, můžete vytvářet a odstraňovat pomocí hesla aplikace **bezpečnostní údaje** stránky.

>[!Note]
>Pokud správce není zapnuté prostředí informace o zabezpečení, je třeba dodržet následující pokyny a informace v [spravovat hesla aplikací pro dvoustupňové ověřování](multi-factor-authentication-end-user-app-passwords.md) oddílu.

### <a name="to-create-a-new-app-password"></a>Chcete-li vytvořit nové heslo aplikace
1. Přihlaste se ke svému pracovnímu nebo školnímu účtu a potom přejděte k vaší https://myprofile.microsoft.com/ stránky.

    ![Můj profil stránky zobrazující zvýrazněný odkazy informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. Vyberte **bezpečnostní údaje** v levém navigačním podokně nebo z odkazu v **bezpečnostní údaje** blokovat a pak vyberte **přidejte metodu** z **bezpečnostní údaje**  stránky.

    ![Stránka informace o zabezpečení s zvýrazněnou možností přidat – metoda](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na **přidejte metodu** stránce **heslo aplikace** z rozevíracího seznamu a pak vyberte **přidat**.

    ![Přidat metodu pole pomocí hesla aplikace vybrané](media/security-info/securityinfo-myprofile-addpassword.png)

4. Zadejte název aplikace, který vyžaduje heslo aplikace a pak vyberte **Další**.

    ![Stránku hesel aplikací, s názvem aplikace](media/security-info/securityinfo-myprofile-password-appname.png)

5. Zkopírujte text z **heslo** vložte heslo v oblasti heslo aplikace (v tomto příkladu Outlook 2010) a potom vyberte **provádí**.

    ![Stránku hesel aplikací, s názvem aplikace](media/security-info/securityinfo-myprofile-password-copytext.png)
    
    Přidá heslo a může úspěšně přihlásit do vaší aplikace do budoucna.

## <a name="delete-your-app-passwords"></a>Odstranění hesla aplikace
Pokud už nepotřebujete použít aplikaci, která vyžaduje heslo aplikace, můžete odstranit heslo přidružené aplikace. Odstranění hesla aplikace dojde k uvolnění jeden z míst heslo aplikace k dispozici pro použití v budoucnu.

>[!Important]
>Pokud omylem odstraníte heslo aplikace, neexistuje žádný způsob, jak ji vrátit. Budete muset vytvořit nové heslo aplikace a znovu ho zadejte do aplikace, proveďte kroky v [vytvořit nová hesla aplikací](#create-new-app-passwords) části tohoto článku.

### <a name="to-delete-an-app-password"></a>Chcete-li odstranit heslo aplikace.

1. Na **bezpečnostní údaje** stránky, vyberte **odstranit** odkaz **heslo aplikace** možnost pro konkrétní aplikaci.

    ![Odkaz na metodu heslo aplikace odstranit z bezpečnostní údaje](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Vyberte **Ano** ze seznamu potvrzení odstranění **heslo aplikace**. Po odstranění hesla aplikace, odebere se ze svých bezpečnostních údajů a dané zařízení zmizí z **bezpečnostní údaje** stránky.

## <a name="for-more-information"></a>Další informace
- Další informace o **bezpečnostní údaje** stránky a jak ji nastavit, najdete v článku [přehled informací o zabezpečení](user-help-security-info-overview.md)
