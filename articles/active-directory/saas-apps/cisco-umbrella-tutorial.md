---
title: 'Kurz: Integrace Azure Active Directory s Cisco zastřešující | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zastřešující Cisco.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 675dca98-f119-4463-8350-d6a45d5601e3
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: f43c3072660ce4b9ca68c2bc58d1c752ce474e28
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54821903"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella"></a>Kurz: Integrace Azure Active Directory s zastřešující Cisco

V tomto kurzu se dozvíte, jak integrovat zastřešující Cisco s Azure Active Directory (Azure AD).
Integrace Cisco zastřešující s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k zastřešující Cisco.
* Uživatelům se automaticky přihlášeni k zastřešující Cisco (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cisco zastřešující, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Cisco zastřešující jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Cisco zastřešující **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-cisco-umbrella-from-the-gallery"></a>Přidání Cisco zastřešující z Galerie

Konfigurace integrace Cisco zastřešující do služby Azure AD, budete muset přidat zastřešující Cisco z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat zastřešující Cisco z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cisco zastřešující**vyberte **Cisco zastřešující** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Cisco zastřešující v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s [název aplikace] podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v [název aplikace].

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s [název aplikace], které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Cisco zastřešující Single Sign-On](#configure-cisco-umbrella-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Cisco zastřešující](#create-cisco-umbrella-test-user)**  – Pokud chcete mít protějšek Britta Simon Cisco zastřešující, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s [název aplikace], proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Cisco zastřešující** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![Cisco zastřešující domény a adresy URL jednotného přihlašování – informace](common/both-preintegrated-signon.png)

    a. Pokud chcete nakonfigurovat aplikace v **SP** intiated režimu, proveďte následující kroky:

    b. Klikněte na tlačítko **nastavit další adresy URL**.

    c. V **přihlašovací adresa URL** textového pole zadejte adresu URL: `https://login.umbrella.com/sso`

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **soubor XML s metadaty**z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Cisco zastřešující** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-cisco-umbrella-single-sign-on"></a>Konfigurace Cisco zastřešující jednotného přihlašování

1. V jiném okně prohlížeče přihlašování k webu společnosti Cisco zastřešující jako správce.

2. V levé nabídce klikněte na **správce** a přejděte do **ověřování** a potom klikněte na **SAML**.

    ![Správce](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_admin.png)

3. Zvolte **jiných** a klikněte na **Další**.

    ![Druhá](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_other.png)

4. Na **Cisco zastřešující metadat**, stránky, klikněte na tlačítko **Další**.

    ![Metadata](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_metadata.png)

5. Na **nahrát metadat** kartu, pokud předem nakonfiguroval SAML, vyberte **jejich změny najdete tady** možnost a postupujte podle pokynů následujících kroků.

    ![Na další](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_next.png)

6. V **možnost A: Nahrajte soubor XML**, nahrajte **kód XML metadat federace** soubor, který jste si stáhli z portálu Azure portal a po nahrání metadat následujících hodnot získat automaticky vyplní automaticky a potom klikněte na **Další**.

    ![Choosefile ](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_choosefile.png)

7. V části **ověřit konfiguraci SAML** klikněte na tlačítko **SAML Konfigurace testu**.

    ![Test](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_test.png)

8. Klikněte na **ULOŽIT**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k zastřešující Cisco.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Cisco zastřešující**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **Cisco zastřešující**.

    ![Cisco zastřešující odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-cisco-umbrella-test-user"></a>Vytvořit testovacího uživatele zastřešující Cisco

Povolit uživatele Azure AD se přihlaste k zastřešující Cisco, musí být poskytnuty do zastřešující Cisco.  
V případě Cisco zastřešující zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. V jiném okně prohlížeče přihlašování k webu společnosti Cisco zastřešující jako správce.

2. V levé nabídce klikněte na **správce** a přejděte do **účty**.

    ![Účet](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_account.png)

3. Na **účty** stránky, klikněte na **přidat** v horní pravé části stránky a proveďte následující kroky.

    ![Uživatel](./media/cisco-umbrella-tutorial/tutorial_cisco-umbrella_createuser.png)

    a. V **křestní jméno** zadejte jméno jako je **Britta**.

    b. V **příjmení** zadejte příjmení jako **simon**.

    c. Z **zvolte Role delegovaný správce**, vyberte svou roli.
  
    d. V **e-mailovou adresu** zadejte emailaddress uživatele jako **brittasimon@contoso.com**.

    e. V **heslo** pole, zadejte své heslo.

    f. V **Potvrdit heslo** pole, zadejte heslo znovu.

    g. Klikněte na tlačítko **vytvořit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Cisco zastřešující na přístupovém panelu, vám by měl být automaticky přihlášeni ke zastřešující Cisco, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)