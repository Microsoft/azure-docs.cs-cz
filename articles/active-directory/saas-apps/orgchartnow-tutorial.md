---
title: 'Kurz: Integrace Azure Active Directory s organizačního diagramu teď | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a nyní organizačního diagramu.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: cc2bbd0c1220a37de640bde6294eb096b25e5398
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "65870492"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Kurz: Integrace Azure Active Directory se nyní organizačního diagramu

V tomto kurzu se dozvíte, jak integrovat organizačního diagramu teď s Azure Active Directory (Azure AD).
Integrace organizačního diagramu teď s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup do tohoto okamžiku organizačního diagramu.
* Uživatelům se automaticky přihlášeni do tohoto okamžiku organizační diagram (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s organizačního diagramu teď, budete potřebovat následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Organizační diagram nyní jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Organizační diagram nyní podporuje **SP** a **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-orgchart-now-from-the-gallery"></a>Přidání organizačního diagramu teď z Galerie

Konfigurace integrace organizačního diagramu teď do služby Azure AD, budete muset přidat organizačního diagramu teď z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat organizačního diagramu teď z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **organizačního diagramu teď**vyberte **organizačního diagramu teď** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Organizační diagram nyní v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a testovací služby Azure AD jednotné přihlašování pomocí organizačního diagramu teď podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské istrovat hned organizačního diagramu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s nyní organizačního diagramu, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace organizačního diagramu teď Single Sign-On](#configure-orgchart-now-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele organizačního diagramu teď](#create-orgchart-now-test-user)**  – Pokud chcete mít protějšek Britta Simon organizačního diagramu teď propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování pomocí organizačního diagramu teď, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **organizačního diagramu teď** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, postupujte následovně:

    ![Organizační diagram nyní domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    V **identifikátor** textové pole, zadejte adresu URL:  `https://sso2.orgchartnow.com`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![image](common/both-preintegrated-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` je **Azure AD identifikátor** zkopírovanými z **nastavit organizačního diagramu teď** části je popsáno dále v kurzu.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavit organizačního diagramu teď** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-orgchart-now-single-sign-on"></a>Konfigurace organizačního diagramu teď jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **organizačního diagramu teď** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [tým podpory nyní organizačního diagramu ](mailto:ocnsupport@officeworksoftware.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do tohoto okamžiku organizačního diagramu.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **organizačního diagramu teď**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **organizačního diagramu teď**.

    ![Odkaz organizačního diagramu teď v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-orgchart-now-test-user"></a>Vytvořit testovacího uživatele nyní organizačního diagramu

Pokud chcete povolit Azure AD uživatelům se přihlásit do tohoto okamžiku organizačního diagramu, musí být poskytnuty do organizačního diagramu teď. 

1. Organizačního diagramu teď podporuje just-in-time zřizování, který je ve výchozím nastavení povolená. Nový uživatel se vytvoří během pokusu o přístup k nyní organizačního diagramu, pokud ještě neexistuje. Zřizování funkce uživatelů v čase vytvoří pouze **jen pro čtení** uživatele, pokud požadavek jednotného přihlašování pochází z rozpoznaný zprostředkovatele identity a e-mailu v kontrolní výraz SAML nebyl nalezen v seznamu uživatelů. Pro tento automatické zřizování funkci je potřeba vytvořit skupinu přístupu s názvem **Obecné** istrovat hned organizačního diagramu. Postupujte prosím podle níže uvedený postup k vytvoření přístupové skupiny:

    a. Přejděte **spravovat skupiny** možnosti po kliknutí na tlačítko **ozubeného kolečka** v pravém horním rohu uživatelského rozhraní.

    ![Nyní organizačního diagramu skupiny](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Vyberte **přidat** ikonu a název skupiny **Obecné** klikněte **OK**. 

    ![Přidejte teď organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Vyberte složky, které chcete mít přístup k uživatelům obecné nebo jen pro čtení:

    ![Organizační diagram nyní složky](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Zámek** složky tak, aby pouze správci je můžou upravit. Stiskněte klávesu **OK**.

    ![Nyní organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Chcete-li vytvořit **správce** uživatelů a **r/w** uživatelů, musíte ručně vytvořit uživatel získal přístup k jejich úrovně oprávnění prostřednictvím jednotného přihlašování. K poskytnutí uživatelského účtu, postupujte následovně:

    a. Přihlaste se k organizační diagram nyní jako správce zabezpečení.

    b.  Klikněte na **nastavení** v pravém horním rohu a pak přejděte do **spravovat uživatele**.

    ![Organizační diagram nyní nastavení](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klikněte na **přidat** a proveďte následující kroky:

    ![Správa nyní organizačního diagramu](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * V **ID uživatele** textového pole zadejte ID uživatele jako **brittasimon\@contoso.com**.

    * V **e-mailovou adresu** textové pole, zadejte e-mailu uživatele, jako je **brittasimon\@contoso.com**.

    * Klikněte na tlačítko **Add** (Přidat).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici organizačního diagramu teď na přístupovém panelu, vám by měl být automaticky přihlášeni do tohoto okamžiku organizačního diagramu, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

