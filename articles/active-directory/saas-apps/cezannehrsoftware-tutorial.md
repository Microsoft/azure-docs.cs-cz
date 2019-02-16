---
title: 'Kurz: Integrace Azure Active Directory s Cezanne HR softwarem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cezanne HR softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62b42e15-c282-492d-823a-a7c1c539f2cc
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac777eebeece7cd67126a639c45e5cf6665f7a9b
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56313054"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Kurz: Integrace Azure Active Directory s Cezanne HR softwaru

V tomto kurzu se dozvíte, jak integrovat Cezanne HR Software s Azure Active Directory (Azure AD).
Integrace softwaru Cezanne HR s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k softwaru Cezanne HR.
* Uživatelům se automaticky přihlášeni k softwaru HR Cezanne (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cezanne HR softwaru, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Cezanne HR Software jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Cezanne HR Software podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-cezanne-hr-software-from-the-gallery"></a>Přidání Cezanne HR softwaru z Galerie

Konfigurace integrace Cezanne HR softwaru do služby Azure AD, budete muset přidat Cezanne HR Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidání Cezanne HR softwaru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cezanne HR softwaru**vyberte **Cezanne HR softwaru** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Software HR Cezanne v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Cezanne HR Software založený na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Cezanne HR softwaru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cezanne HR softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Cezanne HR softwaru Single Sign-On](#configure-cezanne-hr-software-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele HR softwaru Cezanne](#create-cezanne-hr-software-test-user)**  – Pokud chcete mít protějšek Britta Simon Cezanne HR Software, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Cezanne HR softwaru, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Cezanne HR softwaru** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Cezanne HR softwaru domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL: `https://w3.cezanneondemand.com/CezanneOnDemand/`

    c. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací adresu URL a adresy URL odpovědi. Kontakt [tým podpory Cezanne HR softwarového klienta](https://cezannehr.com/services/support/) k získání těchto hodnot.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení softwaru HR Cezanne** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-cezanne-hr-software-single-sign-on"></a>Konfigurace Cezanne HR softwaru jednotného přihlašování

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi Cezanne HR softwaru jako správce.

2. V levém navigačním podokně klikněte na tlačítko **nastavení systému**. Přejděte na **nastavení zabezpečení**. Přejděte na **Konfigurace jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_000.png)

3. V **umožnit uživatelům přihlášení pomocí následující jednotné přihlašování (SSO) služby** panel, zkontrolujte **SAML 2.0** a vyberte **Upřesnit konfiguraci** možnost.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_001.png)

4. Klikněte na tlačítko **přidat nový** tlačítko.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_002.png)

5. Následující postup proveďte **poskytovatele IDENTITY SAML 2.0** oddílu.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_003.png)

    a. Zadejte název vašeho zprostředkovatele Identity, jako **zobrazovaný název**.

    b. V **identifikátor Entity** textového pole vložte hodnotu **Azure Ad identifikátor** který jste zkopírovali z portálu Azure portal.

    c. Změnit **SAML vazby** na "POST".

    d. V **koncový bod služby tokenu zabezpečení** textového pole vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali z portálu Azure portal.

    e. Do textového pole Název atributu ID uživatele, zadejte `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    f. Klikněte na tlačítko **nahrát** ikonu a nahrát na server certifikát stažený z webu Azure portal.

    g. Klikněte na tlačítko **OK**.

6. Klikněte na tlačítko **Uložit** tlačítko.

    ![Nakonfigurovat jednotné přihlašování v aplikaci na straně](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_004.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k softwaru Cezanne HR.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Cezanne HR softwaru**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Cezanne HR softwaru**.

    ![Odkaz Cezanne HR Software v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-cezanne-hr-software-test-user"></a>Vytvořit Cezanne HR Software testovacího uživatele

Chcete-li povolit uživatele Azure AD k přihlášení do Cezanne HR softwaru, musí být poskytnuty do Cezanne HR softwaru. V případě Cezanne HR softwaru je zřizování úlohu.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se ke serveru vaší společnosti Cezanne HR softwaru jako správce.

2. V levém navigačním podokně klikněte na tlačítko **nastavení systému**. Přejděte na **spravovat uživatele**. Přejděte na **Add New User**.

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_005.png "nového uživatele")

3. Na **podrobnosti o osobě** části, proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_006.png "nového uživatele")

    a. Nastavte **interního uživatele** jako OFF.

    b. V **křestní jméno** , jako je textové pole, typ křestní jméno uživatele **Britta**.  

    c. V **příjmení** , jako je textové pole, typ příjmení uživatele **Simon**.

    d. V **e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

4. Na **informace o účtu** části, proveďte následující kroky:

    ![Nový uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_007.png "nového uživatele")

    a. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    b. V **heslo** textového pole zadejte heslo uživatele.

    c. Vyberte **HR Professional** jako **Role zabezpečení**.

    d. Klikněte na **OK**.

5. Přejděte do **Single Sign-On** kartě a vyberte **přidat nový** v **SAML 2.0 identifikátory** oblasti.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_008.png "uživatele")

6. Vyberte zprostředkovatele Identity pro **zprostředkovatele Identity** a v textovém poli **identifikátor uživatele**, zadejte e-mailovou adresu účtu Britta Simon.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_009.png "uživatele")

7. Klikněte na tlačítko **Uložit** tlačítko.

    ![Uživatel](./media/cezannehrsoftware-tutorial/tutorial_cezannehrsoftware_010.png "uživatele")

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Cezanne HR softwaru na přístupovém panelu, vám by měl být automaticky přihlášeni softwaru HR Cezanne, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
