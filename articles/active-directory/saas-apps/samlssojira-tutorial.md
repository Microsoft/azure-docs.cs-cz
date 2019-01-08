---
title: 'Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a jednotné přihlašování SAML pro Jira podle rozlišení GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 3e527965782cc951553a5b5721955d4d3cfe67c6
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54065473"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Kurz: Integrace Azure Active Directory s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH

V tomto kurzu se dozvíte, jak integrovat jednotné přihlašování SAML pro Jira podle rozlišení GmbH se službou Azure Active Directory (Azure AD).
Integrace jednotného přihlašování SAML pro Jira podle rozlišení GmbH s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k SAML SSO pro Jira podle rozlišení GmbH.
* Můžete povolit uživatelům být automaticky přihlášeni pro jednotné přihlašování SAML pro Jira podle rozlišení GmbH (Single Sign-On) pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s jednotným Přihlašováním SAML pro Jira s rozlišení GmbH, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Jednotné přihlašování SAML pro Jira podle rozlišení GmbH jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Jednotné přihlašování SAML pro Jira tak řešení podporuje GmbH **SP** a **IDP**jednotné přihlašování zahájené pomocí

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Přidání jednotného přihlašování SAML pro Jira podle rozlišení GmbH z Galerie

Pokud chcete nakonfigurovat integraci jednotného přihlašování SAML pro Jira podle rozlišení GmbH do služby Azure AD, budete muset přidat jednotné přihlašování SAML pro Jira rozlišení GmbH z Galerie na váš seznam spravovaných aplikací SaaS.

**Pokud chcete přidat jednotné přihlašování SAML pro Jira z Galerie řešení GmbH, proveďte následující kroky:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**vyberte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace .

     ![Jednotné přihlašování SAML pro Jira podle rozlišení GmbH v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části nakonfigurujete a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML Jira podle rozlišení GmbH podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v jednotné přihlašování SAML pro Jira podle rozlišení GmbH musí být vytvořeno.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurovat jednotné přihlašování SAML pro Jira rozlišení GmbH Single Sign-On](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit jednotné přihlašování SAML pro Jira rozlišení GmbH testovací uživatel](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)**  – Pokud chcete mít protějšek Britta Simon v jednotné přihlašování SAML pro Jira podle rozlišení GmbH, která souvisí s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s jednotným Přihlašováním SAML pro Jira podle rozlišení GmbH, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **jednotné přihlašování SAML pro Jira podle rozlišení GmbH** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu proveďte následující kroky:

    ![Jednotné přihlašování SAML pro Jira podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Jednotné přihlašování SAML pro Jira podle rozlišení GmbH domény a adresy URL jednotné přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory jednotného přihlašování SAML pro Jira podle rozlišení GmbH klienta](https://www.resolution.de/go/support) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

6. Na **nastavit jednotné přihlašování SAML pro Jira rozlišení GmbH** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on"></a>Nakonfigurovat jednotné přihlašování SAML pro Jira rozlišení GmbH jednotného přihlašování

1. V okně jiné webové prohlížeče, přihlaste se k vaší **jednotné přihlašování SAML pro Jira pomocí portálu pro správu řešení GmbH** jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **doplňky**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon1.png)

3. Budete přesměrováni na stránku přístup správce. Zadejte **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon2.png)

4. Karta části doplňků, klikněte na tlačítko **najít nové doplňky**. Hledání **SAML jednotné přihlašování (SSO) pro JIRA** a klikněte na tlačítko **nainstalovat** tlačítko k instalaci nové zásuvný modul SAML.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon7.png)

5. Spustí se instalace modulu plug-in. Klikněte na **Zavřít**.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon8.png)

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon9.png)

6.  Klikněte na **Manage** (Spravovat).

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon10.png)
    
8. Klikněte na tlačítko **konfigurovat** konfigurace nového modulu plug-in.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon11.png)

9. Na **konfigurace modulu plug-in SingleSignOn SAML** klikněte na **přidat nového zprostředkovatele identity** tlačítko ke konfiguraci nastavení zprostředkovatele Identity.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon4.png)

10. Na **zvolte zprostředkovatele Identity SAML** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5a.png)
 
    a. Nastavte **Azure AD** jako typ zprostředkovatele identity.
    
    b. Přidat **název** zprostředkovatele Identity (např. Azure AD).
    
    c. Přidat **popis** zprostředkovatele Identity (např. Azure AD).
    
    d. Klikněte na **Další**.
    
11. Na **konfigurace zprostředkovatele Identity** klikněte na **Další** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5b.png)

12. Na **Import metadat zprostředkovatele identity SAML** stránce, proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5c.png)

    a. Klikněte na tlačítko **načíst soubor** tlačítko a vyberte soubor metadat XML, které jste si stáhli v kroku 5.

    b. Klikněte na tlačítko **Import** tlačítko.
    
    c. Počkejte krátce import úspěšný.
    
    d. Klikněte na tlačítko **Další** tlačítko.
    
13. Na **atribut ID uživatele a transformace** klikněte na **Další** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon5d.png)
    
14. Na **vytvoření uživatele a aktualizace** klikněte na **u & ložit Další** se uložit nastavení.   
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6a.png)
    
15. Na **Test nastavení** klikněte na **přeskočit testovací & ručně nakonfigurovat** testovací uživatel prozatím přeskočit. To se provede v další části a vyžaduje některá nastavení na webu Azure portal. 
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6b.png)
    
16. V dialogu čtení apprearing **přeskakování testů znamená...** , klikněte na tlačítko **OK**.
    
    ![Konfigurace jednotného přihlašování](./media/samlssojira-tutorial/addon6c.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k jednotné přihlašování SAML pro Jira podle rozlišení GmbH.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **jednotné přihlašování SAML pro Jira podle rozlišení GmbH**.

    ![Jednotné přihlašování SAML pro Jira podle propojení GmbH řešení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Vytvořit jednotné přihlašování SAML pro Jira podle rozlišení GmbH testovacího uživatele

Umožňuje uživatelům Azure AD k přihlášení na jednotné přihlašování SAML pro Jira rozlišení GmbH, musí být zřízená do jednotné přihlašování SAML pro Jira podle rozlišení GmbH.  
V SAML SSO pro Jira podle rozlišení GmbH zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k SAML SSO pro Jira rozlišení GmbH společnosti lokalita jako správce.

2. Najeďte myší na ikonu a klikněte na tlačítko **Správa uživatelů**.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user1.png) 

3. Budete přesměrováni na stránku přístup správce k zadání **heslo** a klikněte na tlačítko **potvrdit** tlačítko.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user2.png) 

4. V části **Správa uživatelů** kartě oddíl, klikněte na tlačítko **vytvořit uživatele**.

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user3.png) 

5. Na **"Vytvořit nový uživatel"** dialogového okna stránky, proveďte následující kroky:

    ![Přidat zaměstnance](./media/samlssojira-tutorial/user4.png) 

    a. V **e-mailová adresa** , jako je textové pole, typ e-mailovou adresu uživatele Brittasimon@contoso.com.

    b. V **jméno a příjmení** textového pole zadejte celé jméno uživatele jako Britta Simon.

    c. V **uživatelské jméno** , jako je textové pole, typ e-mailu uživatele Brittasimon@contoso.com.

    d. V **heslo** textového pole zadejte heslo uživatele.

    e. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na jednotné přihlašování SAML pro Jira podle rozlišení GmbH dlaždici na přístupovém panelu, by měl být automaticky přihlásíte na jednotné přihlašování SAML pro Jira podle rozlišení GmbH, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

