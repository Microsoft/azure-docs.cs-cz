---
title: 'Kurz: Azure Active Directory integraci jednotného přihlašování s Kanbanize | Microsoft Docs'
description: Přečtěte si, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Kanbanize.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2019
ms.author: jeedes
ms.openlocfilehash: 05ce0d3d3d5c66514edc07446aaf0a879657c10c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92459233"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-kanbanize"></a>Kurz: Azure Active Directory integraci jednotného přihlašování (SSO) s Kanbanize

V tomto kurzu se dozvíte, jak integrovat Kanbanize s Azure Active Directory (Azure AD). Když integrujete Kanbanize s Azure AD, můžete:

* Řízení ve službě Azure AD, která má přístup k Kanbanize.
* Umožněte, aby se vaši uživatelé automaticky přihlásili k Kanbanize svým účtům Azure AD.
* Spravujte svoje účty v jednom centrálním umístění – Azure Portal.

Další informace o integraci aplikací SaaS s Azure AD najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Předpoklady

Chcete-li začít, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).
* Kanbanize odběr s povoleným jednotným přihlašováním (SSO).

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu nakonfigurujete a otestujete jednotné přihlašování Azure AD v testovacím prostředí.

* Kanbanize podporuje jednotné přihlašování (SSO) **a IDP** .
* Kanbanize podporuje zřizování uživatelů **jenom v čase** .

## <a name="adding-kanbanize-from-the-gallery"></a>Přidání Kanbanize z Galerie

Pokud chcete nakonfigurovat integraci Kanbanize do služby Azure AD, musíte přidat Kanbanize z Galerie do svého seznamu spravovaných aplikací SaaS.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účet Microsoft.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** .
1. Přejděte na **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. Chcete-li přidat novou aplikaci, vyberte možnost **Nová aplikace**.
1. V části **Přidat z Galerie** do vyhledávacího pole zadejte **Kanbanize** .
1. Na panelu výsledků vyberte **Kanbanize** a pak aplikaci přidejte. Počkejte několik sekund, než se aplikace přidá do vašeho tenanta.

## <a name="configure-and-test-azure-ad-single-sign-on-for-kanbanize"></a>Konfigurace a testování jednotného přihlašování Azure AD pro Kanbanize

Nakonfigurujte a otestujte jednotné přihlašování Azure AD pomocí Kanbanize pomocí testovacího uživatele s názvem **B. Simon**. Aby jednotné přihlašování fungovalo, je potřeba vytvořit propojení mezi uživatelem služby Azure AD a souvisejícím uživatelem v Kanbanize.

Pokud chcete nakonfigurovat a otestovat jednotné přihlašování Azure AD pomocí Kanbanize, dokončete následující stavební bloky:

