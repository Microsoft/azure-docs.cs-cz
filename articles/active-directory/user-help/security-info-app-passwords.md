---
title: Vytváření hesel aplikací ze stránky Bezpečnostní údaje (preview) – Azure AD
description: Vytvářejte automaticky generovaná hesla (hesla aplikací), která se použijí s jakoukoli aplikací, která není v prohlížeči, nebo s jakoukoli aplikací, která nepodporuje dvoufaktorové ověření ve vaší organizaci. Toto heslo aplikace je oddělené od normálního hesla a lze jej nastavit na stránce Bezpečnostní údaje.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 02/13/2018
ms.author: curtand
ms.openlocfilehash: 787fa67ee77997fd1f9967db3abdbfc83d4ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064066"
---
# <a name="create-app-passwords-from-the-security-info-preview-page"></a>Vytvoření hesel aplikací ze stránky Bezpečnostní údaje (náhled)

Některé aplikace, například Outlook 2010, nepodporují dvoustupňové ověření. Tento nedostatek podpory znamená, že pokud ve vaší organizaci používáte dvoustupňové ověření, aplikace nebude fungovat. Chcete-li tento problém vyřešit, můžete vytvořit automaticky generované heslo pro použití s každou aplikací, která není prohlížečem, odděleně od normálního hesla.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-info.md)]

>[!Important]
>Správce vám nemusí povolit používání hesel aplikací. Pokud **hesla aplikací** jako možnost nevidíte, nejsou ve vaší organizaci dostupná.

Při používání hesel aplikací je důležité mít na paměti:

- Hesla aplikací se generují automaticky a měla by být vytvořena a zadána jednou pro aplikaci.

- Je zde limit 40 hesel na uživatele. Pokud se pokusíte vytvořit po tomto limitu, budete vyzváni k odstranění existujícího hesla před povolením k vytvoření nového.

    >[!Note]
    >Klienti Office 2013 (včetně Outlooku) podporují nové ověřovací protokoly a můžou je používat s dvoustupňovým ověřováním. Tato podpora znamená, že po zapnutí dvoustupňového ověření už nebudete pro klienty Office 2013 potřebovat hesla aplikací. Další informace najdete v článku [Jak funguje moderní ověřování pro klientské aplikace Office 2013 a Office 2016.](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517)

## <a name="create-new-app-passwords"></a>Vytváření nových hesel aplikací

Pokud používáte dvoustupňové ověření s pracovním nebo školním účtem a správce zapnul prostředí bezpečnostních údajů, můžete vytvořit a odstranit hesla aplikací pomocí stránky **Bezpečnostní údaje.**

>[!Note]
>Pokud správce nezapnul prostředí s bezpečnostními údaji, musíte postupovat podle pokynů a informací v části [Správa hesel aplikací pro dvoustupňové ověření.](multi-factor-authentication-end-user-app-passwords.md)

### <a name="to-create-a-new-app-password"></a>Vytvoření nového hesla aplikace

1. Přihlaste se ke svému pracovnímu https://myprofile.microsoft.com/ nebo školnímu účtu a přejděte na stránku.

    ![Stránka Můj profil se zvýrazněnými odkazy Na informace o zabezpečení](media/security-info/securityinfo-myprofile-page.png)

2. V levém navigačním podokně nebo z odkazu v bloku **Informace o zabezpečení** vyberte Bezpečnostní **údaje** a na stránce Informace **o zabezpečení** vyberte **Přidat metodu.**

    ![Stránka S informacemi o zabezpečení se zvýrazněnou možností Přidat metodu](media/security-info/securityinfo-myprofile-addmethod-page.png)

3. Na stránce **Přidat metodu** vyberte v rozevíracím seznamu **heslo aplikace** a pak vyberte **Přidat**.

    ![Pole Přidat metodu s vybraným heslem aplikace](media/security-info/securityinfo-myprofile-addpassword.png)

4. Zadejte název aplikace, která vyžaduje heslo aplikace, a pak vyberte **Další**.

    ![Stránka s heslem aplikace s názvem aplikace](media/security-info/securityinfo-myprofile-password-appname.png)

5. Zkopírujte text z pole **Heslo,** vložte heslo do oblasti hesla aplikace (v tomto příkladu Outlook 2010) a pak vyberte **Hotovo**.

    ![Stránka s heslem aplikace s názvem aplikace](media/security-info/securityinfo-myprofile-password-copytext.png)

    Heslo se přidá a můžete se úspěšně přihlásit do aplikace do budoucna.

## <a name="delete-your-app-passwords"></a>Odstranění hesel aplikace

Pokud už nepotřebujete používat aplikaci, která vyžaduje heslo aplikace, můžete odstranit přidružené heslo aplikace. Odstraněním hesla aplikace se uvolní jedno z dostupných míst pro heslo aplikace pro použití v budoucnu.

>[!Important]
>Pokud heslo aplikace smažete omylem, neexistuje žádný způsob, jak ho vrátit zpět. Budete muset vytvořit nové heslo aplikace a znovu ho zadat do aplikace podle kroků v části [Vytvořit nová hesla aplikací](#create-new-app-passwords) v tomto článku.

### <a name="to-delete-an-app-password"></a>Odstranění hesla aplikace

1. Na stránce **Bezpečnostní informace** vyberte odkaz **Odstranit** vedle možnosti **Heslo aplikace** pro konkrétní aplikaci.

    ![Odkaz na odstranění metody hesla aplikace z bezpečnostních údajů](media/security-info/securityinfo-myprofile-password-appdelete.png)

2. Chcete-li odstranit **heslo aplikace**, vyberte v potvrzovacím poli možnost **Ano** . Po odstranění hesla aplikace se odebere z vašich bezpečnostních údajů a zmizí ze stránky **Bezpečnostní údaje.**

## <a name="for-more-information"></a>Další informace

- Další informace o stránce **Informace o zabezpečení** a o tom, jak ji nastavit, najdete v [tématu Přehled bezpečnostních údajů](user-help-security-info-overview.md)
