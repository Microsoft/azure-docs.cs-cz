---
title: 'Kurz: Integrace Azure Active Directory s Jitbit helpdesku | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Jitbit Helpdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 851b28d10bdf0b2df67e1c0782a683e790b711bc
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59266490"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Kurz: Integrace Azure Active Directory s Jitbit helpdesku

V tomto kurzu se dozvíte, jak integrovat Jitbit helpdesku se službou Azure Active Directory (Azure AD).
Integrace s Azure AD Jitbit helpdesku poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Jitbit Helpdesk.
* Uživatelům se automaticky přihlášeni na technickou podporu Jitbit (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Jitbit technickou podporu, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Jitbit Helpdesk jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Jitbit helpdesku **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Přidání Jitbit helpdesku z Galerie

Ke konfiguraci integrace Jitbit helpdesku do služby Azure AD, budete muset přidat Jitbit helpdesku na váš seznam spravovaných aplikací SaaS z galerie.

**Přidat technickou podporu Jitbit z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Jitbit helpdesku**vyberte **Jitbit helpdesku** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Jitbit Helpdesk v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s Jitbit helpdesku na základě testu uživatelem volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Jitbit Helpdesk.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Jitbit technické podpory, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Jitbit helpdesku Single Sign-On](#configure-jitbit-helpdesk-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Jitbit helpdesku](#create-jitbit-helpdesk-test-user)**  – Pokud chcete mít protějšek Britta Simon Jitbit technické podpory, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Jitbit helpdesku, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Jitbit helpdesku** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Domény Jitbit technickou podporu a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:
    | |
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Tato hodnota není skutečný. Aktualizujte tuto hodnotu skutečné přihlašovací adresa URL. Kontakt [tým podpory helpdesku klienta Jitbit](https://www.jitbit.com/support/) tuto výhodu získáte.

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL následujícím způsobem: `https://www.jitbit.com/web-helpdesk/`

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. V **nastavení Jitbit helpdesku** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-jitbit-helpdesk-single-sign-on"></a>Konfigurace Jitbit technickou podporu jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu Jitbit Helpdesk společnosti jako správce.

1. Na panelu nástrojů v horní části klikněte na tlačítko **správu**.

    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "správy")

1. Klikněte na tlačítko **obecné nastavení**.

    ![Uživatelé, společností a oprávnění](./media/jitbit-helpdesk-tutorial/ic777680.png "uživatele, společností a oprávnění")

1. V **nastavení ověřování** konfigurační oddíl, proveďte následující kroky:

    ![Nastavení ověřování](./media/jitbit-helpdesk-tutorial/ic777683.png "nastavení ověřování")

    a. Vyberte **povolit SAML 2.0 jednotné přihlášení**, přihlaste se pomocí jednotné přihlašování (SSO), s **OneLogin**.

    b. V **adresu URL koncového bodu** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. Otevřete váš **base-64** kódování certifikátu v programu Poznámkový blok, zkopírujte obsah ho do schránky a vložte ho do **certifikát X.509** textové pole

    d. Klikněte na tlačítko **uložit změny**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Jitbit Helpdesk.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Jitbit helpdesku**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Jitbit helpdesku**.

    ![Jitbit Helpdesk odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-jitbit-helpdesk-test-user"></a>Vytvořit testovacího uživatele Jitbit helpdesku

Chcete-li povolit Azure AD uživatelům umožní přihlásit se na technickou podporu Jitbit, musí být poskytnuty do Jitbit Helpdesk. V případě Jitbit helpdesku zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Jitbit helpdesku** tenanta.

1. V nabídce v horní části klikněte na tlačítko **správu**.

    ![Správa](./media/jitbit-helpdesk-tutorial/ic777681.png "správy")

1. Klikněte na tlačítko **uživatelů, firmy a oprávnění**.

    ![Uživatelé, společností a oprávnění](./media/jitbit-helpdesk-tutorial/ic777682.png "uživatele, společností a oprávnění")

1. Klikněte na **Přidat uživatele**.

    ![Přidat uživatele](./media/jitbit-helpdesk-tutorial/ic777685.png "přidat uživatele")

1. V části Vytvoření zadejte údaje účtu služby Azure AD, které chcete zřídit následujícím způsobem:

    ![Vytvoření](./media/jitbit-helpdesk-tutorial/ic777686.png "vytvořit")

   a. V **uživatelské jméno** , jako je textové pole, zadejte uživatelské jméno uživatele **BrittaSimon**.

   b. V **e-mailu** , jako je textové pole, typ e-mailu uživatele **BrittaSimon@contoso.com**.

   c. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

   d. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

   e. Klikněte na možnost **Vytvořit**.

> [!NOTE]
> Další nástroje pro tvorbu účtu uživatele Jitbit helpdesku nebo rozhraní API poskytovaných Jitbit helpdesku můžete použít ke zřízení uživatelských účtů služby Azure AD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Jitbit helpdesku na přístupovém panelu, vám by měl být automaticky přihlášeni na technickou podporu Jitbit, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
