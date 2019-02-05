---
title: 'Kurz: Integrace Azure Active Directory s živém chatu Comm100 | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Comm100 živém chatu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.openlocfilehash: 1e0fbf7678ddac76d1a31c4bde4d75545b429add
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700687"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Kurz: Integrace Azure Active Directory s Comm100 živém chatu

V tomto kurzu se dozvíte, jak integrovat Comm100 živém chatu s Azure Active Directory (Azure AD).
Integrace Comm100 živém chatu s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Comm100 živém chatu.
* Uživatelům se automaticky přihlášeni k Comm100 živém chatu (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Comm100 živém chatu, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Živém chatu Comm100 jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Živém chatu Comm100 podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Přidání živém chatu Comm100 z Galerie

Pokud chcete nakonfigurovat integraci živém chatu Comm100 do služby Azure AD, musíte doplnit Comm100 živém chatu v galerii váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat živém chatu Comm100 z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **živém chatu Comm100**vyberte **živém chatu Comm100** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Comm100 živém chatu v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Comm100 živém chatu na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v živém chatu Comm100.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Comm100 živém chatu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Comm100 živého chatu jednotného přihlašování](#configure-comm100-live-chat-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Comm100 živém chatu](#create-comm100-live-chat-test-user)**  – Pokud chcete mít protějšek Britta Simon Comm100 živém chatu, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Comm100 živém chatu, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **živém chatu Comm100** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Domény živého chatu Comm100 a adresy URL jednotného přihlašování – informace](common/sp-signonurl.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > Hodnota přihlašovací adresa URL není skutečný. Hodnota přihlašovací adresa URL bude aktualizován skutečné přihlašovací adresa URL, který je vysvětlen později v tomto kurzu.

5. Živém chatu Comm100 aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky: 

    | Název |  Zdrojový atribut|
    | ---------------| --------------- |
    |   e-mail    | user.mail |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení živém chatu Comm100** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-comm100-live-chat-single-sign-on"></a>Konfigurace Comm100 živý Chat jednotného přihlašování

9. V jiné okno webového prohlížeče, přihlaste se k Comm100 živém chatu jako správce zabezpečení.

10. V horní pravé straně stránky klikněte na **Můj účet**.

    ![Myaccount Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

11. V levé nabídce klikněte na **zabezpečení** a potom klikněte na tlačítko **agenta Single Sign-On**.

    ![Zabezpečení Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

12. Na **agenta Single Sign-On** stránce, proveďte následující kroky:

    ![Zabezpečení Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

    a. Zkopírujte první zvýrazněný odkaz a vložte ji **přihlašovací adresa URL** textového pole v **Comm100 Live domény chatu a adresy URL** části na webu Azure portal.

    b. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. V **vzdálené adresy URL odhlašovací** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. Klikněte na tlačítko **zvolte soubor** nahrát base-64 kódování certifikátu, který jste si stáhli z portálu Azure portal do **certifikát**.

    e. Klikněte na tlačítko **uložit změny**

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Comm100 živém chatu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **živém chatu Comm100**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **živém chatu Comm100**.

    ![Na živém chatu Comm100 odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-comm100-live-chat-test-user"></a>Vytvořit testovacího uživatele Comm100 živém chatu

Povolení služby Azure AD uživatelům přihlášení na živém chatu Comm100, musí být poskytnuty do Comm100 živém chatu. Zřizování v živém chatu Comm100, je ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k Comm100 živý Chat jako správce zabezpečení.

2. V horní pravé straně stránky klikněte na **Můj účet**.

    ![Myaccount Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. V levé nabídce klikněte na **agentů** a potom klikněte na tlačítko **nového agenta**.

    ![Agent Comm100 živém chatu](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Na **nového agenta** stránce, proveďte následující kroky:

    ![Živém chatu Comm100 nového agenta](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. V **e-mailu** textové pole, zadejte e-mailu uživatele, jako je **Brittasimon@contoso.com**.

    b. V **křestní jméno** textové pole, zadejte jméno uživatele, jako je **Britta**.

    c. V **příjmení** textové pole, zadejte příjmení uživatele, jako je **simon**.

    d. V **zobrazovaného názvu** textového pole zadejte zobrazované jméno uživatele, jako je **Britta simon**

    e. V **heslo** textového pole zadejte heslo.

    f. Klikněte na **Uložit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Comm100 živém chatu na přístupovém panelu, můžete by měl být automaticky přihlášeni k Comm100 živém chatu u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

