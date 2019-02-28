---
title: 'Kurz: Integrace Azure Active Directory s iLMS | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a iLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d528195161b987dda783c4b22721e950f702f94
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984454"
---
# <a name="tutorial-azure-active-directory-integration-with-ilms"></a>Kurz: Integrace Azure Active Directory s iLMS

V tomto kurzu se dozvíte, jak integrovat iLMS s Azure Active Directory (Azure AD).
ILMS integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k iLMS.
* Můžete povolit uživatelům být automaticky přihlášeni k iLMS (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s iLMS, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* iLMS jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* podporuje iLMS **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-ilms-from-the-gallery"></a>Přidání iLMS z Galerie

Konfigurace integrace iLMS do služby Azure AD, budete muset přidat iLMS z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat iLMS z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **iLMS**vyberte **iLMS** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![iLMS v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí iLMS podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v iLMS.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s iLMS, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurovat Single Sign-On iLMS](#configure-ilms-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele iLMS](#create-ilms-test-user)**  – Pokud chcete mít protějšek Britta Simon v iLMS, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s iLMS, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **iLMS** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![iLMS domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textového pole, Vložit **identifikátor** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení portálu pro správu iLMS.

    b. V **adresy URL odpovědi** textového pole, Vložit **(adresa URL koncového bodu)** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení portálu pro správu iLMS s následující vzor `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![iLMS domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textového pole, Vložit **(adresa URL koncového bodu)** hodnotu zkopírujte z **poskytovatele služeb** SAML nastavení v portálu pro správu iLMS jako `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

6. Pokud chcete povolit JIT zřizování, iLMS aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikonu otevřete dialogové okno atributy uživatele.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Budete muset povolit **vytvořit uživatelský účet Un-recognized** v iLMS mapování těchto atributů. Postupujte podle pokynů [tady](http://support.inspiredelearning.com/customer/portal/articles/2204526) , kde získáte představu o konfiguraci atributy.

7. Kromě toho výše iLMS aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Zdrojový atribut|
    | --------|------------- |
    | Dělení | user.department |
    | oblast | user.state |
    | Oddělení | user.jobtitle |

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

9. Na **nastavení iLMS** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-ilms-single-sign-on"></a>Konfigurace iLMS jednotného přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **portál pro správu iLMS** jako správce.

2. Klikněte na tlačítko **SSO:SAML** pod **nastavení** kartě Otevřít nastavení SAML a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/1.png)

3. Rozbalte **poskytovatele služeb** části a zkopírujte **identifikátor** a **(adresa URL koncového bodu)** hodnotu.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/2.png) 

4. V části **zprostředkovatele Identity** klikněte na tlačítko **importovat Metadata**.

5. Vyberte **federačních metadat** soubor stáhnout z webu Azure Portal ze **podpisový certifikát SAML** oddílu.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Pokud chcete povolit JIT k vytváření účtů iLMS pro zrušení zřizování-rozpoznat uživatele, postupujte podle následujících kroků:

    a. Zkontrolujte **vytvořte účet bez rozpoznán jako uživatel**.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mapování atributů ve službě Azure AD s atributy v iLMS. Ve sloupci atributu zadejte název atributy nebo výchozí hodnotu.

    c. Přejděte na **obchodní pravidla** kartě a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/5.png)

    d. Zkontrolujte **vytvořit Un-recognized oblasti, oddělení a oddělení** vytvořit oblasti, oddělení a oddělení, které už neexistují v době jednotného přihlašování.

    e. Zkontrolujte **aktualizace uživatelského profilu při přihlášení** k určení, zda je s každou Single Sign-on aktualizovat profil uživatele.

    f. Pokud **aktualizace prázdné hodnoty pro jiné povinných polí v profilu uživatele** zaškrtnutá možnost, profil volitelné pole, která jsou prázdné po přihlášení bude také způsobit iLMS profilu uživatele tak, aby obsahovala prázdné hodnoty příslušných polí.

    g. Zkontrolujte **odeslat E-mail s oznámením o chybě** a zadejte e-mailu uživatele, kde chcete dostávat e-mailové oznámení chyby.

7. Klikněte na tlačítko **Uložit** uložte nastavení tlačítkem.

    ![Konfigurace jednotného přihlašování](./media/ilms-tutorial/save.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že udělíte přístup k iLMS.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **iLMS**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **iLMS**.

    ![Odkaz iLMS v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-ilms-test-user"></a>Vytvoření iLMS testovacího uživatele

Aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele se vytvoří automaticky v aplikaci. JIT bude fungovat, pokud jste klikli **vytvořit uživatelský účet Un-recognized** zaškrtávací políčko během SAML nastavení konfigurace na portálu pro správu iLMS.

Pokud je potřeba ručně vytvořit uživatele, postupujte podle následujících kroků:

1. Přihlaste se na web společnosti iLMS jako správce.

2. Klikněte na tlačítko **registrovat uživatele** pod **uživatelé** karty otevřete **registrovat uživatele** stránky.

   ![Přidat zaměstnance](./media/ilms-tutorial/3.png)

3. Na **registrovat uživatele** stránce, proveďte následující kroky.

    ![Přidat zaměstnance](./media/ilms-tutorial/create_testuser_add.png)

    a. V **křestní jméno** textové pole, typ první název, například Britta.

    b. V **příjmení** textového pole zadejte příjmení jako Simon.

    c. V **ID e-mailu** , jako je textové pole, typ e-mailovou adresu uživatele BrittaSimon@contoso.com.

    d. V **oblasti** rozevíracím seznamu vyberte hodnotu pro oblast.

    e. V **dělení** rozevíracím seznamu vyberte hodnotu pro dělení.

    f. V **oddělení** rozevíracím seznamu vyberte hodnotu pro oddělení.

    g. Klikněte na **Uložit**.

    > [!NOTE]
    > Můžete odesílat poštu registrace pro uživatele tak, že vyberete **odeslat poštu registrace** zaškrtávací políčko.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici iLMS na přístupovém panelu, můžete by měl být automaticky přihlášeni k iLMS, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)