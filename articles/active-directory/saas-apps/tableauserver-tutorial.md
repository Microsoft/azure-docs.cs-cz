---
title: 'Kurz: Integrace Azure Active Directory s Tableau Server | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 2ae0aefe194ca8bca6ea62420314b4fbdb1e0187
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357918"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Kurz: Integrace Azure Active Directory s Tableau Server

V tomto kurzu se dozvíte, jak integrovat Tableau Server se službou Azure Active Directory (Azure AD).
Tableau Server integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k serveru Tableau.
* Uživatelům se automaticky přihlášeni k Tableau serveru (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Tableau Server, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Tableau Server jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Tableau Server podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-tableau-server-from-the-gallery"></a>Přidání Tableau serveru z Galerie

Konfigurace integrace Tableau Server do služby Azure AD, budete muset přidat Tableau serveru v galerii na váš seznam spravovaných aplikací SaaS.

**Tableau Server přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Tableau Server**vyberte **Tableau Server** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Tableau Server v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí Tableau serveru podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Tableau Server.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Tableau Server, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Tableau Server Single Sign-On](#configure-tableau-server-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Tableau Server](#create-tableau-server-test-user)**  – Pokud chcete mít protějšek Britta Simon v Tableau Server, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Tableau Server, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Tableau Server** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Tableau Server domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://azure.<domain name>.link`

    b. V **identifikátor** pole, zadejte adresu URL, pomocí následujícího vzorce: `https://azure.<domain name>.link`

    c. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Předchozí hodnoty nejsou skutečné hodnoty. Aktualizujte hodnoty s skutečnou adresu URL a identifikátorem na stránce konfigurace Tableau Server, který je vysvětlen později v tomto kurzu.

5. Tableau Server aplikace očekává vlastních deklarací identity **uživatelské jméno** který musí být definováno níže. To se používá jako identifikátor uživatele místo deklarace uživatele. jedinečný identifikátor. Můžete spravovat hodnotami těchto atributů z **atributy uživatele a deklarace identity** části na stránce aplikací pro integraci. Klikněte na tlačítko **upravit** tlačítko Otevřít **atributy uživatele a deklarace identity** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele a deklarace identity** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název | Zdrojový atribut | Obor názvů |
    | ---------------| --------------- | ----------- |
    | uživatelské jméno | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Zadejte **Namespace** hodnotu.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. Na **nastavení Tableau serveru** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurace Tableau serveru jednotného přihlašování

1. Pokud chcete získat jednotné přihlašování nakonfigurované pro vaši aplikaci, budete muset přihlásit k vašemu tenantovi Tableau Server jako správce.

2. Na **konfigurace** kartu, vyberte možnost **uživatele identita a přístup**a pak vyberte **ověřování** metoda kartu.

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Na **konfigurace** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Pro **metodu ověřování**, vyberte SAML.

    b. Zaškrtněte políčko z **povolit ověřování SAML pro server**.

    c. Tableau Server návratová adresa URL – adresa URL, Tableau Server uživatelé budou přistupovat k, jako například <http://tableau_server>. Pomocí `http://localhost` se nedoporučuje. Pomocí adresy URL s koncovým lomítkem (například `http://tableau_server/`) se nepodporuje. Kopírování **Tableau Server návratová adresa URL** a vložte ho do **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** části webu Azure Portal

    d. SAML entity ID – entity ID jednoznačně identifikuje Tableau Server instalace pro zprostředkovatele identity. Můžete zadat adresu URL svého Tableau Server znovu sem, pokud chcete, můžete, ale nemusí být vaše adresa URL serveru Tableau. Kopírování **SAML entity ID** a vložte ho do **identifikátor** textového pole v **základní konfiguraci SAML** části webu Azure Portal

    e. Klikněte na tlačítko **stáhnout soubor metadat XML** a otevřít ji v aplikaci textového editoru. Vyhledejte adresu URL služby příjemce kontrolního výrazu s Http Post a indexu 0 a zkopírujte adresu URL. Nyní vložte ji do **adresy URL odpovědi** textového pole v **základní konfiguraci SAML** části webu Azure Portal

    f. Vyhledejte federačních metadat soubor stažený z webu Azure portal a pak ho nahrajte **soubor metadat SAML zprostředkovatele identity**.

    g. Zadejte názvy atributů, které zprostředkovatel identity je používá pro uložení uživatelských jmen, zobrazované názvy, e-mailové adresy.

    h. Klikněte na **Uložit**.

    > [!NOTE]
    > Zákazník muset nahrát libovolný certifikát v konfiguraci jednotného přihlašování SAML Tableau serveru a bude získat ignorovat v toku jednotného přihlašování. Pokud potřebujete pomoct konfiguraci SAML na Tableau serveru, najdete v tomto článku [konfigurace SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k serveru Tableau.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Tableau Server**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Tableau Server**.

    ![Tableau Server odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-tableau-server-test-user"></a>Vytvořit testovacího uživatele Tableau Server

Cílem této části je vytvořte uživatele Britta Simon v Tableau Server. Budete muset zřídit všechny uživatele v Tableau server.

Tímto uživatelským jménem uživatele by měl odpovídat hodnotě, které jste nakonfigurovali v vlastní atribut Azure AD **uživatelské jméno**. Integrace správné mapování by měl pracovat konfigurace služby Azure AD Single Sign-On.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, budete muset kontaktovat správce Tableau serveru ve vaší organizaci.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Tableau Server na přístupovém panelu, vám by měl být automaticky přihlášeni Tableau server, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

