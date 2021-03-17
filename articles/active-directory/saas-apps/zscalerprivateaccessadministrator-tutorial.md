---
title: 'Kurz: Azure Active Directory integrace se správcem privátního přístupu Zscaler | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a správcem privátního přístupu Zscaler.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.openlocfilehash: 803f5c25429b33e6d1af130196d38e125b410eb4
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890968"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Kurz: Azure Active Directory integrace se správcem privátního přístupu Zscaler

V tomto kurzu se naučíte integrovat správce privátního přístupu Zscaler s Azure Active Directory (Azure AD).
Integrace správce privátního přístupu Zscaler s Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, který má přístup k Zscaler správce privátního přístupu.
* Uživatelům můžete povolit, aby se automaticky přihlásili k Zscaler správce privátního přístupu (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Předpoklady

Ke konfiguraci integrace služby Azure AD pomocí Správce privátního přístupu Zscaler potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným jednotným přihlašováním pro Zscaler správce privátního přístupu

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Správce privátního přístupu Zscaler podporuje **aktualizace SP** a **IDP** , iniciované jednotné přihlašování.

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Přidání správce privátního přístupu Zscaler z Galerie

Pokud chcete nakonfigurovat integraci správce privátního přístupu Zscaler do služby Azure AD, musíte do seznamu spravovaných aplikací pro SaaS přidat Zscaler správce privátního přístupu z galerie.

**Pokud chcete přidat správce privátního přístupu Zscaler z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Zscaler správce privátního přístupu** , vyberte **Zscaler správce privátního přístupu** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![Zscaler správce privátního přístupu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Správce privátního přístupu Zscaler na základě testovacího uživatele s názvem **Britta Simon** .
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Zscaler správce privátního přístupu.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Správce privátního přístupu Zscaler, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování Zscaler správcem privátního přístupu](#configure-zscaler-private-access-administrator-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte testovacího uživatele Zscaler správce privátního přístupu](#create-zscaler-private-access-administrator-test-user)** , který má protějšek Britta Simon v Zscaler správce privátního přístupu, který se odkazuje na reprezentaci uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Správce privátního přístupu Zscaler, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikace **správce privátního přístupu Zscaler** vyberte **jednotné přihlašování** .

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující kroky:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat hodnoty, které jsou popsány.](common/idp-relay.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Klikněte na **nastavit další adresy URL** .

    d. Do textového pole **stav přenosu** zadejte adresu URL: `idpadminsso`

5.  Pokud chcete nakonfigurovat aplikaci v režimu **SP** iniciované, proveďte následující krok:

    ![Snímek obrazovky s nastavením další U R ls, kde můžete zadat přihlášení U R L.](common/both-signonurl.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Chcete-li získat tyto hodnoty, obraťte se na [tým podpory klienta podpory Zscaler pro správce privátního přístupu](https://help.zscaler.com/zpa-submit-ticket) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **Nastavení správce privátního přístupu Zscaler** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-zscaler-private-access-administrator-single-sign-on"></a>Konfigurace Zscaler správce privátního přístupu (Single Sign-On)

1. V jiném okně webového prohlížeče se přihlaste k Zscaler správce privátního přístupu jako správce.

2. V horní části klikněte na **Správa** a přejděte do části **ověřování** klikněte na **Konfigurace IDP** .

    ![Správce Zscaler pro privátní přístup](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. V pravém horním rohu klikněte na **Přidat konfiguraci IDP** . 

    ![Zscaler správce privátního přístupu addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

4. Na stránce **Přidat konfiguraci IDP** proveďte následující kroky:
 
    ![Zscaler správce privátního přístupu idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Klikněte na **Vybrat soubor** a nahrajte stažený soubor metadat z Azure AD do pole pro **nahrání souboru metadat IDP** .

    b. Přečte **metadata IDP** z Azure AD a naplní všechny informace o polích, jak je uvedeno níže.

    ![Zscaler správce privátního přístupu idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Vyberte možnost **jednotné přihlašování** jako **správce** .

    d. Vyberte pole doména z **domén** .
    
    e. Klikněte na **Uložit** .

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD 

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory** , vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé** .

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon** .
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit** .

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Zscaler správci privátního přístupu.

1. V Azure Portal vyberte **podnikové aplikace** , vyberte **všechny aplikace** a pak vyberte **správce privátního přístupu Zscaler** .

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Zscaler správce privátního přístupu** .

    ![Odkaz na správce privátního přístupu Zscaler v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny** .

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-zscaler-private-access-administrator-test-user"></a>Vytvořit testovacího uživatele pro správce privátního přístupu Zscaler

Aby se uživatelé Azure AD mohli přihlásit k Zscaler správcem privátního přístupu, musí se zřídit v Zscaler správce privátního přístupu. V případě správce privátního přístupu Zscaler je zřizování ručním úkolem.

**Chcete-li zřídit uživatelský účet, proveďte následující kroky:**

1. Přihlaste se ke svému webu Správce privátního přístupu Zscaler jako správce.

2. V horní části klikněte na **Správa** a přejděte do části **ověřování** klikněte na **Konfigurace IDP** .

    ![Správce Zscaler pro privátní přístup](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

3. Klikněte na tlačítko **Správci** z levé strany nabídky.

    ![Správce správce privátního přístupu Zscaler](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

4. V pravém horním rohu klikněte na **přidat správce** :

    ![Správce privátního přístupu Zscaler přidat správce](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

5. Na stránce **přidat správce** proveďte následující kroky:

    ![Správce uživatele Zscaler pro privátní přístup](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Do textového pole **uživatelské jméno** zadejte e-mail uživatele jako BrittaSimon@contoso.com .

    b. Do textového pole **heslo** zadejte heslo.

    c. Do textového pole **Potvrdit heslo** zadejte heslo.

    d. Vyberte **role** jako **správce privátního přístupu Zscaler** .

    e. Do textového pole **e-mail** zadejte e-maily uživatele, jako je BrittaSimon@contoso.com .

    f. Do textového pole **telefon** zadejte telefonní číslo.

    například V poli **časové pásmo** vyberte časové pásmo.

    h. Klikněte na **Uložit** .

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Po kliknutí na dlaždici Správce privátního přístupu Zscaler na přístupovém panelu byste měli být automaticky přihlášení ke Správci privátního přístupu Zscaler, pro který jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)