---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s InVision | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a InVision.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02487206-30b0-4b1d-ae99-573c3d2ef9b0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/09/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c2427fff37158745e416f4b2f0641697ad62ea9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81682664"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s InVision

V tomto kurzu se dozvíte, jak integrovat InVision s Azure Active Directory (Azure AD). Když integrujete InVision s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k InVision.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k InVision svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Požadavky

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* InVision odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* InVision podporuje jednotné přihlašování (SSO) **a IDP** .
* Po nakonfigurování InVision můžete vynutili řízení relace, které chrání exfiltrace a infiltraci citlivých dat vaší organizace v reálném čase. Řízení relace se rozšiřuje z podmíněného přístupu. [Přečtěte si, jak vynutili řízení relace pomocí Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-invision-from-the-gallery"></a>Přidání InVision z Galerie

Pokud chcete nakonfigurovat integraci InVision do služby Azure AD, musíte přidat InVision z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Invision** .
1. Na panelu výsledků vyberte **Invision** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-invision"></a>Konfigurace a testování jednotného přihlašování Azure AD pro InVision

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí InVision pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v InVision.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí InVision, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    * **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    * **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte INVISION SSO](#configure-invision-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    * **[Vytvořte Invision Test User](#create-invision-test-user)** -to, abyste měli protějšek B. Simon v Invision, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Invision** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<SUBDOMAIN>.invisionapp.com`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Invision](mailto:support@invisionapp.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Invision** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension. Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k InVision.

1. V Azure Portal vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Invision**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele**a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-invision-sso"></a>Konfigurace jednotného přihlašování InVision

1. V jiném okně webového prohlížeče se přihlaste k webu InVision jako správce.

1. Klikněte na **tým** a vyberte **Nastavení**.

    ![Konfigurace InVision](./media/invision-tutorial/config1.png)

1. Posuňte se dolů k **jednotnému přihlašování** a pak klikněte na **změnit**.

    ![Konfigurace InVision](./media/invision-tutorial/config3.png)

1. Na stránce **jednotného přihlašování** proveďte následující kroky:

    ![Konfigurace InVision](./media/invision-tutorial/config4.png)

    a. Změna **vyžaduje jednotné přihlašování pro každý člen < název účtu >** na **zapnuto**.

    b. Do textového pole **název** zadejte například název jako `azureadsso`.

    c. Do textového pole **Adresa URL pro přihlášení** zadejte hodnotu přihlašovací adresa URL.

    d. Do textového pole **Adresa URL pro odhlášení vložte hodnotu URL odhlašovacího** **protokolu** , kterou jste zkopírovali z Azure Portal.

    e. V textovém poli **certifikátu SAML** Otevřete stažený **certifikát (Base64)** do programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole certifikátu SAML.

    f. Do textového pole **formát ID názvu** zadejte `Unspecified` **formát ID**.

    g. Z rozevíracího seznamu pro **algoritmus hash**vyberte **SHA-256** .

    h. Zadejte vhodný název **popisku tlačítka jednotného přihlašování**.

    i. Zajistěte, aby **se povolilo zřizování za běhu** .

    j. Klikněte na **Aktualizovat**.

### <a name="create-invision-test-user"></a>Vytvořit testovacího uživatele InVision

1. V jiném okně webového prohlížeče se přihlaste k webu InVision jako správce.

1. Klikněte na **tým** a vyberte **osoby**.

    ![Konfigurace InVision](./media/invision-tutorial/config2.png)

1. Kliknutím na **ikonu +** přidejte nového uživatele.

    ![Konfigurace InVision](./media/invision-tutorial/user1.png)

1. Zadejte e-mailovou adresu uživatele a klikněte na tlačítko **Další**.

    ![Konfigurace InVision](./media/invision-tutorial/user2.png)

1. Po ověření e-mailové adresy a kliknutí na **pozvat**.

    ![Konfigurace InVision](./media/invision-tutorial/user3.png)

## <a name="test-sso"></a>Test SSO

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici InVision, měli byste se automaticky přihlásit k InVision, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály a zdroje informací

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Vyzkoušejte si InVision s Azure AD](https://aad.portal.azure.com/)

- [Co je řízení relace v Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chránit InVision pomocí pokročilých viditelností a ovládacích prvků](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)