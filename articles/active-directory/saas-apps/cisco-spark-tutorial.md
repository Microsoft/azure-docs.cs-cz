---
title: 'Kurz: Integrace Azure Active Directory s Cisco Webex | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1460fba4d6897dfcc6bf40b6e02ab856ffe8456
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340464"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Kurz: Integrace Azure Active Directory s Cisco Webex

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) Cisco Webex.
Integrace Cisco Webex s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Cisco Webex.
* Uživatelům se automaticky přihlášeni k Webex Cisco (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Cisco Webex, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Cisco Webex **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-cisco-webex-from-the-gallery"></a>Přidání Cisco Webex z Galerie

Konfigurace integrace Cisco Webex do služby Azure AD, budete muset přidat Cisco Webex z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Cisco Webex z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Cisco Webex**vyberte **Cisco Webex** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Cisco Webex v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Cisco Webex podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Cisco Webex.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Cisco Webex, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Cisco Webex Single Sign-On](#configure-cisco-webex-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Cisco Webex](#create-cisco-webex-test-user)**  – Pokud chcete mít protějšek Britta Simon Cisco Webex, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Cisco Webex, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Cisco Webex** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Cisco Webex domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://web.ciscospark.com/#/signin`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://idbroker.webex.com/<Org Id>`

    > [!NOTE]
    > Tato hodnota identifikátor není skutečný. Aktualizujte tuto hodnotu skutečné identifikátor. Pokud máte službu zprostředkovatele metadat, nahrajte ho do **základní konfiguraci SAML** části pak bude **identifikátor (Entity ID)** hodnotu získá automaticky vyplní automaticky.

5. Aplikaci Webex Cisco očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na **upravit** ikona přidat atributy.

    ![image](common/edit-attribute.png)

6. Kromě toho výše aplikaci Cisco Webex očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:
    
    | Název |  Zdrojový atribut|
    | ---------------|--------- |
    | ID uživatele | user.userprincipalname |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

8. Na **nastavení Cisco Webex** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-cisco-webex-single-sign-on"></a>Konfigurace Cisco Webex jednotné přihlašování

1. Přihlaste se k [správu spolupráci cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

2. Vyberte **nastavení** a v části **ověřování** klikněte na tlačítko **změnit**.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
  
3. Vyberte **integrovat zprostředkovatele identity 3. stran. (Rozšířené)**  a přejít na další obrazovce.

4. Na **Import metadat zprostředkovatele identity** stránka, buď přetažením a soubor metadat služby Azure AD na stránku nebo pomocí možnosti prohlížeč souborů najděte a nahrajte soubor metadat služby Azure AD. Vyberte **vyžadovat certifikát podepsaný certifikační autoritou v metadatech (s vyšším zabezpečením)** a klikněte na tlačítko **Další**.

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

5. Vyberte **Test jednotného přihlašování k připojení**a když se otevře na nové kartě prohlížeče, proveďte ověření pomocí služby Azure AD přihlášením.

6. Zpět **správu spolupráci cloudu Cisco** záložce prohlížeče. Pokud byl test úspěšný, vyberte **tento test proběhl úspěšně. Možnost jednotného přihlašování** a klikněte na tlačítko **Další**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Cisco Webex.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Cisco Webex**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Cisco Webex**.

    ![Odkaz Cisco Webex v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-cisco-webex-test-user"></a>Vytvořit testovacího uživatele Cisco Webex

V této části vytvořte uživatele Britta Simon v Cisco Webex. V této části vytvořte uživatele Britta Simon v Cisco Webex.

1. Přejděte [správu spolupráci cloudu Cisco](https://admin.ciscospark.com/) pomocí svých přihlašovacích údajů správce s úplnými oprávněními.

2. Klikněte na tlačítko **uživatelé** a potom **spravovat uživatele**.
   
    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. V **spravovat uživatele** okně **ručně přidávat nebo upravovat uživatele** a klikněte na tlačítko **Další**.

4. Vyberte **jména a e-mailová adresa**. Potom zadejte textového pole následujícím způsobem:

    ![Konfigurace jednotného přihlašování](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 

    a. V **křestní jméno** textového pole zadejte jméno uživatele, jako je **Britta**.

    b. V **příjmení** textového pole zadejte příjmení uživatele, jako je **Simon**.

    c. V **e-mailová adresa** , jako je textové pole, typ e-mailovou adresu uživatele **britta.simon@contoso.com**.

5. Klikněte symbol plus přidejte Britta Simon. Pak klikněte na **Další**.

6. V **přidat služby pro uživatele** okna, klikněte na tlačítko **Uložit** a potom **Dokončit**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Cisco Webex na přístupovém panelu, vám by měl být automaticky přihlášeni ke Webex Cisco, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
