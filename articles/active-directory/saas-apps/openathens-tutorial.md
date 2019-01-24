---
title: 'Kurz: Integrace Azure Active Directory s OpenAthens | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a OpenAthens.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dd4adfc7-e238-41d5-8b25-1811f08078b6
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/4/2019
ms.author: jeedes
ms.openlocfilehash: 9fe00ad1b567fff28301c981413d281869d2aec1
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808381"
---
# <a name="tutorial-azure-active-directory-integration-with-openathens"></a>Kurz: Integrace Azure Active Directory s OpenAthens

V tomto kurzu se dozvíte, jak integrovat OpenAthens s Azure Active Directory (Azure AD).
OpenAthens integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k OpenAthens.
* Můžete povolit uživatelům být automaticky přihlášeni k OpenAthens (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s OpenAthens, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* OpenAthens jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje OpenAthens **IDP** jednotné přihlašování zahájené pomocí

* Podporuje OpenAthens **JIT** zřizování uživatelů

## <a name="adding-openathens-from-the-gallery"></a>Přidání OpenAthens z Galerie

Konfigurace integrace OpenAthens do služby Azure AD, budete muset přidat OpenAthens z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat OpenAthens z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **OpenAthens**vyberte **OpenAthens** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![OpenAthens v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí OpenAthens podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v OpenAthens.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s OpenAthens, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace OpenAthens Single Sign-On](#configure-openathens-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele OpenAthens](#create-openathens-test-user)**  – Pokud chcete mít protějšek Britta Simon OpenAthens, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s OpenAthens, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **OpenAthens** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

5. Na **základní konfiguraci SAML** části tím, že nahrajete **soubor metadat poskytovatele služeb**, kroky pro které jsou uvedeny dále v tomto kurzu.

    a. Klikněte na tlačítko **nahrát soubor metadat**.

    ![openathens nahrát metadat](common/upload-metadata.png)

    b. Klikněte na **složky logo** vyberte soubor metadat a klikněte na **nahrát**.

    ![Openathens umožňuje procházet metadaty nahrávání](common/browse-upload-metadata.png)

    c. Po úspěšném odeslání souboru metadat **identifikátor** hodnotu get automaticky vyplní v **základní konfiguraci SAML** části textového pole:

    ![OpenAthens domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-openathens-single-sign-on"></a>Konfigurace OpenAthens jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti OpenAthens jako správce.

2. Vyberte **připojení** ze seznamu **správu** kartu. 

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application1.png)

3. Vyberte **SAML 1.1 nebo 2.0**a pak vyberte **konfigurovat** tlačítko.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application2.png)
    
4. Chcete-li přidat konfiguraci, vyberte **Procházet** tlačítko Nahrát soubor metada .xml, který jste si stáhli z webu Azure portal a potom vyberte **přidat**.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application3.png)

5. Proveďte následující kroky v části **podrobnosti** kartu.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application4.png)

    a. V **zobrazovaný název mapování**vyberte **použít atribut**.

    b. V **zobrazovaný název atributu** textové pole, zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.
    
    c. V **mapování jedinečných uživatelů**vyberte **použít atribut**.

    d. V **jedinečný uživatelský atribut** textové pole, zadejte hodnotu `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    e. V **stav**, zaškrtněte všechna tři políčka.

    f. V **místní účty**vyberte **automaticky**.

    g. Vyberte **uložit změny**.

    h. Z **<> / přijímající strana** kartu, zkopírujte **adresa URL metadat** a otevřít v prohlížeči ke stažení **kód XML metadat SP** souboru. Nahrajte tento soubor metadat SP na **základní konfiguraci SAML** oddíl ve službě Azure AD.

    ![Konfigurace jednotného přihlašování](./media/openathens-tutorial/tutorial_openathens_application5.png)

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

V této části je povolit Britta Simon k udělení přístupu k OpenAthens použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **OpenAthens**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikace zadejte a vyberte **OpenAthens**.

    ![Odkaz OpenAthens v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-openathens-test-user"></a>Vytvoření OpenAthens testovacího uživatele

V této části se vytvoří uživateli Britta Simon v OpenAthens. Podporuje OpenAthens **zřizování uživatelů just-in-time**, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi OpenAthens, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici OpenAthens na přístupovém panelu, můžete by měl být automaticky přihlášeni k OpenAthens, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

