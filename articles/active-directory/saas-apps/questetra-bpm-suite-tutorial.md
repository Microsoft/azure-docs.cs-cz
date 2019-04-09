---
title: 'Kurz: Integrace Azure Active Directory s Questetra BPM Suite | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Questetra BPM Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fb6d5b73-e491-4dd2-92d6-94e5aba21465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 33c2d211fad16a81a307a5c0f2a9d048ef07bf4d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259894"
---
# <a name="tutorial-azure-active-directory-integration-with-questetra-bpm-suite"></a>Kurz: Integrace Azure Active Directory s Questetra BPM Suite

V tomto kurzu se dozvíte, jak integrovat Questetra BPM Suite s Azure Active Directory (Azure AD).
Integrace Questetra BPM Suite s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k sadě BPM Questetra.
* Uživatelům se automaticky přihlášeni k sadě BPM Questetra (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Questetra BPM sady, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Sada BPM Questetra jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje sadu BPM Questetra **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-questetra-bpm-suite-from-the-gallery"></a>Přidání sady BPM Questetra z Galerie

Pokud chcete nakonfigurovat integraci sady BPM Questetra do služby Azure AD, budete muset přidat Questetra BPM Suite z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidat sadu BPM Questetra z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Questetra BPM Suite**vyberte **Questetra BPM Suite** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Sada BPM Questetra v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotného přihlašování se sadou Questetra BPM na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Questetra BPM Suite.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Questetra BPM sady, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Questetra BPM sady Single Sign-On](#configure-questetra-bpm-suite-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Questetra BPM Suite](#create-questetra-bpm-suite-test-user)**  – Pokud chcete mít protějšek Britta Simon BPM sady Questetra, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí Questetra BPM Suite, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Questetra BPM Suite** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Questetra BPM sada domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.questetra.net/`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte s skutečné přihlašovací adresu URL a identifikátorem. Můžete získat tyto hodnoty z **SP informace** části na vaše **Questetra BPM Suite** společnosti webu, který je vysvětlen později v kurzu nebo kontaktujte [podpora Questetra BPM Suite klientů tým](https://www.questetra.com/contact/). Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Questetra BPM Suite** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-questetra-bpm-suite-single-sign-on"></a>Konfigurace řízení Questetra Suite jednotného přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **Questetra BPM Suite** společnosti serveru jako správce.

2. V nabídce v horní části klikněte na tlačítko **nastavení systému**. 
   
    ![Azure AD jednotné přihlašování][10]

3. Chcete-li otevřít **SingleSignOnSAML** klikněte na **jednotné přihlašování (SAML)**. 
   
    ![Azure AD jednotné přihlašování][11]

4. Na vaše **Questetra BPM Suite** společnosti v lokalitě, **SP informace** části, proveďte následující kroky:

    a. Kopírovat **ACS URL**a vložte jej do **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** části webu Azure Portal.
    
    b. Kopírovat **Entity ID**a vložte jej do **identifikátor** textového pole v **základní konfiguraci SAML** části webu Azure Portal.

5. Na vaše **Questetra BPM Suite** společnosti serveru, proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování][15]
   
    a. Vyberte **povolit jednotné přihlašování**.
   
    b. V **Entity ID** textového pole vložte hodnotu **Azure AD identifikátor** zkopírovanou z webu Azure portal.
    
    c. V **přihlašovací adresa URL stránky** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
    
    d. V **adresy URL odhlašovací stránky** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.
    
    e. V **formátem** textové pole, typ `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`.

    f. Otevřete váš **Base-64** kódovaného certifikátu v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte jej do **ověření certifikátu** textového pole. 

    g. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k sadě BPM Questetra.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Questetra BPM Suite**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Questetra BPM Suite**.

    ![Odkaz Questetra BPM Suite v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-questetra-bpm-suite-test-user"></a>Vytvoření sady BPM Questetra testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon Questetra BPM Suite.

**Vytvořte uživatele v sadě BPM Questetra volá Britta Simon, proveďte následující kroky:**

1. Přihlaste se k webu společnosti Questetra BPM Suite jako správce.

2. Přejděte na **nastavení systému > seznam uživatelů > Nový uživatel**.
 
3. V dialogovém okně Nový uživatel proveďte následující kroky: 
   
    ![Vytvořit testovacího uživatele][300] 
   
    a. V **název** textové pole, typ **název** uživatele britta.simon@contoso.com.
   
    b. V **e-mailu** textové pole, typ **e-mailu** uživatele britta.simon@contoso.com.
   
    c. V **heslo** textového pole zadejte **heslo** uživatele.
    
    d. Klikněte na tlačítko **přidat nového uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Questetra BPM Suite na přístupovém panelu, můžete by měl být automaticky přihlášeni k sadě BPM Questetra, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_03.png
[11]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_04.png
[15]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_08.png
[300]: ./media/questetra-bpm-suite-tutorial/questera_bpm_suite_11.png