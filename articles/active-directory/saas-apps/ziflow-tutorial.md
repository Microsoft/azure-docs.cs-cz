---
title: 'Kurz: Azure Active Directory integrace s Ziflow | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Ziflow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: e10ca98e0c9257187288d02483ed81915a7b321d
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92894714"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Kurz: Azure Active Directory integrace s Ziflow

V tomto kurzu se dozvíte, jak integrovat Ziflow s Azure Active Directory (Azure AD).
Integrace Ziflow s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Ziflow.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Ziflow (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Ziflow potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným Ziflowm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Ziflow podporuje jednotné přihlašování iniciované v **SP**

## <a name="adding-ziflow-from-the-gallery"></a>Přidání Ziflow z Galerie

Pokud chcete nakonfigurovat integraci Ziflow do služby Azure AD, musíte přidat Ziflow z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Ziflow z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Ziflow** , vyberte **Ziflow** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Ziflow v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Ziflow na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Ziflow.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Ziflow, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Ziflow](#configure-ziflow-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Ziflow Test User](#create-ziflow-test-user)** – pro Britta Simon v Ziflow, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Ziflow, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Ziflow** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. V části **základní konfigurace SAML** proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně Ziflow a adresách URL](common/sp-identifier.png)

    a. Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. Do textového pole **identifikátor (ID entity)** zadejte adresu URL pomocí následujícího vzoru: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Předchozí hodnoty nejsou reálné. V identifikátoru aktualizujete jedinečnou hodnotu ID a adresu URL pro přihlášení s skutečnou hodnotou, která je vysvětlena dále v tomto kurzu.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení Ziflow** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-ziflow-single-sign-on"></a>Nakonfigurovat Ziflow jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k Ziflow jako správce zabezpečení.

2. V pravém horním rohu klikněte na miniatura a pak klikněte na **Spravovat účet** .

    ![Správa konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. V levém horním rohu klikněte na **jednotné přihlašování** .

    ![Přihlašovací znak konfigurace Ziflow](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Na stránce **jednotného přihlašování** proveďte následující kroky:

    ![Konfigurace Ziflow – Single](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Jako **SAML 2.0** vyberte **typ** .

    b. Do textového pole **Adresa URL pro přihlášení** vložte hodnotu **přihlašovací adresa URL** , kterou jste zkopírovali z Azure Portal.

    c. Nahrajte do **podpisového certifikátu x509** certifikát s kódováním base-64, který jste stáhli z Azure Portal.

    d. Do textového pole **Adresa URL** pro odhlášení vložte hodnotu **URL pro odhlášení** , kterou jste zkopírovali z Azure Portal.

    e. V části **nastavení konfigurace pro poskytovatele identifikátorů** zkopírujte zvýrazněnou hodnotu jedinečného ID a přidejte ji s identifikátorem a přihlašovací adresou URL v **základní konfiguraci SAML** na Azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte brittasimon@yourcompanydomain.extension . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Ziflow.

1. V Azure Portal vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte **Ziflow** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Ziflow** .

    ![Odkaz Ziflow v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-ziflow-test-user"></a>Vytvořit testovacího uživatele Ziflow

Aby se uživatelé Azure AD mohli přihlašovat k Ziflow, musí se zřídit v Ziflow. V Ziflow je zřizování ručním úkolem.

Chcete-li zřídit uživatelský účet, proveďte následující kroky:

1. Přihlaste se k Ziflow jako správce zabezpečení.

2. Přejděte na **uživatele** nahoru.

    ![Ziflow osoby konfigurace](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klikněte na **Přidat** a pak klikněte na **Přidat uživatele** .

    ![Snímek obrazovky se zobrazí vybraná možnost Přidat uživatele.](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. V místní nabídce **Přidat uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí dialogové okno Přidat uživatele, kde můžete zadat hodnoty, které jsou popsány.](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Do textového pole **e-mailu** zadejte e-maily uživatele jako brittasimon@contoso.com .

    b. Do textového pole **jméno a příjmení** zadejte jméno uživatele jako například Britta.

    c. Do textového pole **příjmení** zadejte příjmení uživatele, jako je Simon.

    d. Vyberte svoji Ziflow roli.

    e. Klikněte na **Přidat 1 uživatele** .

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Ziflow, měli byste se automaticky přihlásit k Ziflow, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)