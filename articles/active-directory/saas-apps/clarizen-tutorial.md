---
title: 'Kurz: Integrace Azure Active Directory s Clarizen | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Clarizen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/21/2019
ms.author: jeedes
ms.openlocfilehash: f4c7efdb0a43c352450056a9f6f79e3e189c820c
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458308"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen"></a>Kurz: Integrace Azure Active Directory s Clarizen

V tomto kurzu se dozvíte, jak integrovat Clarizen s Azure Active Directory (Azure AD).
Clarizen integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Clarizen.
* Můžete povolit uživatelům být automaticky přihlášeni k Clarizen (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Clarizen, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Clarizen jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Clarizen **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-clarizen-from-the-gallery"></a>Přidání Clarizen z Galerie

Konfigurace integrace Clarizen do služby Azure AD, budete muset přidat Clarizen z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Clarizen z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Clarizen**vyberte **Clarizen** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Clarizen v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Clarizen podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Clarizen.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Clarizen, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Clarizen Single Sign-On](#configure-clarizen-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Clarizen](#create-clarizen-test-user)**  – Pokud chcete mít protějšek Britta Simon Clarizen, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Clarizen, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Clarizen** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Clarizen domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textového pole zadejte hodnotu: `Clarizen`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`

    > [!NOTE]
    > Ty nejsou skutečné hodnoty. Je nutné použít skutečné identifikátor a adresa URL odpovědi. Tady doporučujeme použít jedinečnou hodnotu řetězce jako identifikátor. Abyste získali skutečné hodnoty, obraťte se [tým podpory Clarizen](https://success.clarizen.com/hc/en-us/requests/new).

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení Clarizen** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-clarizen-single-sign-on"></a>Konfigurace Clarizen jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Clarizen jako správce.

1. Klikněte na své uživatelské jméno a potom klikněte na tlačítko **nastavení**.

    ![Kliknutím na "Nastavení" pod vaším uživatelským jménem](./media/clarizen-tutorial/tutorial_clarizen_001.png "nastavení")

1. Klikněte na tlačítko **globální nastavení** kartu. Potom vedle **federovaného ověření**, klikněte na tlačítko **upravit**.

    ![Karta "Globální nastavení"](./media/clarizen-tutorial/tutorial_clarizen_002.png "globální nastavení")

1. V **federovaného ověření** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno "Federované ověřování"](./media/clarizen-tutorial/tutorial_clarizen_003.png "federovaného ověření")

    a. Vyberte **povolit federované ověřování**.

    b. Klikněte na tlačítko **nahrát** na stažený certifikát nahrajete.

    c. V **přihlašovací adresa URL** zadejte hodnotu **přihlašovací adresa URL** v okně Konfigurace aplikací služby Azure AD.

    d. V **odhlašování URL** zadejte hodnotu **odhlašovací adresa URL** v okně Konfigurace aplikací služby Azure AD.

    e. Vyberte **použít příspěvek**.

    f. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon k udělení přístupu k Clarizen použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Clarizen**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Clarizen**.

    ![Odkaz Clarizen v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-clarizen-test-user"></a>Vytvoření Clarizen testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Clarizen.

**Pokud je potřeba ručně vytvořit uživatele, proveďte následující kroky:**

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k Clarizen, je třeba zřídit uživatelské účty. V případě Clarizen zřizování se ruční úlohy.

1. Přihlaste se k webu společnosti Clarizen jako správce.

2. Klikněte na tlačítko **lidé**.

    ![Kliknutím na "Lidé"](./media/clarizen-tutorial/create_aaduser_001.png "osoby")

3. Klikněte na tlačítko **pozvat uživatele**.

    ![Tlačítko "Pozvat uživatele"](./media/clarizen-tutorial/create_aaduser_002.png "pozvat uživatele")

1. V **pozvat lidé** dialogové okno pole, proveďte následující kroky:

    ![Dialogové okno "Pozvat"](./media/clarizen-tutorial/create_aaduser_003.png "pozvání uživatelů")

    a. V **e-mailu** zadejte e-mailovou adresu účtu Britta Simon.

    b. Klikněte na tlačítko **pozvat**.

    > [!NOTE]
    > Držitel účtu Azure Active Directory bude dostávat e-mailu a odkaz potvrďte svůj účet, pak se změní na aktivní.


### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Clarizen na přístupovém panelu, můžete by měl být automaticky přihlášeni k Clarizen, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
