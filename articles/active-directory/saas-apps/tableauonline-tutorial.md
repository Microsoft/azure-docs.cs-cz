---
title: 'Kurz: Integrace Azure Active Directory s Tableau Online | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a tableau. představují Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: jeedes
ms.openlocfilehash: 5ccf978ab33226dc029d534a343a87a796ab69e8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59278101"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Kurz: Integrace Azure Active Directory s Tableau Online

V tomto kurzu se dozvíte, jak integrovat Tableau Online se službou Azure Active Directory (Azure AD).
Integrace Tableau Online s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Tableau Online.
* Uživatelům se automaticky přihlášeni k Tableau Online (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tableau Online, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Tableau Online jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Tableau Online podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-tableau-online-from-the-gallery"></a>Přidání Tableau Online z Galerie

Konfigurace integrace Tableau Online do služby Azure AD, budete muset přidat Tableau Online z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Tableau Online z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Tableau Online**vyberte **Tableau Online** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Tableau Online v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Tableau Online na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Tableau Online.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau Online, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Tableau Online Single Sign-On](#configure-tableau-online-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření Tableau Online testovacího uživatele](#create-tableau-online-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tableau Online, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Tableau Online, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **Tableau Online** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Tableau Online domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`

    > [!NOTE]
    > Zobrazí se `<entityid>` hodnotu **nastavení Tableau Online** části v tomto kurzu. Hodnota ID entity budou **identifikátor služby Azure AD** hodnota v **nastavení Tableau Online** oddílu.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavení Tableau Online** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-tableau-online-single-sign-on"></a>Konfigurace Tableau Online jednotného přihlašování

1. V jiném okně prohlížeče přihlašování k aplikaci Tableau Online. Přejděte na **nastavení** a potom **ověřování**.

    ![Konfigurace jednotného přihlašování](./media/tableauonline-tutorial/tutorial_tableauonline_09.png)

2. V části Povolit SAML, **typy ověřování** oddílu. Zkontrolujte **povolit dodatečné metody ověřování** a zkontrolujte **SAML** zaškrtávací políčko.

    ![Konfigurace jednotného přihlašování](./media/tableauonline-tutorial/tutorial_tableauonline_12.png)

3. Posuňte se dolů až **importovat soubor metadat do režimu Online Tableau** oddílu.  Klikněte na tlačítko Procházet a importovat soubor metadat, který jste si stáhli z Azure AD. Potom klikněte na **použít**.

   ![Konfigurace jednotného přihlašování](./media/tableauonline-tutorial/tutorial_tableauonline_13.png)

4. V **odpovídat kontrolní výrazy** části, vložte odpovídající název kontrolního výrazu zprostředkovatele Identity pro **e-mailová adresa**, **křestní jméno**, a **příjmení**. Pokud chcete získat tyto informace ze služby Azure AD: 
  
    a. Na webu Azure Portal, přejděte **Tableau Online** stránky integrace aplikace.

    b. V ** atributy uživatele a deklarace identity *** oddíl, klikněte na ikonu pro úpravy.

   ![Konfigurace jednotného přihlašování](./media/tableauonline-tutorial/attributesection.png)

    c. Zkopírujte hodnotu oboru názvů pro tyto atributy: jméno, e-mailu a příjmení s použitím následujících kroků:

   ![Azure AD jednotné přihlašování](./media/tableauonline-tutorial/tutorial_tableauonline_10.png)

    d. Klikněte na tlačítko **user.givenname** hodnota

    e. Zkopírujte hodnotu z **Namespace** textového pole.

    ![Konfigurace jednotného přihlašování](./media/tableauonline-tutorial/attributesection2.png)

    f. Kopírování obor názvů hodnoty pro e-mailu a příjmení opakujte předchozí postup.

    g. Přepnout do režimu Online Tableau aplikace a pak nastavit **atributy uživatele a deklarace identity** části následujícím způsobem:

    * E-mailu: **e-mailu** nebo **userprincipalname**

    * Křestní jméno: **givenname**

    * Příjmení: **příjmení**

    ![Konfigurace jednotného přihlašování](./media/tableauonline-tutorial/tutorial_tableauonline_14.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Tableau Online.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Tableau Online**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Tableau Online**.

    ![Tableau Online odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-tableau-online-test-user"></a>Vytvoření Tableau Online testovacího uživatele

V této části vytvořte uživatele Britta Simon v Tableau Online.

1. Na **Tableau Online**, klikněte na tlačítko **nastavení** a potom **ověřování** oddílu. Přejděte dolů k položce **spravovat uživatele** oddílu. Klikněte na tlačítko **Add Users** a potom klikněte na tlačítko **zadejte e-mailové adresy**.
  
    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_15.png)

2. Vyberte **přidat uživatele pro ověřování (SAML)**. V **zadejte e-mailové adresy** přidat textové pole britta.simon@contoso.com
  
    ![Vytváří se testovací uživatele služby Azure AD](./media/tableauonline-tutorial/tutorial_tableauonline_11.png)

3. Klikněte na tlačítko **přidat uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Tableau Online na přístupovém panelu, můžete by měl být automaticky přihlášeni u kterého nastavíte jednotné přihlašování online Tableau. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
