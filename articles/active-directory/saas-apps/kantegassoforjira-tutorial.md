---
title: 'Kurz: Azure Active Directory Integration s Kantega SSO pro JIRA | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kantega SSO pro JIRA.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 9643d0e63e85a9b500021a415e3cdaf3edc756c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97608727"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Kurz: Azure Active Directory Integration s Kantega SSO pro JIRA

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování Kantega pro JIRA s Azure Active Directory (Azure AD).
Integrace Kantega SSO pro JIRA se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Kantega SSO pro JIRA.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Kantega SSO pro JIRA (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Kantega SSO pro JIRA potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Kantega SSO pro předplatné JIRA s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega SSO pro JIRA podporuje **SP a IDP** iniciované jednotné přihlašování

## <a name="adding-kantega-sso-for-jira-from-the-gallery"></a>Přidání jednotného přihlašování Kantega pro JIRA z Galerie

Pokud chcete nakonfigurovat integraci Kantega jednotného přihlašování pro JIRA do Azure AD, musíte přidat Kantega SSO pro JIRA z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Kantega SSO pro JIRA z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **KANTEGA SSO pro JIRA**, vyberte **KANTEGA SSO pro JIRA** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Kantega SSO pro JIRA v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro JIRA na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kantega SSO pro JIRA.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro JIRA, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte KANTEGA SSO pro jednotné přihlašování JIRA](#configure-kantega-sso-for-jira-single-sign-on)** a nakonfigurujte nastavení s jedním Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte KANTEGA SSO pro JIRA testovacího uživatele](#create-kantega-sso-for-jira-test-user)** – abyste měli protějšek Britta Simon v Kantega SSO pro JIRA, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro JIRA, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Kantega SSO pro** integraci aplikací JIRA vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky s konfigurací "základní S A M L" se zvýrazněným textovým polem "identifikátor" a "Reply U R L" a s vybraným tlačítkem Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Kantega SSO pro přihlašovací údaje domény JIRA a adresy URL jednotného přihlašování](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in JIRA, který je vysvětlen dále v tomto kurzu.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavení jednotného přihlašování Kantega pro JIRA** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-kantega-sso-for-jira-single-sign-on"></a>Konfigurace Kantega SSO pro JIRA Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k místnímu serveru JIRA jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Doplňky**.

    ![Snímek obrazovky, na kterém je vybraná ikona "ozubeného kola" a v rozevíracím seznamu je vybraná možnost Přidat doplňky](./media/kantegassoforjira-tutorial/addon1.png)

1. V části karta doplňky klikněte na **najít nové doplňky**. Vyhledejte **KANTEGA SSO pro JIRA (SAML & Kerberos)** a klikněte na tlačítko **instalovat** a nainstalujte nový modul plug-in SAML.

    ![Snímek obrazovky s oddílem "najít nové doplňky" s "Kantego S S informacemi pro JIRA (S A M L & Kerberos)" do vyhledávacího pole a vybraného tlačítka pro instalaci.](./media/kantegassoforjira-tutorial/addon2.png)

1. Spustí se instalace modulu plug-in.

    ![Snímek obrazovky, který zobrazuje dialogové okno "instalace" modulu plug-in.](./media/kantegassoforjira-tutorial/addon3.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Snímek obrazovky s informacemi o tom, že je nainstalovaný a připravený k přechodu dialog s vybranou akcí zavřít](./media/kantegassoforjira-tutorial/addon33.png)

1.  Klikněte na **Manage** (Spravovat).

    ![Snímek obrazovky zobrazující stránku aplikace Kantega s S názvem s vybraným tlačítkem spravovat.](./media/kantegassoforjira-tutorial/addon34.png)
    
1. Nový modul plug-in je uvedený v části **integrace**. Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Snímek obrazovky, který zobrazuje "integrace" v navigační nabídce na levé straně a zvýrazněné tlačítko konfigurovat, které je vybráno v části "Správa doplňků".](./media/kantegassoforjira-tutorial/addon35.png)

