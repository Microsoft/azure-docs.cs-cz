---
title: 'Kurz: Integrace Azure Active Directory se službou DocuSign | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 72505cfc0a86c00882de37c35dff61a12a9c3fbe
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65899596"
---
# <a name="tutorial-azure-active-directory-integration-with-docusign"></a>Kurz: Integrace Azure Active Directory se službou DocuSign

V tomto kurzu se dozvíte, jak integrovat DocuSign s Azure Active Directory (Azure AD).
Integrace DocuSign s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k DocuSign.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k DocuSign (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD službou DocuSign, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* DocuSign jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje DocuSign **SP** jednotné přihlašování zahájené pomocí

* Podporuje DocuSign **JIT** zřizování uživatelů

## <a name="adding-docusign-from-the-gallery"></a>Přidání DocuSign z Galerie

Konfigurace integrace DocuSign do služby Azure AD, budete muset přidat DocuSign z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat DocuSign z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **DocuSign**vyberte **DocuSign** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![DocuSign v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí DocuSign podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v DocuSign.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování službou DocuSign, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace DocuSign Single Sign-On](#configure-docusign-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele DocuSign](#create-docusign-test-user)**  – Pokud chcete mít protějšek Britta Simon v DocuSign, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování službou DocuSign, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **DocuSign** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![DocuSign domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a identifikátor, který je vysvětlen později **zobrazit SAML 2.0 koncové body** části v tomto kurzu.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení DocuSign** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-docusign-single-sign-on"></a>Konfigurace DocuSign jednotné přihlašování

1. V okně prohlížeče jiných webových, přihlášení k vaší **portál pro správu DocuSign** jako správce.

2. V horní pravé části stránky klikněte na profil **logo** a potom klikněte na **přejděte do správce**.
  
    ![Konfiguruje se jednotné přihlašování.][51]

3. Na stránce řešení domény, klikněte na **domén**

    ![Konfiguruje se jednotné přihlašování.][50]

4. V části **domén** klikněte na tlačítko **domény deklaraci identity**.

    ![Konfiguruje se jednotné přihlašování.][52]

5. Na **deklarace identity domény** dialogového okna v **název domény** textového pole zadejte doménu vaší společnosti a potom klikněte na tlačítko **deklarace identity**. Ujistěte se, že ověření domény a je ve stavu aktivní.

    ![Konfiguruje se jednotné přihlašování.][53]

6. Na stránce řešení domény, klikněte na tlačítko **zprostředkovatelé Identity**.
  
    ![Konfiguruje se jednotné přihlašování.][54]

7. V části **zprostředkovatelé Identity** klikněte na tlačítko **přidat zprostředkovatele IDENTITY**. 

    ![Konfiguruje se jednotné přihlašování.][55]

8. Na **nastavení zprostředkovatele Identity** stránce, proveďte následující kroky:

    ![Konfiguruje se jednotné přihlašování.][56]

    a. V **název** textového pole zadejte jedinečný název pro vaši konfiguraci. Nepoužívejte mezery.

    b. V **textového pole Vystavitel zprostředkovatele Identity**, vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    c. V **přihlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. V **odhlašovací adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Vyberte **ověřovací přihlašovací požadavek**.

    f. Jako **žádost odeslat ověřovací**vyberte **příspěvek**.

    g. Jako **žádost odeslat odhlášení**vyberte **získat**.

    h. V **mapování vlastního atributu** části, klikněte na **přidat nové mapování**.

    ![Konfiguruje se jednotné přihlašování.][62]

    i. Zvolte pole, které chcete propojit s deklarací identity Azure AD. V tomto příkladu **emailaddress** deklarací identity je namapována na žádnou hodnotu **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**. Jedná se o výchozí název deklarace identity z Azure AD pro deklarace identity e-mailu a pak klikněte na tlačítko **Uložit**.

    ![Konfiguruje se jednotné přihlašování.][57]

    > [!NOTE]
    > Použít příslušné **identifikátor uživatele** mapovat uživatele ze služby Azure AD pro mapování uživatele DocuSign. Vyberte správné pole a zadejte příslušnou hodnotu na základě svého nastavení organizace.

    j. V **certifikáty zprostředkovatele Identity** klikněte na tlačítko **přidat certifikát**a pak nahrajte certifikát, který jste si stáhli z portálu Azure AD a klikněte na **Uložit**.

    ![Konfiguruje se jednotné přihlašování.][58]

    k. V **zprostředkovatelé Identity** klikněte na tlačítko **akce**a potom klikněte na tlačítko **koncové body**.

    ![Konfiguruje se jednotné přihlašování.][59]

    l. V **zobrazit SAML 2.0 koncové body** části na **portál pro správu DocuSign**, proveďte následující kroky:

    ![Konfiguruje se jednotné přihlašování.][60]

    * Kopírovat **URL vystavitele poskytovatele služby**a vložte jej do **identifikátor** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    * Kopírovat **přihlašovací adresa URL služby zprostředkovatele**a vložte jej do **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    * Klikněte na tlačítko **zavřít**

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon udělení přístupu k DocuSign používá Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **DocuSign**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **DocuSign**.

    ![DocuSign odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-docusign-test-user"></a>Vytvoření DocuSign testovacího uživatele

V této části se vytvoří uživateli Britta Simon v DocuSign. DocuSign podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi DocuSign, vytvoří se nový po ověření.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory DocuSign](https://support.docusign.com/).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici DocuSign na přístupovém panelu, můžete by měl být automaticky přihlášeni k DocuSign, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png
