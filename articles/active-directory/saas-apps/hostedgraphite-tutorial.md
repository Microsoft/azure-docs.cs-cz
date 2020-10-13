---
title: 'Kurz: Azure Active Directory integrace s hostujícím grafitem | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a hostujícím grafitem.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: 137a077ac73b5dd5ca9c68c6be2b30beb8e92ce0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91826234"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Kurz: Azure Active Directory integrace s hostovaným grafitem

V tomto kurzu se dozvíte, jak integrovat program pro hostování pomocí Azure Active Directory (Azure AD).
Integrace hostovaného grafitu se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k hostovanému grafitu.
* Uživatelům můžete povolit, aby se automaticky přihlásili k hostovanému grafitu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD pomocí hostovaného grafitu potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Hostované předplatné s povoleným jednorázovým přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Hostovaný grafit podporuje **aktualizace SP a IDP, které** iniciovaly jednotné přihlašování.
* Hostovaný grafit podporuje **jenom včas** zřizování uživatelů.

## <a name="adding-hosted-graphite-from-the-gallery"></a>Přidání hostovaného grafitu z Galerie

Pokud chcete nakonfigurovat integraci hostovaného grafitu do služby Azure AD, musíte do seznamu spravovaných aplikací SaaS přidat hostovaný grafit z galerie.

**Chcete-li přidat hostovaný grafit z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **hostující grafit**, vyberte **hostovaný grafit** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Hostující grafit v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí hostovaného grafitu na základě testovacího uživatele s názvem **Britta Simon**.
Aby se jednotné přihlašování fungovalo, je potřeba zřídit vztah odkazu mezi uživatelem služby Azure AD a souvisejícím uživatelem v hostovaném grafitu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí hostovaného grafitu, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Konfigurace hostovaného jednotného přihlašování](#configure-hosted-graphite-single-sign-on)** – pro konfiguraci nastavení jediného Sign-On na straně aplikace
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření hostovaného programu pro testování pomocí nástroje grafitového uživatele](#create-hosted-graphite-test-user)** – Pokud chcete mít protějšek Britta Simon v hostovaném grafitu, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí hostovaného grafitu, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **hostované** integrace aplikace grafit vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Počítejte s tím, že se nejedná o reálné hodnoty. Tyto hodnoty musíte aktualizovat skutečným identifikátorem, adresou URL odpovědi a adresou URL pro přihlášení. Chcete-li získat tyto hodnoty, můžete přejít na na straně aplikace >instalaci SAML nebo se obraťte na [hostovaného týmu podpory pro grafit](mailto:help@hostedgraphite.com).

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

7. V části **nastavení hostovaného grafitu** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-hosted-graphite-single-sign-on"></a>Konfigurovat hostovaný grafit Single Sign-On

1. Přihlaste se k hostovanému klientovi grafitu jako správce.

2. Na bočním panelu přejděte na **stránku nastavení SAML** (**přístup-> nastavení SAML**).

    ![Snímek obrazovky s vybraným nastavením SAML zobrazuje nabídku přístup.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Potvrďte, že tyto adresy URL odpovídají vaší konfiguraci v **základní části Konfigurace SAML** v Azure Portal.

    ![Snímek obrazovky ukazuje základní konfiguraci SAML.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. Do textových polí  **ID objektu entity nebo vystavitele** a **přihlašovací adresa URL pro přihlášení SSO** vložte hodnotu **identifikátoru služby Azure AD** a **adresu URL pro přihlášení** , kterou jste zkopírovali z Azure Portal.

    ![Snímek obrazovky zobrazuje položky zprostředkovatele identity.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Jako **výchozí roli uživatele**vyberte jen **pro čtení** .

    ![Snímek obrazovky zobrazuje výchozí uživatelskou roli, která je jen pro čtení.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Otevřete v programu Poznámkový blok certifikát s kódováním Base-64 stažený z Azure Portal, zkopírujte jeho obsah do schránky a vložte ho do textového pole **certifikát X. 509** .

    ![Snímek obrazovky ukazuje certifikát X. 509.](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Klikněte na tlačítko **Uložit** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte **brittasimon \@ yourcompanydomain. extension.**  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k hostovanému grafitu.

1. V Azure Portal vyberte možnost **podnikové aplikace**, vyberte možnost **všechny aplikace**a pak vyberte možnost **hostovaný grafit**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte možnost **hostovaný grafit**.

    ![Hostované propojení grafitu v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-hosted-graphite-test-user"></a>Vytvořit hostovaného uživatele pro grafitové testování

V této části se v hostovaném grafitu vytvoří uživatel s názvem Britta Simon. Hostovaný grafit podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel v hostovaném grafitu ještě neexistuje, vytvoří se po ověření nový.

> [!NOTE]
> Pokud potřebujete ručně vytvořit uživatele, musíte se obrátit na hostovaného týmu podpory grafitu prostřednictvím <mailto:help@hostedgraphite.com> .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici hostovaného grafitu na přístupovém panelu byste měli být automaticky přihlášeni k hostovanému grafitu, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