1. V části **SAML** . V rozevíracím seznamu **Přidat poskytovatele identity** vyberte **Azure Active Directory (Azure AD)** .

    ![Snímek obrazovky, který zobrazuje rozevírací seznam přidat zprostředkovatele identity s vybraným Azure Active Directory (Azure A D).](./media/kantegassoforjira-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Snímek obrazovky, který zobrazuje oddíl "Příprava Azure A D" pomocí příkazu "Basic".](./media/kantegassoforjira-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky: 

    ![Snímek obrazovky s oddílem "vlastnosti aplikace" se zvýrazněným textovým polem App I D U R L a vybraným tlačítkem pro kopírování a tlačítkem Další](./media/kantegassoforjira-tutorial/addon6.png)

    1. Zkopírujte hodnotu **identifikátoru URI ID aplikace** a použijte ji jako **identifikátor, adresu URL odpovědi a adresu URL Sign-On** v části **základní konfigurace SAML** v Azure Portal.

    1. Klikněte na **Next** (Další).

1. V části **import metadat** proveďte následující kroky: 

    ![Snímek obrazovky s vybraným oddílem "import metadat" se souborem metadat v mém počítači](./media/kantegassoforjira-tutorial/addon7.png)

    1. Vyberte **soubor metadat na mém počítači** a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    1. Klikněte na **Next** (Další).

1. V části **název a umístění jednotného přihlašování** proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje "název a S S S/S" umístění "se zvýrazněným názvem poskytovatele identity" a tlačítkem "Další".](./media/kantegassoforjira-tutorial/addon8.png)

    1. Do textového pole **název zprostředkovatele** identity přidejte jméno poskytovatele identity (např. Azure AD).

    1. Klikněte na **Next** (Další).

1. Ověřte podpisový certifikát a klikněte na tlačítko **Další**.

    ![Snímek obrazovky, který zobrazuje oddíl "ověření podpisu" s vybraným tlačítkem "Další".](./media/kantegassoforjira-tutorial/addon9.png)

1. V části **uživatelské účty JIRA** proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje "uživatelské účty JIRA" se zvýrazněnou možností "vytvořit uživatele v interním adresáři JIRA", pokud je to potřeba, a vybrané tlačítko "Další".](./media/kantegassoforjira-tutorial/addon10.png)

    1. **V případě potřeby vyberte možnost vytvořit uživatele v interním adresáři JIRA** a zadejte vhodný název skupiny pro uživatele (může to být víc. skupin oddělených čárkami).

    1. Klikněte na **Next** (Další).

1. Klikněte na **Finish** (Dokončit).

    ![Snímek obrazovky zobrazující, že se v části Souhrn zobrazuje tlačítko Dokončit.](./media/kantegassoforjira-tutorial/addon11.png)

1. V části **známé domény pro Azure AD** proveďte následující kroky:

    ![Konfigurace jednoho Sign-On](./media/kantegassoforjira-tutorial/addon12.png)

    1. Na levém panelu stránky vyberte **známé domény** .

    2. Do textového pole **známé domény** zadejte název domény.

    3. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    1. Do pole **název** zadejte **BrittaSimon**.

    1. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Kantega SSO pro JIRA.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Kantega SSO pro JIRA**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **KANTEGA SSO pro JIRA**.

    ![Odkaz Kantega SSO pro JIRA v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-kantega-sso-for-jira-test-user"></a>Vytvořit jednotné přihlašování Kantega pro JIRA testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k JIRA, musí se zřídit v JIRA. V Kantega SSO pro JIRA je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k místnímu serveru JIRA jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Správa uživatelů**.

    ![Snímek obrazovky s vybranou ikonou "ozubeného kola" a "Správa uživatelů" vybraná v rozevíracím seznamu.](./media/kantegassoforjira-tutorial/user1.png) 

1. V části karta **Správa uživatelů** klikněte na možnost **vytvořit uživatele**.

    ![Snímek obrazovky, který zobrazuje oddíl Správa uživatelů pomocí vybraného tlačítka pro vytvoření uživatele](./media/kantegassoforjira-tutorial/user2.png) 

1. Na stránce **vytvořit nového uživatele** proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforjira-tutorial/user3.png) 

    1. Do textového pole **e-mailová adresa** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

    2. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je Britta Simon.

    3. Do textového pole **uživatelské jméno** zadejte e-maily jako uživatel Brittasimon@contoso.com .

    4. Do textového pole **heslo** zadejte heslo uživatele.

    5. Klikněte na **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kantega SSO for JIRA na přístupovém panelu byste se měli automaticky přihlásili k Kantega SSO pro JIRA, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)