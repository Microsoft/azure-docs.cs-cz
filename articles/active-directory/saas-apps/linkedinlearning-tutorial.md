---
title: 'Kurz: Integrace Azure Active Directory s Linkedinem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2018
ms.author: jeedes
ms.openlocfilehash: 0b742ec14dfb40d941fab68594d4bb45f6e37f55
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56099271"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-learning"></a>Kurz: Integrace Azure Active Directory s Linkedinem

V tomto kurzu se dozvíte, jak integrovat LinkedIn Learning s Azure Active Directory (Azure AD).
Integrace LinkedIn Learning s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Linkedinem.
* Uživatelům se automaticky přihlášeni k Linkedinem (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Linkedinem, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* LinkedIn Learning jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* LinkedIn Learning podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* LinkedIn Learning podporuje **JIT** zřizování uživatelů

## <a name="adding-linkedin-learning-from-the-gallery"></a>Přidání LinkedIn Learning v galerii

Pokud chcete nakonfigurovat integrace LinkedIn Learning do služby Azure AD, budete muset přidat LinkedIn Learning v galerii na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat LinkedIn Learning v galerii, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **LinkedIn Learning**vyberte **LinkedIn Learning** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![LinkedIn Learning v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s Linkedinem podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele ve službě LinkedIn Learning.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Linkedinem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace LinkedIn Learning Single Sign-On](#configure-linkedin-learning-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele LinkedIn Learning](#create-linkedin-learning-test-user)**  – Pokud chcete mít protějšek Britta Simon ve službě LinkedIn Learning, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Linkedinem, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **LinkedIn Learning** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **zahájené pomocí IdP** režimu proveďte následující kroky:

    ![LinkedIn Learning domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textového pole zadejte **Entity ID** zkopírovali z portálu LinkedIn. 

    b. V **adresy URL odpovědi** textového pole zadejte **Assertion Consumer Service (ACS) adresy Url** zkopírovali z portálu LinkedIn.

    c. Pokud chcete nakonfigurovat aplikace v **SP iniciované** režimu klikněte **nastavit další adresy URL** možnost **základní konfiguraci SAML** části můžete zadat vaše přihlašovací adresa URL. Chcete-li vytvořit kopii přihlašovací adresu Url **Assertion Consumer Service (ACS) adresy Url** a nahraďte /saml//přihlášení /. Jakmile, která byla provedena, přihlašovací adresa URL by měly mít následujícímu vzoru:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![LinkedIn Learning domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Tyto hodnoty nejsou skutečné hodnoty. Tyto hodnoty aktualizuje o skutečné identifikátor a adresa URL odpovědi, který je vysvětlen později v **konfigurace LinkedIn Learning jednotného přihlašování** části kurzu.

5. LinkedIn Learning aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. LinkedIn Learning aplikace očekává **nameidentifier** namapovat s **user.mail**, takže budete muset kliknout na Upravit mapování atributů **upravit** ikonu a změňte mapování atributů.

    ![image](common/edit-attribute.png)

6. Kromě toho výše LinkedIn Learning aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:
    
    | Název | Zdrojový atribut |
    | ---------------| --------------- |
    | e-mail  | user.mail  |
    | Oddělení  | user.department  |
    | jméno  | user.givenname  |
    | Příjmení  | user.surname  |

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

### <a name="configure-linkedin-learning-single-sign-on"></a>Konfigurace LinkedIn Learning jednotného přihlašování

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi LinkedIn Learning jako správce.

2. V **centra pro účty**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **Learning – výchozí** z rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Klikněte na tlačítko **nebo klikněte sem pro načítání a kopírování jednotlivých polí ve formuláři** a zkopírujte **Entity Id** a **Assertion Consumer Service (ACS) adresy Url** a vložte ji  **Základní konfigurace SAML** části webu Azure Portal.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Přejděte na **nastavení Linkedinu správce** oddílu. Nahrát soubor XML, který jste si stáhli z webu Azure portal kliknutím **soubor XML nahrajte** možnost.

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Klikněte na tlačítko **na** pro povolení jednotného přihlašování. Stav jednotného přihlašování se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurace jednotného přihlašování](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Linkedinem.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **LinkedIn Learning**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **LinkedIn Learning**.

    ![LinkedIn Learning odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-linkedin-learning-test-user"></a>Vytvořit testovacího uživatele Linkedinem

LinkedIn Learning aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele se vytvoří automaticky v aplikaci. Na správce stránky nastavení na portálu vyfiltrují LinkedIn Learning přepínač **automaticky přiřadit licence** na aktivní pouze v čase zřizování a to se také přiřadit licenci uživateli.

   ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Linkedinem na přístupovém panelu, vám by měl být automaticky přihlášeni na LinkedIn Learning, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

