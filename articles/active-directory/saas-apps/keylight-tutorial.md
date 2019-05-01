---
title: 'Kurz: Integrace Azure Active Directory s LockPath Keylight | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9757588b7adb4032600113d2ac948097e8df6c2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717458"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Kurz: Integrace Azure Active Directory s LockPath Keylight

V tomto kurzu se dozvíte, jak integrovat LockPath Keylight s Azure Active Directory (Azure AD).
LockPath Keylight integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k LockPath Keylight.
* Uživatelům se automaticky přihlášeni k LockPath Keylight (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s LockPath Keylight, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* LockPath Keylight jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje LockPath Keylight **SP** jednotné přihlašování zahájené pomocí
* Podporuje LockPath Keylight **JIT** zřizování uživatelů

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Přidání LockPath Keylight z Galerie

Konfigurace integrace LockPath Keylight do služby Azure AD, budete muset přidat LockPath Keylight z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LockPath Keylight z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LockPath Keylight**vyberte **LockPath Keylight** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![LockPath Keylight v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a test Azure AD jednotné přihlašování s LockPath Keylight podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v LockPath Keylight.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s LockPath Keylight, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace LockPath Keylight Single Sign-On](#configure-lockpath-keylight-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele LockPath Keylight](#create-lockpath-keylight-test-user)**  – Pokud chcete mít protějšek Britta Simon LockPath Keylight, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s LockPath Keylight, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **LockPath Keylight** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![LockPath Keylight domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company name>.keylightgrc.com/`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company name>.keylightgrc.com`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory LockPath Keylight klienta](https://www.lockpath.com/contact/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Na **nastavení LockPath Keylight** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-lockpath-keylight-single-sign-on"></a>Konfigurace LockPath Keylight jednotného přihlašování

1. Pokud chcete povolit jednotné přihlašování v LockPath Keylight, proveďte následující kroky:

    a. Přihlášení ke svému účtu LockPath Keylight jako správce.

    b. V nabídce v horní části klikněte na tlačítko **osoba**a vyberte **Keylight nastavení**.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/401.png)

    c. Ve stromovém zobrazení na levé straně klikněte na tlačítko **SAML**.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/402.png)

    d. Na **nastavení SAML** dialogového okna, klikněte na tlačítko **upravit**.

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/404.png)

1. Na **upravit nastavení SAML** dialogového okna stránky, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/keylight-tutorial/405.png)

    a. Nastavte **ověřování SAML** k **aktivní**.

    b. V **přihlašovací adresa URL zprostředkovatele Identity** vložit do textového pole **přihlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    c. V **odhlašovací adresa URL zprostředkovatele Identity** vložit do textového pole **odhlašovací adresa URL** hodnotu, která jste zkopírovali z portálu Azure portal.

    d. Klikněte na tlačítko **zvolit soubor** vyberte stažený certifikát LockPath Keylight a potom klikněte na **otevřít** na kterou certifikát nahrajete.

    e. Nastavte **místo Id uživatele SAML** k **NameIdentifier element příkazu subjektu**.

    f. Zadejte **poskytovatele služeb Keylight** pomocí následujícímu vzoru: `https://<CompanyName>.keylightgrc.com`.

    g. Nastavte **automatické zřizování uživatelů** k **aktivní**.

    h. Nastavte **typ účtu automatické zřizování** k **úplné uživatelské**.

    i. Nastavte **role zabezpečení automatické zřizování**vyberte **standardního uživatele pomocí SAML**.

    j. Nastavte **konfigurace zabezpečení automatické zřizování**vyberte **standardní konfigurace uživatele**.

    k. V **atribut e-mailové** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    l. V **křestní jméno atributu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    m. V **poslední název atributu** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    n. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k LockPath Keylight.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **LockPath Keylight**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **LockPath Keylight**.

    ![Odkaz LockPath Keylight v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-lockpath-keylight-test-user"></a>Vytvoření LockPath Keylight testovacího uživatele

V této části se vytvoří uživateli Britta Simon v LockPath Keylight. LockPath Keylight podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi LockPath Keylight, vytvoří se nový po ověření. Pokud je potřeba ručně vytvořit uživatele, budete muset požádat [tým podpory LockPath Keylight klienta](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici LockPath Keylight na přístupovém panelu, vám by měl být automaticky přihlášeni ke LockPath Keylight, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)