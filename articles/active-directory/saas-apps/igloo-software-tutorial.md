---
title: 'Kurz: Integrace Azure Active Directory s Igloo softwarem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Igloo softwaru.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 60ec322758a3c3b83e7b24cfba37daaec02fc4c0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60273950"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Kurz: Integrace Azure Active Directory s Igloo softwaru

V tomto kurzu se dozvíte, jak integrovat Igloo Software s Azure Active Directory (Azure AD).
Integrace softwaru Igloo s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k softwaru Igloo.
* Uživatelům se automaticky přihlášeni k softwaru Igloo (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Igloo softwaru, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Igloo softwaru jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Igloo Software podporuje **SP** jednotné přihlašování zahájené pomocí
* Igloo Software podporuje **JIT** zřizování uživatelů

## <a name="adding-igloo-software-from-the-gallery"></a>Přidání Igloo softwaru z Galerie

Konfigurace integrace Igloo softwaru do služby Azure AD, budete muset přidat Igloo Software z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Igloo softwaru z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Igloo softwaru**vyberte **Igloo softwaru** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Software igloo v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Igloo Software založený na uživateli testu **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské Igloo softwaru.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Igloo softwaru, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Igloo softwaru Single Sign-On](#configure-igloo-software-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele softwaru Igloo](#create-igloo-software-test-user)**  – Pokud chcete mít protějšek Britta Simon Igloo Software, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Igloo softwaru, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Igloo softwaru** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Igloo softwaru domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company name>.igloocommmunities.com`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company name>.igloocommmunities.com/saml.digest`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizujte skutečné přihlašovací adresa URL, identifikátoru a adresa URL odpovědi. Kontakt [tým podpory Igloo softwarového klienta](https://www.igloosoftware.com/services/support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení softwaru Igloo** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-igloo-software-single-sign-on"></a>Konfigurace Igloo softwaru jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti Igloo softwaru jako správce.

2. Přejděte **ovládací panely**.

     ![Ovládací panely](./media/igloo-software-tutorial/ic799949.png "ovládací panely")

3. V **členství** klikněte na tlačítko **přihlašování v nastavení**.

    ![Nastavení přihlášení](./media/igloo-software-tutorial/ic783968.png "nastavení přihlášení")

4. V části Konfigurace SAML klikněte na tlačítko **konfigurace ověřování SAML**.

    ![Konfigurace SAML](./media/igloo-software-tutorial/ic783969.png "konfigurace SAML")

5. V **obecné konfigurace** části, proveďte následující kroky:

    ![Obecná konfigurace](./media/igloo-software-tutorial/ic783970.png "Obecná konfigurace")

    a. V **název připojení** textového pole zadejte vlastní název pro vaši konfiguraci.

    b. V **přihlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.

    c. V **odhlašovací adresa URL zprostředkovatele identity** textového pole vložte hodnotu **odhlašovací adresa URL** zkopírovanou z webu Azure portal.

    d. Vyberte **odpovědi na odhlášení a typ požadavku HTTP** jako **příspěvek**.

    e. Otevřete váš **base-64** kódovaného certifikátu v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **veřejný certifikát** textového pole.

6. V **odpovědi a konfigurace ověřování**, proveďte následující kroky:

    ![Odpovědi a konfigurace ověřování](./media/igloo-software-tutorial/IC783971.png "odpovědi a konfigurace ověřování")
  
    a. Jako **zprostředkovatele Identity**vyberte **Microsoft ADFS**.

    b. Jako **typ identifikátoru**vyberte **e-mailovou adresu**. 

    c. V **atribut e-mailové** textové pole, typ **emailaddress**.

    d. V **křestní jméno atributu** textové pole, typ **givenname**.

    e. V **poslední název atributu** textové pole, typ **příjmení**.

7. Proveďte následující kroky k dokončení konfigurace:

    ![Vytvoření uživatele na přihlašovací](./media/igloo-software-tutorial/IC783972.png "vytvoření uživatele na přihlašovací") 

    a. Jako **vytvoření uživatele na přihlašovací**vyberte **vytvořte nového uživatele ve vaší lokalitě při přihlášení**.

    b. Jako **nastavení přihlášení**vyberte **SAML použijte tlačítko na obrazovce "Sign in"**.

    c. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k softwaru Igloo.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Igloo softwaru**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Igloo softwaru**.

    ![Odkaz Igloo Software v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-igloo-software-test-user"></a>Vytvořit Igloo Software testovacího uživatele

Neexistuje žádná položka akce konfigurace zřizování uživatelů pro Igloo softwaru.  

Přiřazený uživatel se pokusí přihlásit k softwaru Igloo pomocí přístupového panelu, Igloo Software kontroluje, zda uživatel existuje.  Pokud není žádný uživatelský účet k dispozici dosud, vytvoří se automaticky Igloo softwarem.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Igloo softwaru na přístupovém panelu, vám by měl být automaticky přihlášeni Igloo software, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)