---
title: 'Kurz: Integrace Azure Active Directory s adaptivní Insights | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a adaptivní Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2c7ca8699274b96f7f382dfe1958bf5babbbe99
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720164"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Kurz: Integrace Azure Active Directory s adaptivní Insights

V tomto kurzu se dozvíte, jak integrovat adaptivní Insights s Azure Active Directory (Azure AD).
Integrace adaptivní Insights s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k přehledům adaptivní.
* Uživatelům se automaticky přihlášeni k adaptivní Insights (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s adaptivní přehledy, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Adaptivní Insights jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Adaptivní Insights podporuje **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-adaptive-insights-from-the-gallery"></a>Přidání adaptivní Insights z Galerie

Pokud chcete nakonfigurovat integraci adaptivní Insights do služby Azure AD, budete muset adaptivní Insights přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Adaptivní Insights přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **adaptivní Insights**vyberte **adaptivní Insights** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Adaptivní Insights v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s adaptivní přehledy na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v adaptivní Insights.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s adaptivní přehledy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace adaptivní Insights Single Sign-On](#configure-adaptive-insights-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele adaptivní Insights](#create-adaptive-insights-test-user)**  – Pokud chcete mít protějšek Britta Simon adaptivní Insights, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí adaptivního Insights, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **adaptivní Insights** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Adaptivní Insights domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > Adaptivní insights můžete získat identifikátor (Entity ID) a hodnoty adresy URL odpovědi **nastavení jednotného přihlašování SAML** stránky.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení adaptivní Insights** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-adaptive-insights-single-sign-on"></a>Konfigurace adaptivní Insights jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti adaptivní Insights jako správce.

2. Přejděte na **správce**.

    ![Správce](./media/adaptivesuite-tutorial/ic805644.png "správce")

3. V **uživatelů a rolí** klikněte na tlačítko **spravovat nastavení jednotného přihlašování SAML**.

    ![Správa nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/ic805645.png "spravovat nastavení jednotného přihlašování SAML")

4. Na **nastavení jednotného přihlašování SAML** stránce, proveďte následující kroky:

    ![Nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/ic805646.png "nastavení jednotného přihlašování SAML")

    a. V **název zprostředkovatele identit** textového pole zadejte název pro vaši konfiguraci.

    b. Vložit **Azure Ad identifikátor** hodnota zkopírována z webu Azure portal do **zprostředkovatele Identity Entity ID** textového pole.

    c. Vložit **přihlašovací adresa URL** hodnota zkopírována z webu Azure portal do **zprostředkovatele Identity adresu URL jednotného přihlašování** textového pole.

    d. Vložit **odhlašovací adresa URL** hodnota zkopírována z webu Azure portal do **odhlašovací adresa URL vlastní** textového pole.

    e. Pokud chcete uložit stažený certifikát, klikněte na tlačítko **zvolte soubor**.

    f. Vyberte pro následující:

     * **Id uživatele SAML**vyberte **adaptivní Insights uživatelské jméno**.

     * **Umístění id uživatele SAML**vyberte **id uživatele v předmětu NameID**.

     * **Formátem ID názvu SAML**vyberte **e-mailová adresa**.

     * **Povolit SAML**vyberte **povolit jednotné přihlašování SAML a přímé přihlášení adaptivní Insights**.

    g. Kopírování **adaptivní adresu URL jednotného přihlašování Insights** a vložte do **identifikátor (Entity ID)** a **adresy URL odpovědi** textová pole v **adaptivní Insights domény a adresy URL** části webu Azure Portal.

    h. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole 'brittasimon@yourcompanydomain.extension. Například, BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tak, že udělíte přístup k přehledům adaptivní.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **adaptivní Insights**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **adaptivní Insights**.

    ![Adaptivní Insights odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-adaptive-insights-test-user"></a>Vytvořit testovacího uživatele adaptivní Insights

Pokud chcete povolit Azure AD uživatelům umožní přihlásit k přehledům Adaptivní, musí být zřízená na adaptivní přehledy. V případě adaptivní Insights zřizování je ruční úloha.

**Konfigurace zřizování uživatelů, proveďte následující kroky:**

1. Přihlaste se k vaší **adaptivní Insights** společnosti serveru jako správce.

2. Přejděte na **správce**.

   ![Správce](./media/adaptivesuite-tutorial/IC805644.png "správce")

3. V **uživatelů a rolí** klikněte na tlačítko **přidat uživatele**.

   ![Přidání uživatele](./media/adaptivesuite-tutorial/IC805648.png "přidat uživatele")

4. V **nového uživatele** části, proveďte následující kroky:

   ![Odeslat](./media/adaptivesuite-tutorial/IC805649.png "odeslání")

   a. Typ **název**, **přihlášení**, **e-mailu**, **heslo** platného uživatele Azure Active Directory chcete zahrnuty do související textová pole.

   b. Vyberte **Role**.

   c. Klikněte na **Submit** (Odeslat).

> [!NOTE]
> Můžete použít jakékoli jiné adaptivní Insights uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných adaptivní Insights uživatelským účtům, zřídit AAD.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici adaptivní Insights na přístupovém panelu, vám by měl být automaticky přihlášeni adaptivní insights, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)