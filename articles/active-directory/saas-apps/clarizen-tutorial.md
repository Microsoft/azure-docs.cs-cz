---
title: 'Kurz: Azure Active Directory integrace s Clarizen | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Clarizen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: 4e312c70e8d1630c1e8d1bb9a893b338383c7966
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88520647"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Kurz: Azure Active Directory integrace s Clarizen

V tomto kurzu se dozvíte, jak integrovat Clarizen s Azure Active Directory (Azure AD).
Integrace Clarizen s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Clarizen.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Clarizen (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Clarizen potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Předplatné s povoleným Clarizenm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Clarizen podporuje jednotné přihlašování **IDP** .

## <a name="adding-clarizen-from-the-gallery"></a>Přidání Clarizen z Galerie

Pokud chcete nakonfigurovat integraci Clarizen do služby Azure AD, musíte přidat Clarizen z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Clarizen z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Clarizen**, vyberte **Clarizen** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Clarizen v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Clarizen na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Clarizen.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Clarizen, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Clarizen](#configure-clarizen-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Clarizen Test User](#create-clarizen-test-user)** – pro Britta Simon v Clarizen, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Clarizen, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Clarizen** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Clarizen a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte hodnotu: `Clarizen`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Nejedná se o reálné hodnoty. Musíte použít skutečný identifikátor a adresu URL odpovědi. Tady doporučujeme, abyste jako identifikátor použili jedinečnou hodnotu řetězce. Pokud chcete získat skutečné hodnoty, obraťte se na [tým podpory Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Clarizen** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-clarizen-single-sign-on"></a>Nakonfigurovat Clarizen jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu Clarizen společnosti jako správce.

1. Klikněte na své uživatelské jméno a pak klikněte na **Nastavení**.

    ![V části uživatelské jméno klikněte na nastavení.](./media/clarizen-tutorial/tutorial_clarizen_001.png "Nastavení")

1. Klikněte na kartu **globální nastavení** . Pak klikněte na **Upravit**vedle **federovaného ověřování**.

    ![Karta globální nastavení](./media/clarizen-tutorial/tutorial_clarizen_002.png "Globální nastavení")

1. V dialogovém okně **federované ověřování** proveďte následující kroky:

    ![Dialogové okno federované ověřování](./media/clarizen-tutorial/tutorial_clarizen_003.png "Federované ověřování")

    a. Vyberte možnost **Povolit federované ověřování**.

    b. Kliknutím na **nahrát** nahrajte stažený certifikát.

    c. Do pole **Adresa URL pro přihlášení** zadejte hodnotu **přihlašovací adresa URL** z okna konfigurace aplikace služby Azure AD.

    d. V poli **Adresa URL pro** odhlášení zadejte hodnotu **URL pro odhlášení** z okna konfigurace aplikace služby Azure AD.

    e. Vyberte **použít příspěvek**.

    f. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Clarizen.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Clarizen**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Clarizen**.

    ![Odkaz Clarizen v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-clarizen-test-user"></a>Vytvořit testovacího uživatele Clarizen

Cílem této části je vytvořit uživatele s názvem Britta Simon v Clarizen.

**Pokud potřebujete ručně vytvořit uživatele, proveďte prosím následující kroky:**

Pokud chcete uživatelům Azure AD povolit, aby se přihlásili k Clarizen, musíte zřídit uživatelské účty. V případě Clarizen je zřizování ručním úkolem.

1. Přihlaste se k webu Clarizen společnosti jako správce.

2. Klikněte na **lidé**.

    ![Kliknutí na lidé](./media/clarizen-tutorial/create_aaduser_001.png "People")

3. Klikněte na **pozvat uživatele**.

    ![Tlačítko pozvat uživatele](./media/clarizen-tutorial/create_aaduser_002.png "Pozvat uživatele")

1. V dialogovém okně **pozvat lidi** proveďte následující kroky:

    ![Dialogové okno pozvat lidi](./media/clarizen-tutorial/create_aaduser_003.png "Pozvat lidi")

    a. Do pole **e-mail** zadejte e-mailovou adresu účtu Britta Simon.

    b. Klikněte na **pozvat**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz pro potvrzení, že účet ještě nebude aktivní.


### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Clarizen, měli byste se automaticky přihlásit k Clarizen, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
