---
title: 'Kurz: Azure Active Directory Integration s Kantega SSO pro Bamboo | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kantega SSO pro Bamboo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e238b574-9e9b-43b7-ab98-d2a87ff89d48
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 8c951d7f5f1629447b1b5c1fc6e8a1c202246d74
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099105"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bamboo"></a>Kurz: Azure Active Directory Integration s Kantega SSO pro Bamboo

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování Kantega pro Bamboo s Azure Active Directory (Azure AD).
Integrace Kantega SSO pro Bamboo se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Kantega SSO pro Bamboo.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Kantega SSO pro Bamboo (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Kantega SSO pro Bamboo potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Kantega SSO pro předplatné Bamboo s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega SSO pro Bamboo podporuje **SP a IDP** iniciované jednotné přihlašování

## <a name="adding-kantega-sso-for-bamboo-from-the-gallery"></a>Přidání jednotného přihlašování Kantega pro Bamboo z Galerie

Pokud chcete nakonfigurovat integraci Kantega jednotného přihlašování pro Bamboo do Azure AD, musíte přidat Kantega SSO pro Bamboo z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Kantega SSO pro Bamboo z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **KANTEGA SSO pro Bamboo**, vyberte **Kantega SSO pro Bamboo** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Kantega SSO pro Bamboo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Bamboo na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kantega SSO pro Bamboo.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Bamboo, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte KANTEGA SSO pro jednotné přihlašování Bamboo](#configure-kantega-sso-for-bamboo-single-sign-on)** a nakonfigurujte nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte KANTEGA SSO pro Bamboo testovacího uživatele](#create-kantega-sso-for-bamboo-test-user)** – abyste měli protějšek Britta Simon v Kantega SSO pro Bamboo, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Bamboo, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Kantega SSO pro** integraci aplikací Bamboo vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Kantega SSO pro přihlašovací údaje domény Bamboo a adresy URL jednotného přihlašování](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Kantega SSO pro přihlašovací údaje domény Bamboo a adresy URL jednotného přihlašování](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in Bamboo, který je vysvětlen dále v tomto kurzu.

6. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavení jednotného přihlašování Kantega pro Bamboo** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-kantega-sso-for-bamboo-single-sign-on"></a>Konfigurace jednotného přihlašování Kantega SSO pro Bamboo

1. V jiném okně webového prohlížeče se přihlaste k místnímu serveru Bamboo jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Doplňky**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon1.png)

1. V části karta doplňky klikněte na **najít nové doplňky**. Vyhledejte **KANTEGA SSO pro Bamboo (SAML & Kerberos)** a klikněte na tlačítko **instalovat** a nainstalujte nový modul plug-in SAML.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon2.png)

1. Spustí se instalace modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon21.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon33.png)

1. Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon34.png)

1. Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon3.png)

1. V části **SAML** . V rozevíracím seznamu **Přidat poskytovatele identity** vyberte **Azure Active Directory (Azure AD)** .

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon6.png)

    a. Zkopírujte hodnotu **identifikátoru URI ID aplikace** a použijte ji jako **identifikátor, adresu URL odpovědi a přihlašovací adresu URL** v části **základní konfigurace SAML** v Azure Portal.

    b. Klikněte na **Další**.

1. V části **import metadat** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon7.png)

    a. Vyberte **soubor metadat na mém počítači**a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    b. Klikněte na **Další**.

1. V části **název a umístění jednotného přihlašování** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon8.png)

    a. Do textového pole **název zprostředkovatele** identity přidejte jméno poskytovatele identity (např. Azure AD).

    b. Klikněte na **Další**.

1. Ověřte podpisový certifikát a klikněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon9.png)

1. V části **uživatelské účty Bamboo** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon10.png)

    a. **V případě potřeby vyberte možnost vytvořit uživatele v interním adresáři Bamboo** a zadejte vhodný název skupiny pro uživatele (může to být víc. skupin oddělených čárkami).

    b. Klikněte na **Další**.

1. Klikněte na **Finish** (Dokončit).

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon11.png)

1. V části **známé domény pro Azure AD** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/kantegassoforbamboo-tutorial/addon12.png)

    a. Na levém panelu stránky vyberte **známé domény** .

    b. Do textového pole **známé domény** zadejte název domény.

    c. Klikněte na **Uložit**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Kantega SSO pro Bamboo.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Kantega SSO pro Bamboo**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **KANTEGA SSO pro Bamboo**.

    ![Odkaz Kantega SSO pro Bamboo v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-kantega-sso-for-bamboo-test-user"></a>Vytvořit jednotné přihlašování Kantega pro Bamboo testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k Bamboo, musí se zřídit v Bamboo. V případě Kantega jednotného přihlašování pro Bamboo je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru Bamboo jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Správa uživatelů**.

    ![Přidat zaměstnance](./media/kantegassoforbamboo-tutorial/user1.png)

1. Klikněte na **Uživatelé**. V části **Přidat uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforbamboo-tutorial/user2.png)

    a. Do textového pole **uživatelské jméno** zadejte e-maily jako Brittasimon@contoso.comuživatel.

    b. Do textového pole **heslo** zadejte heslo uživatele.

    c. Do textového pole **Potvrdit heslo** zadejte znovu heslo uživatele.

    d. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je Britta Simon.

    e. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com.

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kantega SSO for Bamboo na přístupovém panelu byste se měli automaticky přihlásili k Kantega SSO pro Bamboo, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
