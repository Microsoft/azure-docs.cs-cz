---
title: 'Kurz: Azure Active Directory Integration s Kantega SSO pro Bitbucket | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kantega SSO pro Bitbucket.
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
ms.openlocfilehash: 52879eb7cb7a9d90113971aa66c590b99b2d5e88
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459274"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-bitbucket"></a>Kurz: Azure Active Directory Integration s Kantega SSO pro Bitbucket

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování Kantega pro Bitbucket s Azure Active Directory (Azure AD).
Integrace Kantega SSO pro Bitbucket se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Kantega SSO pro Bitbucket.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Kantega SSO pro Bitbucket (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD s Kantega SSO pro Bitbucket potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Kantega SSO pro předplatné Bitbucket s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega SSO pro Bitbucket podporuje **SP a IDP** iniciované jednotné přihlašování

## <a name="adding-kantega-sso-for-bitbucket-from-the-gallery"></a>Přidání jednotného přihlašování Kantega pro Bitbucket z Galerie

Pokud chcete nakonfigurovat integraci Kantega jednotného přihlašování pro Bitbucket do Azure AD, musíte přidat Kantega SSO pro Bitbucket z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Kantega SSO pro Bitbucket z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **KANTEGA SSO pro Bitbucket**, vyberte **Kantega SSO pro Bitbucket** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Kantega SSO pro Bitbucket v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Bitbucket na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kantega SSO pro Bitbucket.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro BitBucket, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte KANTEGA SSO pro jednotné přihlašování Bitbucket](#configure-kantega-sso-for-bitbucket-single-sign-on)** a nakonfigurujte nastavení s jedním Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte KANTEGA SSO pro Bitbucket testovacího uživatele](#create-kantega-sso-for-bitbucket-test-user)** – abyste měli protějšek Britta Simon v Kantega SSO pro BitBucket, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro BitBucket, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Kantega SSO pro** integraci aplikací Bitbucket vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor, odpovědět U R L a vybrat Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in BitBucket, který je vysvětlen dále v tomto kurzu.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavení jednotného přihlašování Kantega pro Bitbucket** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-kantega-sso-for-bitbucket-single-sign-on"></a>Konfigurace Kantega SSO pro Bitbucket Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k portálu pro správu Bitbucket jako správce.

1. Klikněte na ozubeného kola a potom klikněte na **najít nové** doplňky.

    ![Snímek obrazovky zobrazuje BitBucket správu s vybranými možnostmi najít nové doplňky.](./media/kantegassoforbitbucket-tutorial/addon1.png)

1. Vyhledejte **KANTEGA SSO pro BITBUCKET SAML & Kerberos** a kliknutím na **instalovat** nainstalujete nový modul plug-in SAML.

    ![Snímek obrazovky zobrazuje Kantega SSO pro Bitbucket SAML & Kerberos s možností instalace.](./media/kantegassoforbitbucket-tutorial/addon2.png)

1. Spustí se instalace modulu plug-in.

    ![Snímek obrazovky se zobrazí průběh instalace.](./media/kantegassoforbitbucket-tutorial/addon31.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Snímek obrazovky se zobrazí tlačítko Zavřít.](./media/kantegassoforbitbucket-tutorial/addon33.png)

1. Klikněte na **Manage** (Spravovat).

    ![Snímek obrazovky se zobrazeným tlačítkem spravovat.](./media/kantegassoforbitbucket-tutorial/addon34.png)

1. Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Snímek obrazovky zobrazuje doplňky nainstalované uživatelem s vybranou konfigurací.](./media/kantegassoforbitbucket-tutorial/addon35.png)

1. V části **SAML** . V rozevíracím seznamu **Přidat poskytovatele identity** vyberte **Azure Active Directory (Azure AD)** .

    ![Snímek obrazovky zobrazuje Kantega jednu Sign-On s Azure A D vybraným jako poskytovatel identity.](./media/kantegassoforbitbucket-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Snímek obrazovky s vybraným základním sestavením Azure A D](./media/kantegassoforbitbucket-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části vlastnosti aplikace, kde můžete zadat informace v tomto kroku.](./media/kantegassoforbitbucket-tutorial/addon6.png)

    a. Zkopírujte hodnotu **identifikátoru URI ID aplikace** a použijte ji jako **identifikátor, adresu URL odpovědi a adresu URL Sign-On** v části **základní konfigurace SAML** v Azure Portal.

    b. Klikněte na **Next** (Další).

