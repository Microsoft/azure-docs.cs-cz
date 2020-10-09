---
title: 'Kurz: Azure Active Directory integrace s StatusPage | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2ee2eb7586489527272d92dd1eaef6976bc41f94
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88524750"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Kurz: Azure Active Directory integrace s StatusPage

V tomto kurzu se dozvíte, jak integrovat StatusPage s Azure Active Directory (Azure AD).
Integrace StatusPage s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k StatusPage.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k StatusPage (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s StatusPage potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným StatusPagem jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* StatusPage podporuje jednotné přihlašování **IDP** .

## <a name="adding-statuspage-from-the-gallery"></a>Přidání StatusPage z Galerie

Pokud chcete nakonfigurovat integraci StatusPage do služby Azure AD, musíte přidat StatusPage z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat StatusPage z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **StatusPage**, vyberte **StatusPage** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![StatusPage v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí StatusPage na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v StatusPage.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí StatusPage, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování StatusPage](#configure-statuspage-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření StatusPage Test User](#create-statuspage-test-user)** – pro Britta Simon v StatusPage, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí StatusPage, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **StatusPage** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Informace o jednotném přihlašování v doméně StatusPage a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<subdomain>.statuspagestaging.com/`
    - `https://<subdomain>.statuspage.io/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:

    - `https://<subdomain>.statuspagestaging.com/sso/saml/consume`
    - `https://<subdomain>.statuspage.io/sso/saml/consume`

    > [!NOTE]
    > Obraťte se na tým podpory StatusPage na adrese [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) a vyžádejte si metadata, která jsou nutná ke konfiguraci jednotného přihlašování. 
    >
    > a. Z metadat Zkopírujte hodnotu vystavitele a vložte ji do textového pole **identifikátoru** .
    >
    > b. Z metadat zkopírujte adresu URL odpovědi a vložte ji do textového pole **Adresa URL odpovědi** .

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **certifikát (Base64)** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

6. V části **Nastavení StatusPage** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-statuspage-single-sign-on"></a>Nakonfigurovat StatusPage jednu Sign-On

1. V jiném okně prohlížeče se přihlaste k webu StatusPage společnosti jako správce.

1. Na hlavním panelu nástrojů klikněte na **Spravovat účet**.

    ![Konfigurace jednoho Sign-On](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na kartu **jednotného přihlašování** .

    ![Konfigurace jednoho Sign-On](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:

    ![Konfigurace jednoho Sign-On](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Konfigurace jednoho Sign-On](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Do textového pole **Adresa URL cíle jednotného přihlašování** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    b. Otevřete stažený certifikát v programu Poznámkový blok, zkopírujte obsah a vložte ho do textového pole **certifikátu** .

    c. Klikněte na **Uložit konfiguraci**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé**a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole typ **uživatelského jména**`brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k StatusPage.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **StatusPage**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **StatusPage**.

    ![Odkaz StatusPage v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-statuspage-test-user"></a>Vytvořit testovacího uživatele StatusPage

Cílem této části je vytvořit uživatele s názvem Britta Simon v StatusPage.

StatusPage podporuje zřizování za běhu. V [konfiguraci jednotného přihlašování Azure AD](#configure-azure-ad-single-sign-on)jste ho už povolili.

**Chcete-li vytvořit uživatele s názvem Britta Simon v StatusPage, proveďte následující kroky:**

1. Přihlaste se k webu StatusPage společnosti jako správce.

1. V nabídce v horní části klikněte na **Spravovat účet**.

    ![Konfigurace jednoho Sign-On](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klikněte na kartu **Členové týmu** .
  
    ![Vytvoření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klikněte na **Přidat člena týmu**.
  
    ![Vytvoření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Zadejte **e-mailovou adresu**, **jméno**a **příjmení** platného uživatele, kterého chcete zřídit, do příslušných textových polí. 

    ![Vytvoření testovacího uživatele Azure AD](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Jako **role**vyberte **Správce klienta**.

1. Klikněte na **vytvořit účet**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici StatusPage, měli byste se automaticky přihlásit k StatusPage, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
