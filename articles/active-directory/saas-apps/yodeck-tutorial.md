---
title: 'Kurz: Azure Active Directory integrace s Yodeck | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Yodeck.
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
ms.openlocfilehash: 68e961336d7e86f9983fea49044137894ab0f985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895073"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Kurz: Azure Active Directory integrace s Yodeck

V tomto kurzu se dozvíte, jak integrovat Yodeck s Azure Active Directory (Azure AD).
Integrace Yodeck s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k Yodeck.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Yodeck (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Yodeck potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Předplatné s povoleným Yodeckm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Yodeck podporuje **jednotné** přihlašování (SSO) a **IDP** .

## <a name="adding-yodeck-from-the-gallery"></a>Přidání Yodeck z Galerie

Pokud chcete nakonfigurovat integraci Yodeck do služby Azure AD, musíte přidat Yodeck z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Yodeck z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Yodeck**, vyberte **Yodeck** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Yodeck v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Yodeck na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Yodeck.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Yodeck, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Yodeck](#configure-yodeck-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření Yodeck Test User](#create-yodeck-test-user)** – pro Britta Simon v Yodeck, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Yodeck, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Yodeck** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    ![Informace o jednotném přihlašování v doméně Yodeck a adresách URL](common/idp-identifier.png)

    Do textového pole **identifikátor** zadejte adresu URL:  `https://app.yodeck.com/api/v1/account/metadata/`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky ukazuje základní konfiguraci SAML s odkazem na soubor s metadaty pro nahrání.](common/both-preintegrated-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL:  `https://app.yodeck.com/login`

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML klikněte v části **podpisový certifikát SAML** na Kopírovat tlačítko a zkopírujte **adresu URL federačních metadat aplikace** a uložte ji do svého počítače.

    ![Odkaz na stažení certifikátu](common/copy-metadataurl.png)

### <a name="configure-yodeck-single-sign-on"></a>Nakonfigurovat Yodeck jednu Sign-On

1. Pokud chcete automatizovat konfiguraci v rámci **Yodeck**, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Na snímku obrazovky se zobrazí tlačítko nainstalovat rozšíření.](./media/target-process-tutorial/install_extension.png)

1. Po přidání rozšíření do prohlížeče klikněte na **Yodeck nastavení** a nasměrujte vás na aplikaci Yodeck. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Yodeck. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-5.

    ![Konfigurace instalace](common/setup-sso.png)

    **Pokud chcete aplikaci nakonfigurovat ručně, proveďte následující kroky:**

1. V jiném okně webového prohlížeče se přihlaste k webu Yodeck společnosti jako správce.

1. Klikněte na možnost **uživatelská nastavení** . v pravém horním rohu stránky vyberte **Nastavení účtu**.

    ![Snímek obrazovky se zobrazí s nastavením účtu vybraným pro uživatele.](./media/yodeck-tutorial/configure1.png)

1. Vyberte **SAML** a proveďte následující kroky:

    ![Snímek obrazovky se zobrazí na kartě SAML, kde můžete provádět tyto kroky.](./media/yodeck-tutorial/configure2.png)

    a. Vyberte **importovat z adresy URL**.

    b. Do textového pole **Adresa URL** vložte hodnotu **adresy URL federačních metadat aplikace** , kterou jste zkopírovali z Azure Portal a klikněte na **importovat**.
    
    c. Po importu **adresy URL federačních metadat aplikace** se zbývající pole naplní automaticky.

    d. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Yodeck.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Yodeck**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Yodeck**.

    ![Odkaz Yodeck v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-yodeck-test-user"></a>Vytvořit testovacího uživatele Yodeck

Aby se uživatelé Azure AD mohli přihlašovat k Yodeck, musí se zřídit v Yodeck. V případě Yodeck je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Yodeck společnosti jako správce.

2. V pravém horním rohu stránky klikněte na možnost **uživatelská nastavení** a vyberte **Uživatelé**.

    ![Snímek obrazovky se zobrazí s uživateli vybranými pro uživatele.](./media/yodeck-tutorial/user1.png)

3. Kliknutím na **+ uživatel** otevřete kartu **Podrobnosti o uživateli** .

    ![Snímek obrazovky se zobrazí tlačítko Uživatelé.](./media/yodeck-tutorial/user2.png)

4. Na stránce **Podrobnosti o uživateli** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí karta Podrobnosti o uživateli, kde můžete provést tyto kroky.](./media/yodeck-tutorial/user3.png)

    a. Do textového pole **jméno a příjmení** zadejte jméno uživatele, jako je **Britta**.

    b. Do textového pole **příjmení** zadejte jméno uživatele jako **Simon**.

    c. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele brittasimon@contoso.com .

    d. Vyberte odpovídající možnost **oprávnění účtu** podle požadavků vaší organizace.
    
    e. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Yodeck, měli byste se automaticky přihlásit k Yodeck, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)