---
title: 'Kurz: Azure Active Directory integrace s Brightspace podle Desire2Learn | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Brightspace pomocí Desire2Learn.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/08/2019
ms.author: jeedes
ms.openlocfilehash: 91d48e42dc1c92e05e963b4c8b53e44daa403378
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456686"
---
# <a name="tutorial-azure-active-directory-integration-with-brightspace-by-desire2learn"></a>Kurz: Azure Active Directory integrace s Brightspace pomocí Desire2Learn

V tomto kurzu se naučíte, jak integrovat Brightspace pomocí Desire2Learn s Azure Active Directory (Azure AD).
Integrace Brightspace by Desire2Learn se službou Azure AD poskytuje následující výhody:

* Můžete řídit v Azure AD, kteří mají přístup k Brightspace pomocí Desire2Learn.
* Můžete povolit, aby se vaši uživatelé automaticky přihlásili k Brightspace prostřednictvím Desire2Learn (jednotné přihlašování) se svými účty Azure AD.
* Účty můžete spravovat v jednom centrálním umístění – Azure Portal.

Pokud chcete získat další podrobnosti o integraci aplikace SaaS s Azure AD, přečtěte si téma [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

Ke konfiguraci integrace služby Azure AD s Brightspace pomocí Desire2Learn potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verzi [tady](https://azure.microsoft.com/pricing/free-trial/) .
* Brightspace podle povoleného předplatného jednotného přihlašování Desire2Learn

> [!NOTE]
> Tato integrace je taky dostupná pro použití z cloudového prostředí Azure AD USA. Tuto aplikaci můžete najít v galerii cloudových aplikací pro státní správu Azure AD USA a nakonfigurovat ji stejným způsobem jako ve veřejném cloudu.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Brightspace by Desire2Learn podporuje **IDP** iniciované jednotného přihlašování.

## <a name="adding-brightspace-by-desire2learn-from-the-gallery"></a>Přidání Brightspace pomocí Desire2Learn z Galerie

Pokud chcete nakonfigurovat integraci Brightspace by Desire2Learn do služby Azure AD, musíte přidat Brightspace od galerie Desire2Learn z Galerie do svého seznamu spravovaných aplikací SaaS.

**Pokud chcete přidat Brightspace podle Desire2Learn z Galerie, proveďte následující kroky:**

1. V **[Azure Portal](https://portal.azure.com)** na levém navigačním panelu klikněte na ikonu **Azure Active Directory** .

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte na **podnikové aplikace** a vyberte možnost **všechny aplikace** .

    ![Okno podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **Nová aplikace** v horní části dialogového okna.

    ![Tlačítko Nová aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Brightspace by Desire2Learn**, vyberte **Brightspace podle Desire2Learn** z panelu výsledků a potom kliknutím na tlačítko **Přidat** přidejte aplikaci.

     ![Brightspace podle Desire2Learn v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a testování jednotného přihlašování Azure AD

V této části nakonfigurujete a otestujete jednotné přihlašování Azure AD pomocí Brightspace pomocí Desire2Learn na základě testovacího uživatele s názvem **Britta Simon**.
Aby jednotné přihlašování fungovalo, musí se zřídit vztah propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Brightspace podle Desire2Learn.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Brightspace od Desire2Learn, je nutné dokončit následující stavební bloky:

1. **[Nakonfigurujte jednotné přihlašování Azure AD](#configure-azure-ad-single-sign-on)** a Umožněte uživatelům používat tuto funkci.
2. **[Nakonfigurujte Brightspace pomocí jednotného přihlašování Desire2Learn](#configure-brightspace-by-desire2learn-single-sign-on)** – ke konfiguraci nastavení jediného Sign-On na straně aplikace.
3. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí Britta Simon.
4. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – pro povolení Britta Simon pro použití jednotného přihlašování Azure AD.
5. **[Vytvořte Brightspace pomocí Desire2Learn testovacího uživatele](#create-brightspace-by-desire2learn-test-user)** – abyste měli protějšek Britta Simon v Brightspace pomocí Desire2Learn, který je propojený s reprezentací uživatele v Azure AD.
6. **[Otestujte jednotné přihlašování](#test-single-sign-on)** – ověřte, jestli konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace jednotného přihlašování Azure AD

V této části povolíte jednotné přihlašování Azure AD v Azure Portal.

Pokud chcete nakonfigurovat jednotné přihlašování Azure AD pomocí Brightspace pomocí Desire2Learn, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com/)na stránce Application Integration **Brightspace podle Desire2Learn** vyberte **jednotné přihlašování**.

    ![Konfigurovat odkaz jednotného přihlašování](common/select-sso.png)

2. V dialogovém okně **Vyberte metodu jednotného přihlašování** vyberte možnost režim **SAML/WS** , čímž povolíte jednotné přihlašování.

    ![Režim výběru jednotného přihlašování](common/select-saml-option.png)

3. Na stránce **nastavit jeden Sign-On s SAML** klikněte na **Upravit** ikona a otevře se základní dialogové okno **Konfigurace SAML** .

    ![Upravit základní konfiguraci SAML](common/edit-urls.png)

4. Na stránce **nastavit jeden Sign-On se** stránkou SAML proveďte následující kroky:

    ![Brightspace podle informací o jednotném přihlašování v doméně Desire2Learn a adresách URL](common/idp-intiated.png)

    a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru:
    
    ```http
    https://<companyname>.tenants.brightspace.com/samlLogin
    https://<companyname>.desire2learn.com/shibboleth-sp
    ```

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<companyname>.desire2learn.com/d2l/lp/auth/login/samlLogin.d2l`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem a adresou URL odpovědi. Pokud chcete získat tyto hodnoty, kontaktujte [Brightspace podle týmu podpory klientů Desire2Learn](https://www.d2l.com/contact/) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

5. Na stránce **nastavit jeden Sign-On se** stránkou SAML v části **podpisový certifikát SAML** klikněte na **Stáhnout** a Stáhněte si **XML federačních metadat** z daných možností podle vašich požadavků a uložte ho do svého počítače.

    ![Odkaz na stažení certifikátu](common/metadataxml.png)

6. V části **Nastavení Brightspace podle Desire2Learn** zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Odhlašovací adresa URL

### <a name="configure-brightspace-by-desire2learn-single-sign-on"></a>Konfigurace Brightspace podle Desire2Learn jednoho Sign-On

Chcete-li nakonfigurovat jednotné přihlašování na **Brightspace po straně Desire2Learn** , je třeba odeslat stažený **kód XML federačních metadat** a příslušné zkopírované adresy URL od Azure Portal do [Brightspace podle týmu podpory Desire2Learn](https://www.d2l.com/contact/). Toto nastavení nastaví, aby bylo správně nastaveno připojení SAML SSO na obou stranách.

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

V této části povolíte Britta Simon pro použití jednotného přihlašování pomocí Azure tím, že udělíte přístup k Brightspace Desire2Learn.

1. V Azure Portal vyberte **podnikové aplikace**, vyberte **všechny aplikace**a pak vyberte **Brightspace podle Desire2Learn**.

    ![Okno Podnikové aplikace](common/enterprise-applications.png)

2. V seznamu aplikace vyberte **Brightspace podle Desire2Learn**.

    ![Odkaz Brightspace podle Desire2Learn v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **Uživatelé a skupiny**.

    ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

4. Klikněte na tlačítko **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Podokno přidat přiřazení](common/add-assign-user.png)

5. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **Britta Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

6. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, pak v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.

7. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

### <a name="create-brightspace-by-desire2learn-test-user"></a>Vytvořit Brightspace podle Desire2Learn testovacího uživatele

V této části vytvoříte uživatele s názvem Britta Simon v Brightspace pomocí Desire2Learn. Spolupracujte s [Brightspace podle Desire2Learn týmu podpory](https://www.d2l.com/contact/) a přidejte uživatele v Brightspace podle platformy Desire2Learn. Před použitím jednotného přihlašování je nutné vytvořit a aktivovat uživatele.

> [!NOTE]
> K zřizování Azure Active Directory uživatelských účtů můžete použít jiné Brightspace pomocí nástrojů pro vytváření uživatelských účtů Desire2Learn a rozhraní API, které poskytuje Brightspace od společnosti Desire2Learn.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Brightspace by Desire2Learn, měli byste se k Brightspace automaticky přihlásit pomocí Desire2Learn, pro kterou jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje

- [Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory](./tutorial-list.md)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)