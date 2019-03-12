---
title: 'Kurz: Integrace Azure Active Directory s Cisco Webex schůzky | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex schůzky.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4bf593473e646bbfe3d8db1df44bd48195b4cf7
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729657"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Kurz: Integrace Azure Active Directory s Cisco Webex schůzky

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Cisco Webex schůzky.
Integrace Cisco Webex schůzky s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Cisco Webex schůzky.
* Uživatelům se automaticky přihlášeni k Cisco Webex schůzky (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cisco Webex schůzky, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex schůzky jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Cisco Webex schůzky **SP** jednotné přihlašování zahájené pomocí

* Podporuje Cisco Webex schůzky **JIT** zřizování uživatelů

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Přidání schůzky Webex Cisco z Galerie

Ke konfiguraci integrace Cisco Webex schůzky do služby Azure AD, budete muset přidat Cisco Webex schůzky v galerii na váš seznam spravovaných aplikací SaaS.

**Přidání schůzky Webex Cisco z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cisco Webex schůzky**vyberte **Cisco Webex schůzky** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Cisco Webex schůzky v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Cisco Webex schůzky podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Cisco Webex schůzky.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex schůzky, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Cisco Webex schůzky Single Sign-On](#configure-cisco-webex-meetings-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Cisco Webex schůzky](#create-cisco-webex-meetings-test-user)**  – Pokud chcete mít protějšek Britta Simon Cisco Webex schůzky, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Cisco Webex schůzky, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Cisco Webex schůzky** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na webu Azure Portal na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části tím, že nahrajete na stažený **metadata poskytovatele služeb** souboru a nakonfigurovat aplikaci tak, že provedete následující kroky:

    >[!Note]
    >Zobrazí se soubor metadat zprostředkovatelů služby, který je vysvětlen později v **konfigurace Cisco Webex schůzky Single Sign-On** části kurzu. 

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![Nahrát soubor metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Zvolte soubor metadat](common/browse-upload-metadata.png)

    c. Po úspěšném dokončení nahrávání souboru metadat poskytovatele služeb **identifikátor** a **adresy URL odpovědi** hodnoty získat automaticky vyplní v **základní konfiguraci SAML** části:

    ![Cisco Webex schůzky domény a adresy URL jednotného přihlašování – informace](common/sp-identifier-reply.png)

    V **přihlašovací adresa URL** textového pole vložte hodnotu **adresy URL odpovědi** které získá automaticky vyplněný odeslání souboru SP metadat.

5. Cisco Webex schůzky aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na **upravit** ikona přidat atributy.

    ![image](common/edit-attribute.png)

6. Odstranit výchozí atributy z **deklarace identity uživatelů** oddílu a Cisco Webex schůzky aplikace očekává, že několik další atributy se mají předat zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:
    
    | Název | Zdrojový atribut|
    | ---------------|  --------- |
    |   jméno    | user.givenname |
    |   Příjmení    | user.surname |
    |   e-mail       | user.mail |
    |   uid    | user.mail |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. Na **nastavení Cisco Webex schůzky** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Konfigurace Cisco Webex schůzky jednotného přihlašování

1. Přejděte na [správu spolupráci cloudu Cisco](https://www.webex.com/go/connectadmin) pomocí svých přihlašovacích údajů pro správu.

2. Přejděte na **nastavení zabezpečení** a přejděte do **federované Konfigurace jednotného přihlašování k webu**.
 
    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. Na **federované Konfigurace jednotného přihlašování k webu** proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. V textovém poli protokol Federation zadejte váš protokol.

    b. Klikněte na **importovat Metadata SAML** odkaz nahrát soubor metadat, který jste si stáhli z webu Azure portal.

    c. Klikněte na **exportovat** tlačítko a stáhněte si soubor metadat zprostředkovatelů služby a nahrajte ho do **základní konfiguraci SAML** části na webu Azure portal.

    d. V **AuthContextClassRef** textové pole, typ `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` a pokud budete chtít povolit vícefaktorové ověřování pomocí Azure AD zadejte dvě hodnoty jako `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Vyberte **automatické vytvoření účtu**.

    >[!NOTE]
    >Pro povolení **just-in-time** uživatele zřizování je potřeba zkontrolovat **automatické vytvoření účtu**. Kromě toho atributy tokenu SAML nutné mají být předány jako odpověď SAML.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Cisco Webex schůzky.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Cisco Webex schůzky**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Cisco Webex schůzky**.

    ![Odkaz Cisco Webex schůzky v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-cisco-webex-meetings-test-user"></a>Vytvoření schůzky Webex Cisco testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v Cisco Webex schůzky. Podporuje Cisco Webex schůzky **just-in-time** zřizování, který je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Cisco Webex schůzky, je vytvořen nový při pokusu o přístup k Cisco Webex schůzky.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici schůzky Webex Cisco na přístupovém panelu, vám by měl být automaticky přihlášeni ke schůzky Webex Cisco, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

