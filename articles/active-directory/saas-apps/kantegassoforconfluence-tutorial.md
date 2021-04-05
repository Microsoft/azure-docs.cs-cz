---
title: 'Kurz: Azure Active Directory Integration s Kantega SSO pro Confluence | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kantega SSO pro Confluence.
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
ms.openlocfilehash: be86e04359c29696d208994d85d36b7740b60cc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101646210"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Kurz: Azure Active Directory Integration s Kantega SSO pro Confluence

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování Kantega pro Confluence s Azure Active Directory (Azure AD).
Integrace Kantega SSO pro Confluence se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Kantega SSO pro Confluence.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Kantega SSO pro Confluence (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Kantega SSO pro Confluence potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Kantega SSO pro předplatné Confluence s povoleným jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kantega SSO pro Confluence podporuje **SP a IDP** iniciované jednotné přihlašování

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Přidání jednotného přihlašování Kantega pro Confluence z Galerie

Pokud chcete nakonfigurovat integraci Kantega jednotného přihlašování pro Confluence do Azure AD, musíte přidat Kantega SSO pro Confluence z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Kantega SSO pro Confluence z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **KANTEGA SSO pro Confluence**, vyberte **Kantega SSO pro Confluence** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Kantega SSO pro Confluence v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Confluence na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kantega SSO pro Confluence.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Confluence, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte KANTEGA SSO pro jednotné přihlašování Confluence](#configure-kantega-sso-for-confluence-single-sign-on)** a nakonfigurujte nastavení s jedním Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte KANTEGA SSO pro Confluence testovacího uživatele](#create-kantega-sso-for-confluence-test-user)** – abyste měli protějšek Britta Simon v Kantega SSO pro Confluence, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí jednotného přihlašování Kantega pro Confluence, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce **Kantega SSO pro** integraci aplikací Confluence vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky s oddílem konfigurace "základní S a M L" s vybranými poli "identifikátor" a "Reply U R L", který je označený a tlačítko Uložit.](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![Kantega SSO pro přihlašovací údaje domény Confluence a adresy URL jednotného přihlašování](common/metadata-upload-additional-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a adresou URL Sign-On. Tyto hodnoty jsou obdrženy během konfigurace modulu plug-in Confluence, který je vysvětlen dále v tomto kurzu.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **nastavení jednotného přihlašování Kantega pro Confluence** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Konfigurace Kantega SSO pro Confluence Single Sign-On

1. V jiném okně webového prohlížeče se přihlaste k **portálu pro správu Confluence** jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Doplňky**.

    ![Snímek obrazovky, na kterém se zobrazuje ikona nabídky "ozubeného kola" a volba "doplňky".](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Na kartě **tržiště ATLASSIAN** klikněte na **najít nové doplňky**.

    ![Snímek obrazovky, který zobrazuje kartu "ATTLASSIAN MARKETPLACE" s vybranými možnostmi najít nové doplňky.](./media/kantegassoforconfluence-tutorial/addon.png)

1. Vyhledejte **KANTEGA SSO pro CONFLUENCE SAML Kerberos** a klikněte na **instalovat** tlačítko pro instalaci nového modulu plug-in SAML.

    ![Snímek obrazovky se stránkou "najít nové doplňky" s "Kantega S S O" pro Confluence S A M L Kerberos "ve vyhledávacím poli a vybraném tlačítku pro instalaci.](./media/kantegassoforconfluence-tutorial/addon2.png)

1. Spustí se instalace modulu plug-in.

    ![Snímek obrazovky znázorňující obrazovku "instalace modulu plug-in".](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Po dokončení instalace. Klikněte na **Zavřít**.

    ![Snímek obrazovky, na kterém se zobrazuje obrazovka "nainstalováno a připraveno k instalaci", která má vybranou akci zavřít](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Klikněte na **Manage** (Spravovat).

    ![Snímek obrazovky, který zobrazuje Kantega jednotné přihlašování s protokolem Kerberos a S A M L s vybraným tlačítkem spravovat.](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Kliknutím na **Konfigurovat** Nakonfigurujte nový modul plug-in.

    ![Snímek obrazovky se stránkou Kantega jednotného přihlašování pomocí protokolu Kerberos a S A M L s vybraným tlačítkem konfigurovat.](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Tento nový modul plug-in je taky možné najít na kartě **uživatelé & zabezpečení** .

    ![Snímek obrazovky, který zobrazuje kartu "uživatelé & zabezpečení" s vybranou akcí "jednotné přihlašování" Kantega.](./media/kantegassoforconfluence-tutorial/addon36.png)

1. V části **SAML** . V rozevíracím seznamu **Přidat poskytovatele identity** vyberte **Azure Active Directory (Azure AD)** .

    ![Snímek obrazovky, který zobrazuje část S A M L s vybranou možnost Přidat zprostředkovatele identity a Azure Active Directory (Azure AD).](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Vyberte úroveň předplatného jako **základní**.

    ![Snímek obrazovky zobrazující stránku "Příprava služby Azure AD" s vybraným "základní".](./media/kantegassoforconfluence-tutorial/addon5.png)

1. V části **Vlastnosti aplikace** proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje část "vlastnosti aplikace" s polem App I D U R L a zvýrazněným tlačítkem pro kopírování a vybraným tlačítkem Další.](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Zkopírujte hodnotu **identifikátoru URI ID aplikace** a použijte ji jako **identifikátor, adresu URL odpovědi a adresu URL Sign-On** v části **základní konfigurace SAML** v Azure Portal.

    b. Klikněte na **Next** (Další).

1. V části **import metadat** proveďte následující kroky: 

    ![Snímek obrazovky s vybraným oddílem "import metadat" se souborem metadat v mém počítači](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Vyberte **soubor metadat na mém počítači** a nahrajte soubor metadat, který jste stáhli z Azure Portal.

    b. Klikněte na **Next** (Další).

1. V části **název a umístění jednotného přihlašování** proveďte následující kroky:

    ![Snímek obrazovky, který zobrazuje "název a S S S/S" umístění "se zvýrazněným názvem poskytovatele identity" a tlačítkem "Další".](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Do textového pole **název zprostředkovatele** identity přidejte jméno poskytovatele identity (např. Azure AD).

    b. Klikněte na **Next** (Další).

1. Ověřte podpisový certifikát a klikněte na tlačítko **Další**.

    ![Snímek obrazovky, který zobrazuje oddíl "ověření podpisu" s vybraným tlačítkem "Další".](./media/kantegassoforconfluence-tutorial/addon9.png)

1. V části **uživatelské účty Confluence** proveďte následující kroky:

    ![Snímek obrazovky s oddílem uživatelské účty Confluence s možností vytvořit uživatele v interním adresáři Confluence, pokud je potřeba a tlačítko Další](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. **V případě potřeby vyberte možnost vytvořit uživatele v interním adresáři Confluence** a zadejte vhodný název skupiny pro uživatele (může to být víc. skupin oddělených čárkami).

    b. Klikněte na **Next** (Další).

1. Klikněte na **Finish** (Dokončit).

    ![Snímek obrazovky se stránkou souhrnu s vybraným tlačítkem Dokončit](./media/kantegassoforconfluence-tutorial/addon11.png)

1. V části **známé domény pro Azure AD** proveďte následující kroky: 

    ![Snímek obrazovky zobrazující stránku známé domény pro Azure AD se zvýrazněným textovým polem známé domény a vybraným tlačítkem Uložit.](./media/kantegassoforconfluence-tutorial/addon12.png)

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Kantega SSO pro Confluence.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **Kantega SSO pro Confluence**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **KANTEGA SSO pro Confluence**.

    ![Odkaz Kantega SSO pro Confluence v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-kantega-sso-for-confluence-test-user"></a>Vytvořit jednotné přihlašování Kantega pro Confluence testovacího uživatele

Aby se uživatelé Azure AD mohli přihlašovat k Confluence, musí se zřídit v Confluence. V případě jednotného přihlašování Kantega pro Confluence je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se k Kantega SSO pro web Confluence jako správce.

1. Najeďte myší na ozubeného kola a klikněte na **Správa uživatelů**.

    ![Snímek obrazovky zobrazující ikonu "ozubeného kola" a "Správa uživatelů".](./media/kantegassoforconfluence-tutorial/user1.png)

1. V části Uživatelé klikněte na kartu **Přidat uživatele** . Na stránce **Přidat uživatelský** dialog proveďte následující kroky:

    ![Přidat zaměstnance](./media/kantegassoforconfluence-tutorial/user2.png)

    a. Do textového pole **uživatelské jméno** zadejte e-maily jako uživatel Brittasimon@contoso.com .

    b. Do textového pole **celé jméno** zadejte jméno a příjmení uživatele, jako je Britta Simon.

    c. Do textového pole **e-mail** zadejte e-mailovou adresu uživatele Brittasimon@contoso.com .

    d. Do textového pole **heslo** zadejte heslo pro uživatele.

    e. Klikněte na **Potvrdit heslo** znovu zadejte heslo.

    f. Klikněte na tlačítko **Přidat** .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Kantega SSO for Confluence na přístupovém panelu byste se měli automaticky přihlásili k Kantega SSO pro Confluence, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)