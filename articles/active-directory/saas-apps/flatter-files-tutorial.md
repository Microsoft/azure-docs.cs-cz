---
title: 'Kurz: Integrace Azure Active Directory s plošší souborů | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a plošší soubory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f86fe5e3-0e91-40d6-869c-3df6912d27ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.openlocfilehash: c18678e211b8b1f8682215a7314309166ebb02cf
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772003"
---
# <a name="tutorial-azure-active-directory-integration-with-flatter-files"></a>Kurz: Integrace Azure Active Directory s plošší soubory

V tomto kurzu se dozvíte, jak integrovat plošší soubory pomocí Azure Active Directory (Azure AD).
Integrace plošší soubory s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k souborům plošší.
* Uživatelům se automaticky přihlášeni k plošší soubory (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s plošší soubory, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Plošší soubory jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Plošší podporuje soubory **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-flatter-files-from-the-gallery"></a>Přidání plošší soubory z Galerie

Konfigurace integrace plošší soubory do služby Azure AD, budete muset přidat plošší soubory z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat plošší soubory z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **plošší soubory**vyberte **plošší soubory** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Plošší soubory v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotného přihlašování se podle testu uživateli plošší soubory **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v plošší soubory.

Nakonfigurovat a otestovat Azure AD jednotného přihlašování se plošší soubory, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace plošší soubory Single Sign-On](#configure-flatter-files-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele plošší soubory](#create-flatter-files-test-user)**  – Pokud chcete mít protějšek Britta Simon plošší souborů, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotného přihlašování se plošší soubory, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **plošší soubory** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![Plošší soubory domény a adresy URL jednotného přihlašování – informace](common/preintegrated.png)

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavit plošší soubory** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-flatter-files-single-sign-on"></a>Konfigurace plošší soubory jednotného přihlašování

1. Přihlášení do aplikace plošší soubory jako správce.

2. Klikněte na tlačítko **řídicí panel**. 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_05.png)  

3. Klikněte na tlačítko **nastavení**a pak proveďte následující kroky na **společnosti** kartu: 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_06.png)  
    
    a. Vyberte **používat protokol SAML 2.0 pro ověřování**.
    
    b. Klikněte na tlačítko **konfigurace SAML**.

4. Na **konfigurace SAML** dialogového okna, proveďte následující kroky: 
   
    ![Konfigurace jednotného přihlašování](./media/flatter-files-tutorial/tutorial_flatter_files_08.png)  
   
    a. V **domény** textového pole zadejte registrované domény.
   
    >[!NOTE]
    >Pokud nemáte k dispozici registrované domény ještě, kontaktujte tým prostřednictvím podpory plošší soubory [ support@flatterfiles.com ](mailto:support@flatterfiles.com). 
    
    b. V **adresa URL zprostředkovatele Identity** textového pole vložte hodnotu **přihlašovací adresa URL** který jste zkopírovali formuláře webu Azure portal.
   
    c.  V poznámkovém bloku otevřete certifikát kódováním base-64, zkopírujte obsah ho do schránky a a vložte ho do **certifikát poskytovatele Identity** textového pole.

    d. Klikněte na tlačítko **aktualizace**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k souborům plošší.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **plošší soubory**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **plošší soubory**.

    ![Odkaz plošší soubory v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-flatter-files-test-user"></a>Vytvořit testovacího uživatele plošší soubory

Cílem této části je vytvořte uživatele Britta Simon v plošší soubory.

**Vytvořte uživatele v plošší soubory jako Britta Simon, proveďte následující kroky:**

1. Přihlaste se k vaší **plošší soubory** společnosti serveru jako správce.

2. V navigačním podokně na levé straně klikněte na tlačítko **nastavení**a potom klikněte na tlačítko **uživatelé** kartu.
   
    ![Vytvoření plošší soubory uživatele](./media/flatter-files-tutorial/tutorial_flatter_files_09.png)

3. Klikněte na tlačítko **přidat uživatele**. 

4. Na **přidat uživatele** dialogového okna, proveďte následující kroky:
   
    ![Vytvoření plošší soubory uživatele](./media/flatter-files-tutorial/tutorial_flatter_files_10.png)

    a. V **křestní jméno** textové pole, typ **Britta**.
   
    b. V **příjmení** textové pole, typ **Simon**. 
   
    c. V **e-mailovou adresu** textového pole zadejte Haniny e-mailovou adresu na webu Azure Portal.
   
    d. Klikněte na **Submit** (Odeslat).   


### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici plošší soubory na přístupovém panelu, vám by měl být automaticky přihlášeni plošší souborů, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

