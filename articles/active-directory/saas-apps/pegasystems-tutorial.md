---
title: 'Kurz: Integrace Azure Active Directory se systémy Pega | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Pega systémy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 44a9784e47772c5a2ae1335aa048a4d3b86073eb
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58577098"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Kurz: Integrace Azure Active Directory s Pega systémy

V tomto kurzu se dozvíte, jak integrovat systémy Pega se službou Azure Active Directory (Azure AD).
Integrace Pega systémy s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k systémům Pega.
* Uživatelům se automaticky přihlášeni k systémům Pega (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Pega systémy, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Systémy Pega jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje systémy Pega **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-pega-systems-from-the-gallery"></a>Přidání Pega systémů z Galerie

Pokud chcete nakonfigurovat integraci systémů Pega do služby Azure AD, budete muset přidat Pega systémů z Galerie na váš seznam spravovaných aplikací SaaS.

**Přidat Pega systémů z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Pega systémy**vyberte **Pega systémy** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Systémy Pega v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Pega systémy založené na test uživateli **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v systémech Pega.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Pega systémy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Pega systémy Single Sign-On](#configure-pega-systems-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Pega systémy](#create-pega-systems-test-user)**  – Pokud chcete mít protějšek Britta Simon Pega systémů, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Pega systémy, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Pega systémy** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Pega systémy domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Pega systémy domény a adresy URL jednotného přihlašování – informace](common/both-advanced-urls.png)

    a. V **přihlašovací adresa URL** textového pole zadejte znaménko na hodnotu adresy URL.

    b. V **stav přenosu** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné přihlašovací identifikátor, adresa URL odpovědi na adresu URL a adresy URL stavu. Můžete najít hodnoty identifikátor a adresa URL odpovědi z aplikace Pega, což je vysvětleno dále v tomto kurzu. Stav přenosu, kontaktujte [tým podpory klientské systémy Pega](https://www.pega.com/contact-us) má být získána hodnota. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Systémy Pega aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikony otevřete **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

7. Kromě toho výše systémy Pega aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Zdrojový atribut|
    | ------------------- | -------------------- |
    | uid | *********** |
    | CN  | *********** |
    | mail | *********** |
    | accessgroup | *********** |
    | organizace | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | workgroup | *********** |
    | Telefon | *********** |

    > [!NOTE]
    > Toto jsou konkrétní hodnoty zákazníka. Zadejte odpovídající hodnoty.

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. Na **nastavení Pega systémy** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-pega-systems-single-sign-on"></a>Konfigurace Pega systémy jednotného přihlašování

1. Ke konfiguraci jednotného přihlašování na **Pega systémy** straně, otevřete **Pega portál** pomocí účtu správce v jiném okně prohlížeče.

2. Vyberte **vytvořit** -> **SysAdmin** -> **ověřovací službu**.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Proveďte následující kroky na **vytvořit ověřovací službu** obrazovky:

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Vyberte **SAML 2.0** z typu

    b. V **název** textového pole zadejte libovolný název například jednotného přihlašování k Azure AD

    c. V **krátký popis** textového pole zadejte popis, žádné  

    d. Klikněte na **vytvořit a otevřít** 
    
4. V **zprostředkovatele Identity (IdP) informace** části, klikněte na **metadat zprostředkovatele identity Import** a vyhledejte soubor metadat, který jste si stáhli z portálu Azure portal. Klikněte na tlačítko **odeslat** načtení metadat.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. To bude naplnění dat zprostředkovatele identity, jak je znázorněno níže.

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Proveďte následující kroky na **nastavení poskytovatele služeb (SP)** části:

    ![Nakonfigurovat jednotné přihlašování uložit tlačítko](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Kopírovat **identifikace entit** hodnotu a vložte ji do **identifikátor** textového pole v **základní konfiguraci SAML** na webu Azure Portal.

    b.  Kopírovat **Assertion Consumer Service (ACS) umístění** hodnotu a vložte ji do **adresy URL odpovědi** textového pole textbox v **základní konfiguraci SAML** na webu Azure Portal.

    c. Vyberte **zakázat žádost o podepsání**.

7. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k systémům Pega.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Pega systémy**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Pega systémy**.

    ![Odkaz Pega systémy v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-pega-systems-test-user"></a>Vytvoření Pega systémy testovacího uživatele

Cílem této části je vytvořte uživatele Britta Simon v systémech Pega. Práce s [tým podpory klientské systémy Pega](https://www.pega.com/contact-us) vytvořit uživatele v Pega systémy.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Pega systémy na přístupovém panelu, vám by měl být automaticky přihlášeni ke Pega systémy, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)