1. V části **import metadat** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v části import metadat, kde můžete vyhledat soubor metadat.](./media/kantegassoforbitbucket-tutorial/addon7.png)

    a. Vyberte **soubor metadat na mém počítači** a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    b. Klikněte na **Next** (Další).

1. V části **název a umístění jednotného přihlašování** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí název a umístění s S S, kde je Azure A D názvem zprostředkovatele identity.](./media/kantegassoforbitbucket-tutorial/addon8.png)

    a. Do textového pole **název zprostředkovatele** identity přidejte jméno poskytovatele identity (např. Azure AD).

    b. Klikněte na **Next** (Další).

1. Ověřte podpisový certifikát a klikněte na tlačítko **Další**.

    ![Snímek obrazovky ukazuje ověření podpisu.](./media/kantegassoforbitbucket-tutorial/addon9.png)

1. V části **uživatelské účty Bitbucket** proveďte následující kroky:

    ![Snímek obrazovky zobrazuje uživatelské účty BitBucket, kde máte možnost vytvářet uživatele.](./media/kantegassoforbitbucket-tutorial/addon10.png)

    a. **V případě potřeby vyberte možnost vytvořit uživatele v interním adresáři Bitbucket** a zadejte vhodný název skupiny pro uživatele (může to být víc. skupin oddělených čárkami).

    b. Klikněte na **Next** (Další).

1. Klikněte na **Finish** (Dokončit).

    ![Snímek obrazovky zobrazující stránku souhrnu](./media/kantegassoforbitbucket-tutorial/addon11.png)

1. V části **známé domény pro Azure AD** proveďte následující kroky:

    ![Snímek obrazovky ukazuje známé domény pro Azure A D, kde můžete provádět tyto kroky.](./media/kantegassoforbitbucket-tutorial/addon12.png)

    a. Na levém panelu stránky vyberte **známé domény** .

    b. Do textového pole **známé domény** zadejte název domény.

    c. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Kantega SSO pro Bitbucket.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Kantega SSO pro Bitbucket**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **KANTEGA SSO pro Bitbucket**.

    ![Odkaz Kantega SSO pro Bitbucket v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Vytvořit jednotné přihlašování Kantega pro Bitbucket testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k BitBucket, musí se zřídit v Bitbucket. V případě Kantega jednotného přihlašování pro Bitbucket je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k webu Bitbucket společnosti jako správce.

1. Klikněte na ikonu nastavení.

    ![Snímek obrazovky se zobrazí ikona nastavení.](./media/kantegassoforbitbucket-tutorial/user1.png) 

1. V části karta **Správa** klikněte na **Uživatelé**.

    ![Snímek obrazovky zobrazuje BitBucket správu s vybranými uživateli. ](./media/kantegassoforbitbucket-tutorial/user2.png)

1. Klikněte na **vytvořit uživatele**.

    ![Snímek obrazovky zobrazuje BitBucket správu s vybraným nástrojem vytvořit uživatele.](./media/kantegassoforbitbucket-tutorial/user3.png)   

1. Na stránce **vytvořit uživatele** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit uživatele, kde můžete provést tyto kroky.](./media/kantegassoforbitbucket-tutorial/user4.png) 

    a. Do textového pole **uživatelské jméno** zadejte e-maily jako uživatel Brittasimon@contoso.com .

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je Britta Simon.

    c. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

    d. Do textového pole **heslo** zadejte heslo uživatele.

    e. Do textového pole **Potvrdit heslo** zadejte znovu heslo uživatele.

    f. Klikněte na **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kantega SSO for Bitbucket na přístupovém panelu byste se měli automaticky přihlásili k Kantega SSO pro BitBucket, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)