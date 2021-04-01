---
title: 'Kurz: Azure Active Directory integrace s RFPIO | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a RFPIO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.openlocfilehash: c4e838afa867a7fb1e7fa8f582bc8879c24056a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92506016"
---
# <a name="tutorial-azure-active-directory-integration-with-rfpio"></a>Kurz: Azure Active Directory integrace s RFPIO

V tomto kurzu se dozvíte, jak integrovat RFPIO s Azure Active Directory (Azure AD).
Integrace RFPIO s Azure AD poskytuje následující výhody:

* Můžete kontrolovat v Azure AD, kteří mají přístup k RFPIO.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k RFPIO (jednotné přihlašování) pomocí svých účtů Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s RFPIO potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/) .
* Předplatné s povoleným RFPIOm jednotným přihlašováním

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* RFPIO podporuje jednotné přihlašování (SSO) **a IDP** .

## <a name="adding-rfpio-from-the-gallery"></a>Přidání RFPIO z Galerie

Pokud chcete nakonfigurovat integraci RFPIO do služby Azure AD, musíte přidat RFPIO z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat RFPIO z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **RFPIO**, vyberte **RFPIO** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

    ![RFPIO v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí RFPIO na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v RFPIO.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí RFPIO, musíte dokončit tyto stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte jednotné přihlašování RFPIO](#configure-rfpio-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvoření RFPIO Test User](#create-rfpio-test-user)** – pro Britta Simon v RFPIO, který je propojený s reprezentací uživatele Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí RFPIO, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **RFPIO** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , proveďte v **základní části Konfigurace SAML** následující krok:

    ![Snímek obrazovky ukazuje základní konfiguraci SAML, kde můžete zadat identifikátor.](common/idp-identifier.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://www.rfpio.com`

    b. Klikněte na **nastavit další adresy URL**.

    c. Do textového pole **stav přenosu** zadejte hodnotu řetězce. Pokud chcete získat tuto hodnotu, kontaktujte [tým podpory RFPIO](https://www.rfpio.com/contact/) .

    ![Snímek obrazovky se zobrazí nastavení další U R ls.](common/idp-preintegrated-relay.png)

5. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    ![image](common/both-preintegrated-signon.png)

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://www.app.rfpio.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta RFPIO](https://www.rfpio.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

6. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

7. V části **Nastavení RFPIO** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-rfpio-single-sign-on"></a>Nakonfigurovat RFPIO jednu Sign-On

1. V jiném okně webového prohlížeče se přihlaste k webu **RFPIO** jako správce.

1. Klikněte na rozevírací seznam levého dolního rohu.

    ![Snímek obrazovky se zobrazí se šipkou dolů v dolní části podokna.](./media/rfpio-tutorial/app1.png)

1. Klikněte na **Nastavení organizace**. 

    ![Snímek obrazovky zobrazuje vybraná nastavení organizace.](./media/rfpio-tutorial/app2.png)

1. Klikněte na **funkce & integraci**.

    ![Snímek obrazovky zobrazuje funkce a integraci vybrané z nastavení.](./media/rfpio-tutorial/app4.png)

1. V **konfiguraci jednotného přihlašování SAML** klikněte na **Upravit**.

    ![Snímek obrazovky ukazuje konfiguraci SAML S S s tlačítkem upravit s názvem.](./media/rfpio-tutorial/app3.png)

1. V této části provedete následující akce:

    ![CScreenshot ukazuje konfiguraci SAML s S povoleným protokolem SAML.](./media/rfpio-tutorial/app5.png)
    
    a. Zkopírujte obsah **stažených METADAT XML** a vložte ho do pole **Konfigurace identity** .

    > [!NOTE]
    >Ke zkopírování obsahu stažených **federačních METADAT XML** použijte **program Notepad + +** nebo správný **Editor XML**.

    b. Klikněte na tlačítko **ověřit**.

    c. Po kliknutí na **ověřit** se Překlopí **SAML (povoleno)** na zapnuto.

    d. Klikněte na **Odeslat**.

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

Cílem této části je vytvořit testovacího uživatele v Azure Portal s názvem Britta Simon.

1. V Azure Portal v levém podokně vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.

    ![Odkazy "uživatelé a skupiny" a "Všichni uživatelé"](common/users.png)

2. V horní části obrazovky vyberte **Nový uživatel** .

    ![Tlačítko pro nového uživatele](common/new-user.png)

3. Ve vlastnostech uživatele proveďte následující kroky.

    ![Uživatelský dialog](common/user-properties.png)

    a. Do pole **název** zadejte **BrittaSimon**.
  
    b. Do pole **uživatelské jméno** zadejte `brittasimon@yourcompanydomain.extension` . Například BrittaSimon@contoso.com.

    c. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k RFPIO.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace** a pak vyberte **RFPIO**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **RFPIO**.

    ![Odkaz RFPIO v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-rfpio-test-user"></a>Vytvořit testovacího uživatele RFPIO

1. Přihlaste se k webu RFPIO společnosti jako správce.

1. Klikněte na rozevírací seznam levého dolního rohu.

    ![Snímek obrazovky se zobrazí se šipkou dolů v dolní části podokna.](./media/rfpio-tutorial/app1.png)

1. Klikněte na **Nastavení organizace**. 

    ![Snímek obrazovky zobrazuje vybraná nastavení organizace.](./media/rfpio-tutorial/app2.png)

1. Klikněte na **Členové týmu**.

    ![Snímek obrazovky ukazuje členy týmu vybrané z nastavení.](./media/rfpio-tutorial/app6.png)

1. Klikněte na **přidat členy**.

    ![Snímek obrazovky se zobrazí tlačítko přidat členy.](./media/rfpio-tutorial/app7.png)

1. V části **Přidat nové členy** . Proveďte následující akce:

    ![Snímek obrazovky ukazuje přidat nové členy, kde můžete zadat hodnoty, které jsou popsány.](./media/rfpio-tutorial/app8.png)

    a. Zadejte **e-mailovou adresu** do pole **zadat jednu e-mailem na řádek** .

    b. Podle svých požadavků prosím vyberte **roli** .

    c. Klikněte na **přidat členy**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory obdrží e-mail a provede odkaz k potvrzení jeho účtu předtím, než se aktivuje.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici RFPIO, měli byste se automaticky přihlásit k RFPIO, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)