---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Trelica | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trelica.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 577bdae2-dbab-4445-a07e-de0119b65d76
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17c68a95530f345d1ec0ed077681ec4cd6eb3775
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402446"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trelica"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Trelica

V tomto kurzu se dozvíte, jak integrovat Trelica s Azure Active Directory (Azure AD).

Pomocí této integrace můžete:

* Řízení ve službě Azure AD, která má přístup k Trelica.
* Umožněte uživatelům, aby se automaticky přihlásili k Trelica pomocí svých účtů Azure AD.
* Spravujte své účty v jednom centrálním umístění: Azure Portal.

Další informace o integraci aplikací SaaS (software jako služba) s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné Trelica s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Trelica podporuje jednotné přihlašování iniciované IDP.
* Trelica podporuje zřizování uživatelů za běhu.
* Po nakonfigurování Trelica můžete vynutili řízení relace. Tento ovládací prvek chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trelica-from-the-gallery"></a>Přidání Trelica z Galerie

Pokud chcete nakonfigurovat integraci Trelica do služby Azure AD, musíte přidat Trelica z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Vyberte možnost **podnikové aplikace**a pak vyberte **všechny aplikace**.
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

   ![Stránka nastavit jednotné přihlašování pomocí SAML s ikonou tužky pro základní konfiguraci SAML zvýrazněnou](common/edit-urls.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** zadejte následující hodnoty:

    1. Do pole **identifikátor** zadejte adresu URL **https://app.trelica.com** .

    1. Do pole **Adresa URL odpovědi** zadejte adresu URL, která má vzor `https://app.trelica.com/Id/Saml2/<CUSTOM_IDENTIFIER>/Acs` .

    > [!NOTE]
    > Hodnota **adresy URL odpovědi** není skutečná. Aktualizujte tuto hodnotu skutečnou adresou URL odpovědi. Pokud chcete získat tuto hodnotu, obraťte se na [tým podpory klienta Trelica](mailto:support@trelica.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** přejít do části **podpisový certifikát SAML** . Napravo od **adresy URL federačních metadat aplikace**můžete kliknutím na tlačítko Kopírovat zkopírovat adresu URL. Uložte adresu URL do vašeho počítače.

    ![Část podpisového certifikátu SAML s vybraným tlačítkem pro kopírování v poli Adresa URL federačních metadat aplikace](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele s názvem B. Simon ve Azure Portal.

1. V podokně Azure Portal vlevo vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel**.
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **název** zadejte **B. Simon**.
   1. Do pole **uživatelské jméno** zadejte **B. Simon@**_doménaspolečnosti_**.** _přípona_. Například, B.Simon@contoso.com.
   1. Zaškrtněte políčko **Zobrazit heslo** a potom zapište hodnotu zobrazenou v poli **heslo** .
   1. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon používat jednotné přihlašování pomocí Azure tím, že udělíte přístup k Trelica.

1. V Azure Portal vyberte možnost **podnikové aplikace**  >  **všechny aplikace**.
1. V seznamu aplikace vyberte **Trelica**.
1. Na stránce Přehled aplikace otevřete část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Oddíl Manage se zvýrazněnými uživateli a skupinami](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**. V dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny**.

   ![Okno Uživatelé a skupiny se zvýrazněnou možností přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** . Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele. Pak zvolte tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** vyberte **přiřadit**.

## <a name="configure-trelica-sso"></a>Konfigurace jednotného přihlašování Trelica

Pokud chcete nakonfigurovat jednotné přihlašování na straně **Trelica** , odešlete do [týmu podpory Trelica](mailto:support@trelica.com)zkopírované hodnoty **URL federačních metadat aplikace** . Nakonfigurují toto nastavení tak, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-a-trelica-test-user"></a>Vytvořit testovacího uživatele v Trelica

V této části vytvoříte uživatele s názvem B. Simon v Trelica.

Trelica podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části nemusíte nic dělat. Pokud uživatel ještě v Trelica neexistuje, vytvoří se po ověření nový.

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí portálu moje aplikace.

Když vyberete dlaždici Trelica na portálu moje aplikace, automaticky jste přihlášeni k Trelica, pro které jste nastavili jednotné přihlašování. Další informace o portálu moje aplikace najdete v tématu [Úvod do portálu moje aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Kurzy pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si Trelica s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit Trelica pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