1. **[NAKONFIGURUJTE jednotné přihlašování Azure AD](#configure-azure-ad-sso)** – umožníte uživatelům používat tuto funkci.
    1. **[Vytvořte testovacího uživatele Azure AD](#create-an-azure-ad-test-user)** – k otestování jednotného přihlašování Azure AD pomocí B. Simon.
    1. **[Přiřaďte testovacího uživatele Azure AD](#assign-the-azure-ad-test-user)** – Pokud chcete povolit B. Simon používat jednotné přihlašování Azure AD.
1. **[Nakonfigurujte KANBANIZE SSO](#configure-kanbanize-sso)** – pro konfiguraci nastavení jednotného přihlašování na straně aplikace.
    1. **[Vytvořte Kanbanize Test User](#create-kanbanize-test-user)** -to, abyste měli protějšek B. Simon v Kanbanize, která je propojená s reprezentací uživatele v Azure AD.
1. **[Test SSO](#test-sso)** – ověřte, zda konfigurace funguje.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

Pomocí těchto kroků povolíte jednotné přihlašování služby Azure AD v Azure Portal.

1. V [Azure Portal](https://portal.azure.com/)na stránce integrace aplikací **Kanbanize** Najděte oddíl **Spravovat** a vyberte **jednotné přihlašování**.
1. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**.
1. Na stránce **nastavit jednotné přihlašování pomocí SAML** klikněte na ikonu Upravit/pero pro **základní konfiguraci SAML** a upravte nastavení.

   ![Upravit základní konfiguraci SAML](common/edit-urls.png)

1. Pokud chcete nakonfigurovat aplikaci v režimu iniciované **IDP** , zadejte v **základní části Konfigurace SAML** hodnoty následujících polí:

     a. Do textového pole **identifikátor** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.kanbanize.com/`

    b. Do textového pole **Adresa URL odpovědi** zadejte adresu URL pomocí následujícího vzoru: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Klikněte na **nastavit další adresy URL**.

    d. Do textového pole **stav přenosu** zadejte adresu URL: `/ctrl_login/saml_login`

1. Klikněte na **nastavit další adresy URL** a proveďte následující krok, pokud chcete nakonfigurovat aplikaci v režimu iniciované **SP** :

    Do textového pole **přihlašovací adresa URL** zadejte adresu URL pomocí následujícího vzoru:  `https://<subdomain>.kanbanize.com`

    > [!NOTE]
    > Tyto hodnoty nejsou reálné. Aktualizujte tyto hodnoty skutečným identifikátorem, adresou URL odpovědi a přihlašovací adresou URL. Pokud chcete získat tyto hodnoty, obraťte se na [tým podpory klienta Kanbanize](mailto:support@ms.kanbanize.com) . Můžete se také podívat na vzory uvedené v části **základní konfigurace SAML** v Azure Portal.

1. Kanbanize aplikace očekává kontrolní výrazy SAML v určitém formátu, což vyžaduje přidání mapování vlastních atributů do konfigurace atributů tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, kde NameIdentifier je mapován pomocí **User. userPrincipalName**. Aplikace Kanbanize očekává, že NameIdentifier budou mapovány pomocí **User. mail**, takže potřebujete upravit mapování atributů kliknutím na ikonu Upravit a změnit mapování atributů.

    ![image](common/edit-attribute.png)

1. Na stránce **nastavit jednotné přihlašování pomocí SAML** v části **podpisový certifikát SAML** vyhledejte **certifikát (Base64)** a vyberte **Stáhnout** a Stáhněte certifikát a uložte ho do počítače.

    ![Odkaz na stažení certifikátu](common/certificatebase64.png)

1. V části **Nastavení Kanbanize** zkopírujte na základě vašeho požadavku příslušné adresy URL.

    ![Kopírovat adresy URL konfigurace](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Vytvoření testovacího uživatele Azure AD

V této části vytvoříte testovacího uživatele ve Azure Portal s názvem B. Simon.

1. V levém podokně Azure Portal vyberte možnost **Azure Active Directory**, vyberte možnost **Uživatelé** a potom vyberte možnost **Všichni uživatelé**.
1. V horní části obrazovky vyberte **Nový uživatel** .
1. Ve vlastnostech **uživatele** proveďte následující kroky:
   1. Do pole **Název** zadejte `B.Simon`.  
   1. Do pole **uživatelské jméno** zadejte username@companydomain.extension . Například, `B.Simon@contoso.com`.
   1. Zaškrtněte políčko **Zobrazit heslo** a pak zapište hodnotu, která se zobrazí v poli **heslo** .
   1. Klikněte na **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřazení testovacího uživatele Azure AD

V této části povolíte B. Simon pro použití jednotného přihlašování Azure tím, že udělíte přístup k Kanbanize.

1. V Azure Portal vyberte **podnikové aplikace** a pak vyberte **všechny aplikace**.
1. V seznamu aplikace vyberte **Kanbanize**.
1. Na stránce Přehled aplikace najděte část **Správa** a vyberte **Uživatelé a skupiny**.

   ![Odkaz uživatelé a skupiny](common/users-groups-blade.png)

1. Vyberte **Přidat uživatele** a pak v dialogovém okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

    ![Odkaz Přidat uživatele](common/add-assign-user.png)

1. V dialogovém okně **Uživatelé a skupiny** vyberte v seznamu uživatelé možnost **B. Simon** a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. Pokud očekáváte hodnotu role v kontrolním výrazu SAML, v dialogovém okně **Vybrat roli** vyberte v seznamu příslušnou roli pro uživatele a pak klikněte na tlačítko **Vybrat** v dolní části obrazovky.
1. V dialogovém okně **Přidat přiřazení** klikněte na tlačítko **přiřadit** .

## <a name="configure-kanbanize-sso"></a>Konfigurace jednotného přihlašování Kanbanize

1. Pokud chcete automatizovat konfiguraci v rámci Kanbanize, je potřeba nainstalovat rozšíření **prohlížeče zabezpečeného přihlašování aplikace** kliknutím na **instalovat rozšíření**.

    ![Rozšíření moje aplikace](common/install-myappssecure-extension.png)

2. Po přidání rozšíření do prohlížeče klikněte na **nastavit Kanbanize** , které vás přesměruje do aplikace Kanbanize. Odtud zadejte přihlašovací údaje správce, které se přihlásí k Kanbanize. Rozšíření prohlížeče automaticky provede konfiguraci aplikace za vás a automatizujte kroky 3-7.

    ![Konfigurace instalace](common/setup-sso.png)

3. Pokud chcete nastavit Kanbanize ručně, otevřete nové okno webového prohlížeče a přihlaste se k webu Kanbanize společnosti jako správce a proveďte následující kroky:

4. Přejděte na stránku vpravo nahoře a klikněte na logo **Nastavení** .

    ![Nastavení Kanbanize](./media/kanbanize-tutorial/tutorial-kanbanize-set.png)

5. Na stránce panelu pro správu na levé straně nabídky klikněte na **integrace** a pak povolte **jednotné přihlašování**.

    ![Snímek obrazovky se zobrazí panel pro správu s vybraným integrací.](./media/kanbanize-tutorial/tutorial-kanbanize-admin.png)

6. V části integrace klikněte na **Konfigurovat** a otevřete stránku **integrace s jedním Sign-On** .

    ![Kanbanize konfigurace](./media/kanbanize-tutorial/tutorial-kanbanize-config.png)

7. Na stránce **integrace s jedním Sign-On** v části **Konfigurace** proveďte následující kroky:

    ![Snímek obrazovky se zobrazí stránka integrace na jednom Sign-On, kde zadáte hodnoty v tomto kroku.](./media/kanbanize-tutorial/tutorial-kanbanize-save.png)

    a. Do textového pole **ID entity IDP** vložte hodnotu **identifikátoru Azure AD**, kterou jste zkopírovali z Azure Portal.

    b. Do textového pole **koncového bodu přihlášení IDP** vložte hodnotu **adresy URL pro přihlášení**, kterou jste zkopírovali z Azure Portal.

    c. Do textového pole **koncového bodu odhlašovacího IDP** vložte hodnotu **URL pro odhlášení**, kterou jste zkopírovali z Azure Portal.

    d. Do pole **název atributu pro textové pole e-mailu** zadejte tuto hodnotu. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. Do textového pole **název atributu pro křestní jméno** zadejte tuto hodnotu. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. Do textového pole **název atributu pro jméno a příjmení** zadejte tuto hodnotu. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    > [!Note]
    > Tyto hodnoty lze získat kombinováním oboru názvů a hodnot názvu příslušného atributu z oddílu atributy uživatele v Azure Portal.

    například V programu Poznámkový blok otevřete certifikát s kódováním Base-64, který jste stáhli z Azure Portal, zkopírujte jeho obsah (bez počátečních a koncových značek) a pak ho vložte do pole **certifikát IDP X. 509** .

    h. Zaškrtněte **Povolit přihlášení pomocí jednotného přihlašování i Kanbanize**.

    i. Klikněte na **Uložit nastavení**.

### <a name="create-kanbanize-test-user"></a>Vytvořit testovacího uživatele Kanbanize

V této části se v Kanbanize vytvoří uživatel s názvem B. Simon. Kanbanize podporuje zřizování uživatelů za běhu, což je ve výchozím nastavení povolené. V této části není žádná položka akce. Pokud uživatel ještě v Kanbanize neexistuje, vytvoří se po ověření nový. Pokud potřebujete ručně vytvořit uživatele, obraťte se na [tým podpory Kanbanize Client](mailto:support@ms.kanbanize.com).

## <a name="test-sso"></a>Test SSO 

V této části otestujete konfiguraci jednotného přihlašování Azure AD pomocí přístupového panelu.

Když na přístupovém panelu kliknete na dlaždici Kanbanize, měli byste se automaticky přihlásit k Kanbanize, pro které jste nastavili jednotné přihlašování. Další informace o přístupovém panelu najdete v tématu [Úvod do přístupového panelu](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Další zdroje informací

- [ Seznam kurzů pro integraci aplikací SaaS s Azure Active Directory ](./tutorial-list.md)

- [Co je přístup k aplikacím a jednotné přihlašování pomocí Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Co je podmíněný přístup v Azure Active Directory?](../conditional-access/overview.md)

- [Vyzkoušejte si Kanbanize s Azure AD](https://aad.portal.azure.com/)