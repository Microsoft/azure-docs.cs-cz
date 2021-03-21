---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Trelica | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trelica.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.openlocfilehash: a674f5f653ad420ab8f28ff73c6b86f9c18b154e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92517748"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Trelica

V tomto kurzu se dozvíte, jak integrovat Trelica s Azure Active Directory (Azure AD).

Pomocí této integrace můžete:

* Řízení ve službě Azure AD, která má přístup k Trelica.
* Umožněte uživatelům, aby se automaticky přihlásili k Trelica pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Trelica s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Trelica podporuje jednotné přihlašování iniciované IDP.
* Trelica podporuje zřizování uživatelů za běhu.
* Po nakonfigurování Trelica můžete vynutili řízení relace. Tento ovládací prvek chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Přidání Trelica z Galerie

Pokud chcete nakonfigurovat integraci Trelica do služby Azure AD, musíte přidat Trelica z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Trelica** .
1. Ve výsledcích hledání vyberte **Trelica** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trelica"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Trelica

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Trelica pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, musíte vytvořit propojený vztah mezi uživatelem služby Azure AD a souvisejícím uživatelem v Trelica.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Trelica, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** , aby vaši uživatelé mohli používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** pro testování jednotného přihlašování Azure AD pomocí B. Simon.
    1. Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD, **[přiřaďte testovacímu uživateli Azure AD](#assign-the-azure-ad-test-user)** .
1. Nakonfigurujte jednotné přihlašování **[TRELICA SSO](#configure-trelica-sso)** pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Trelica testovacího uživatele](#create-a-trelica-test-user)** , který bude mít protějšek B. Simon v Trelica. Tato strana je propojená se zastoupením uživatele v Azure AD.
1. **[Otestujte jednotné přihlašování](#test-sso)** a ověřte, jestli konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Trelica** přejít na část **Správa** . Vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu tužky pro **základní konfiguraci SAML** a upravte nastavení.

   ![Stránka nastavit jeden Sign-On se stránkou SAML s ikonou tužky pro základní konfiguraci SAML zvýrazněnou](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte následující hodnoty:

    1. Do pole **identifikátor** zadejte adresu URL **https://app.trelica.com** .

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL, která má vzor `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > Hodnota adresy URL odpovědi není reálné číslo. Aktualizujte tuto hodnotu pomocí skutečné adresy URL odpovědi (označované také jako služba ACS).
    > Můžete to zjistit tak, že se přihlásíte k Trelica a na [stránce Konfigurace zprostředkovatelů identity SAML](https://app.trelica.com/Admin/Profile/SAML) (účet správce > > SAML). Klikněte na tlačítko Kopírovat vedle **adresy URL služby assertion Consumer Service (ACS)** , která bude vložena do schránky, připravena pro vložení do textového pole **odpovědi URL** ve službě Azure AD.
    > Pokud máte nějaké dotazy, přečtěte si [dokumentaci k nápovědě pro Trelica](https://docs.trelica.com/admin/saml/azure-ad) nebo se obraťte na [tým podpory Trelica klientů](mailto:support@trelica.com) .

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** kliknutím na tlačítko Kopírovat zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Část podpisového certifikátu SAML s vybraným tlačítkem pro kopírování v poli Adresa URL federačních metadat aplikace](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V podokně Azure Portal vlevo vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte **B. Simon**.
   1. Do pole **uživatelské jméno** zadejte **B.Simon@**_doménaspolečnosti_**.** _přípona_. Například, B.Simon@contoso.com.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Trelica.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **Trelica**.
1. Na stránce Přehled aplikace otevřete část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Oddíl Manage se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte možnost **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

   ![Okno Uživatelé a skupiny se zvýrazněnou možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-trelica-sso"></a>Konfigurace jednotného přihlašování Trelica

Pokud chcete nakonfigurovat jednotné přihlašování na straně **Trelica** , použijte [stránku konfigurace zprostředkovatelů identity SAML](https://app.trelica.com/Admin/Profile/SAML) (účet správce > > SAML). Klikněte na tlačítko **Nový** . Jako název zadejte **Azure AD** a jako typ metadat vyberte **metadata z adresy URL** . Do pole **Adresa URL metadat** v Trelica vložte **adresu URL federačních metadat aplikace** , kterou jste přijali ze služby Azure AD.

Pokud máte nějaké dotazy, přečtěte si [dokumentaci k nápovědě pro Trelica](https://docs.trelica.com/admin/saml/azure-ad) nebo se obraťte na [tým podpory Trelica klientů](mailto:support@trelica.com) .

### <a name="create-a-trelica-test-user"></a>Vytvořit testovacího uživatele v Trelica

Trelica podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části nemusíte nic dělat. Pokud uživatel ještě v Trelica neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když vyberete dlaždici Trelica na portálu moje aplikace, automaticky jste přihlášeni k Trelica, pro které jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu [Úvod do portálu moje aplikace](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [Kurzy pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Trelica s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Jak chránit Trelica pomocí pokročilých viditelností a ovládacích prvků](/cloud-app-security/proxy-intro-aad)