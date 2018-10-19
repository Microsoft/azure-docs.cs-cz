---
title: 'Kurz: Integrace Azure Active Directory pomocí adaptivního Insights | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a adaptivní Insights.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 74f38ef3043cfa0e6df40c264b72cc5eae38544e
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430285"
---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-insights"></a>Kurz: Integrace Azure Active Directory pomocí adaptivního Insights

V tomto kurzu se dozvíte, jak integrovat adaptivní Insights s Azure Active Directory (Azure AD).

Integrace adaptivní Insights s Azure AD poskytuje následující výhody:

- Můžete řídit ve službě Azure AD, který má přístup k přehledům adaptivní.
- Uživatele, aby automaticky získat přihlášení k adaptivní Insights (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
- Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s adaptivní přehledy, potřebujete následující položky:

- Předplatné Azure AD
- Adaptivní Insights jednotné přihlašování povoleno předplatné

> [!NOTE]
> Pokud chcete vyzkoušet kroky v tomto kurzu, nedoporučujeme použití produkční prostředí.

Pokud chcete vyzkoušet kroky v tomto kurzu, postupujte podle těchto doporučení:

- Nepoužívejte produkčním prostředí, pokud to není nutné.
- Pokud nemáte prostředí zkušební verzi Azure AD, můžete si [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Popis scénáře
V tomto kurzu je otestovat Azure AD jednotné přihlašování v testovacím prostředí. Scénář popsaný v tomto kurzu se skládá ze dvou hlavních stavebních bloků:

1. Přidání adaptivní Insights z Galerie
2. Konfigurace a testování Azure AD jednotného přihlašování

## <a name="adding-adaptive-insights-from-the-gallery"></a>Přidání adaptivní Insights z Galerie
Pokud chcete nakonfigurovat integraci adaptivní Insights do služby Azure AD, budete muset adaptivní Insights přidat z Galerie na váš seznam spravovaných aplikací SaaS.

**Adaptivní Insights přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu. 

    ![image](./media/adaptivesuite-tutorial/selectazuread.png)

2. Přejděte do **podnikové aplikace**. Pak přejděte na **všechny aplikace**.

    ![image](./media/adaptivesuite-tutorial/a_select_app.png)
    
3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![image](./media/adaptivesuite-tutorial/a_new_app.png)

4. Do vyhledávacího pole zadejte **adaptivní Insights**vyberte **adaptivní Insights** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části Konfigurace a testování Azure AD jednotné přihlašování pomocí adaptivního přehledy na základě testovací uživatele nazývá "Britta Simon".

Pro jednotné přihlašování pro práci služba Azure AD potřebuje vědět, co uživatel protějšky v adaptivní Insights je pro uživatele ve službě Azure AD. Jinými slovy vztah odkazu mezi uživatele služby Azure AD a související uživatelské v adaptivní Insights je potřeba navázat.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s adaptivní přehledy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
3. **[Vytvořit testovacího uživatele adaptivní Insights](#create-an-adaptive-insights-test-user)**  – Pokud chcete mít protějšek Britta Simon adaptivní Insights, který je propojený s Azure AD reprezentace uživatele.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části Povolení služby Azure AD jednotného přihlašování na portálu Azure portal a konfigurace jednotného přihlašování v aplikaci adaptivní Insights.

**Ke konfiguraci Azure AD jednotné přihlašování pomocí adaptivního Insights, proveďte následující kroky:**

1. V [webu Azure portal](https://portal.azure.com/)na **adaptivní Insights** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![image](./media/adaptivesuite-tutorial/B1_B2_Select_SSO.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML** chcete povolit jednotné přihlašování.

    ![image](./media/adaptivesuite-tutorial/b1_b2_saml_sso.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **základní konfiguraci SAML** dialogového okna.

    ![image](./media/adaptivesuite-tutorial/b1-domains_and_urlsedit.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky, pokud chcete nakonfigurovat aplikace v **IDP** intiated režimu:

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_url.png)

    a. V **identifikátor (Entity ID)** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. V **adresy URL odpovědi** textového pole zadejte adresu URL pomocí následujícímu vzoru: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    >[!NOTE]
    > Adaptivní insights můžete získat identifikátor (Entity ID) a hodnoty adresy URL odpovědi **nastavení jednotného přihlašování SAML** stránky.
 
5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** a uložte ho do počítače.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_certficate.png) 

6. Na **nastavení adaptivní Insights** tématu, zkopírujte adresu URL odpovídající podle vašich požadavků.

    Všimněte si, že adresa URL může třeba následující:

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

    ![image](./media/adaptivesuite-tutorial/d1_samlsonfigure.png) 

7. V okně jiné webové prohlížeče Přihlaste se k webu společnosti adaptivní Insights jako správce.

8. Přejděte na **správce**.

    ![Správce](./media/adaptivesuite-tutorial/IC805644.png "správce")

9. V **uživatelů a rolí** klikněte na tlačítko **spravovat nastavení jednotného přihlašování SAML**.

    ![Správa nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/IC805645.png "spravovat nastavení jednotného přihlašování SAML")

10. Na **nastavení jednotného přihlašování SAML** stránce, proveďte následující kroky:

    ![Nastavení jednotného přihlašování SAML](./media/adaptivesuite-tutorial/IC805646.png "nastavení jednotného přihlašování SAML")

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

    ![image](./media/adaptivesuite-tutorial/d_users_and_groups.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![image](./media/adaptivesuite-tutorial/d_adduser.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![image](./media/adaptivesuite-tutorial/d_userproperties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **vlastnosti**, vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Vyberte **Vytvořit**.
 
### <a name="create-an-adaptive-insights-test-user"></a>Vytvořit testovacího uživatele adaptivní Insights

Povolit uživatele Azure AD se přihlaste k adaptivní přehledy, musí být zřízená na adaptivní přehledy. V případě adaptivní Insights zřizování je ruční úloha.

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

   c. Klikněte na tlačítko **odeslat**.

>[!NOTE]
>Můžete použít jakékoli jiné adaptivní Insights uživatele účtu nástrojů pro vytváření nebo rozhraní API poskytovaných adaptivní Insights uživatelským účtům, zřídit AAD.
>

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure tak, že udělíte přístup k přehledům adaptivní.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**.

    ![image](./media/adaptivesuite-tutorial/d_all_applications.png)

2. V seznamu aplikací vyberte **adaptivní Insights**.

    ![image](./media/adaptivesuite-tutorial/tutorial_adaptivesuite_app.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![image](./media/adaptivesuite-tutorial/d_leftpaneusers.png)

4. Vyberte **přidat** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![image](./media/adaptivesuite-tutorial/d_assign_user.png)

4. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

5. V **přidat přiřazení** dialogové okno Vybrat **přiřadit** tlačítko.
    
### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici adaptivní Insights na přístupovém panelu, vám by měl získat automaticky přihlášení k aplikaci adaptivní Insights.
Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Další zdroje informací:

* [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](tutorial-list.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
