---
title: 'Kurz: Azure Active Directory integrace s Pingboard | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pingboard.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: f96f7eb358907bc27303735f4d8a111efeb6a3dc
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996649"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Kurz: Azure Active Directory integrace s Pingboard

V tomto kurzu se dozvíte, jak integrovat Pingboard s Azure Active Directory (Azure AD).
Integrace Pingboard s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Pingboard.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Pingboard (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Pingboard potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Pingboardm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Pingboard podporuje **jednotné** přihlašování (SSO) a **IDP** .

* Pingboard podporuje [automatizované zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial) 

## <a name="adding-pingboard-from-the-gallery"></a>Přidání Pingboard z Galerie

Pokud chcete nakonfigurovat integraci Pingboard do služby Azure AD, musíte přidat Pingboard z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Pingboard z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Pingboard**, vyberte **Pingboard** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Pingboard v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Pingboard na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Pingboard.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Pingboard, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Pingboard](#configure-pingboard-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Pingboard Test User](#create-pingboard-test-user)** – pro Britta Simon v Pingboard, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Pingboard, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Pingboard** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky znázorňující konfiguraci "základní S A M L" se zvýrazněnými textovými poli "identifikátor" a "Reply U R L" a vybraným tlačítkem Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL: `http://app.pingboard.com/sp`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<entity-id>.pingboard.com/auth/saml/consume`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Informace o jednotném přihlašování v doméně Pingboard a adresách URL](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty pomocí skutečné adresy URL odpovědi a přihlašovací adresy URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Pingboard](https://support.pingboard.com/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **Nastavení Pingboard** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-pingboard-single-sign-on"></a>Nakonfigurovat Pingboard jednu Sign-On

1. Pokud chcete nakonfigurovat jednotné přihlašování na straně Pingboard, otevřete nové okno prohlížeče a přihlaste se ke svému účtu Pingboard. Abyste mohli nastavit jednotné přihlašování, musíte být správcem Pingboard.

2. V horní nabídce vyberte **aplikace > integrace**

    ![Konfigurace jednoho Sign-On](./media/pingboard-tutorial/Pingboard_integration.png)

3. Na stránce **integrace** Najděte dlaždici **"Azure Active Directory"** a klikněte na ni.

    ![Pingboard Integration Single Sign-On](./media/pingboard-tutorial/Pingboard_aad.png)

4. V modálním příkladu klikněte na **Konfigurovat** .

    ![Tlačítko Konfigurace Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

5. Na následující stránce si všimnete, že je povolená Integrace Azure SSO. Otevřete stažený soubor XML s metadaty v poznámkovém bloku a vložte obsah do **metadat IDP**.

    ![Obrazovka konfigurace jednotného přihlašování Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

6. Soubor se ověří a pokud je vše správné, bude teď povolené jednotné přihlašování.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Pingboard.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Pingboard**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Pingboard**.

    ![Odkaz Pingboard v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-pingboard-test-user"></a>Vytvořit testovacího uživatele Pingboard

Cílem této části je vytvořit uživatele s názvem Britta Simon v Pingboard. Pingboard podporuje automatické zřizování uživatelů, které je ve výchozím nastavení povolené. Další podrobnosti najdete v [tématu](pingboard-provisioning-tutorial.md) konfigurace automatického zřizování uživatelů.

**Pokud potřebujete ručně vytvořit uživatele, proveďte následující kroky:**

1. Přihlaste se k webu Pingboard společnosti jako správce.

2. Klikněte na tlačítko **Přidat zaměstnance** na stránce **adresáře** .

    ![Přidat zaměstnance](./media/pingboard-tutorial/create_testuser_add.png)

3. Na stránce **Přidat zaměstnance** proveďte následující kroky:

    ![Pozvat lidi](./media/pingboard-tutorial/create_testuser_name.png)

    a. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je **Britta Simon**.

    b. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele **brittasimon@contoso.com** .

    c. Do textového pole **název úlohy** zadejte název úlohy Britta Simon.

    d. V rozevíracím seznamu **umístění** vyberte umístění Britta Simon.

    e. Klikněte na **Přidat**.

4. Zobrazí se potvrzovací obrazovka s potvrzením přidání uživatele.

    ![potvrdit](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Pingboard, měli byste se automaticky přihlásit k Pingboard, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurace zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/saas-apps/pingboard-provisioning-tutorial)
