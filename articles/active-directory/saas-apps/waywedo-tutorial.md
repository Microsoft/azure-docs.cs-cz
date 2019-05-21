---
title: 'Kurz: Integrace Azure Active Directory s způsob děláme | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a způsob, jak vidíme.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 541be5466b65705daa0485976eab3df8eb3d707f
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905354"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Kurz: Integrace Azure Active Directory s způsob děláme

V tomto kurzu se dozvíte, jak integrovat způsob, jak jsme se s Azure Active Directory (Azure AD).
Integrace způsob, jak jsme se s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k způsob děláme.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k způsob děláme (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s způsob děláme, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Způsob, jak jsme jednotné přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Způsob, jak děláme podporuje **SP** jednotné přihlašování zahájené pomocí

* Způsob, jak děláme podporuje **JIT** zřizování uživatelů

## <a name="adding-way-we-do-from-the-gallery"></a>Přidání způsob, jak můžeme udělat z Galerie

Pokud chcete nakonfigurovat integraci děláme způsob, jak do služby Azure AD, musíte doplnit způsob, jak můžeme udělat z Galerie váš seznam spravovaných aplikací SaaS.

**Děláme způsob, jak přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **způsob děláme**vyberte **způsob děláme** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Způsob, jak jsme se v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí způsob děláme podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v způsob, jak jsme to.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s způsob děláme, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurovat způsob, jak jsme jednotné přihlašování](#configure-way-we-do-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvoření způsob, jak jsme testovací uživatele](#create-way-we-do-test-user)**  – Pokud chcete mít protějšek Britta Simon způsob, jak to je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s způsob děláme, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **způsob děláme** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Způsob, jak můžeme udělat domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory způsob, jak můžeme udělat klienta](mailto:support@waywedo.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Raw)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificateraw.png)

6. Na **nastavit způsob děláme** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-way-we-do-single-sign-on"></a>Konfigurovat způsob, jak jsme jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k způsob, jak můžeme udělat jako správce zabezpečení.

2. Klikněte na tlačítko **osoba ikonu** v pravém horním rohu jakékoli stránky v způsob, jak jsme to klikněte **účtu** v rozevírací nabídce.

    ![Způsob, jak děláme účtu](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

3. Klikněte na tlačítko **ikonu nabídky** otevřete navigační nabídky nabízených oznámení a klikněte na tlačítko **Single Sign On**.

    ![Způsob, jak můžeme udělat jeden](./media/waywedo-tutorial/tutorial_waywedo_single.png)

4. Na **nastavení přihlašování –** stránce, proveďte následující kroky:

    ![Způsob, jak děláme uložit](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klikněte na tlačítko **zapnout jednotné přihlašování** přepnutím **Ano** povolit jednotné přihlašování.

    b. V **jednotné přihlašování – název** textového pole zadejte své jméno.

    c. V **Entity ID** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    d. V **adresu URL jednotného přihlašování SAML** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Nahrajte certifikát po kliknutí **vyberte** vedle **certifikát**.

    f. **Volitelná nastavení** -
    
    * Hesla – povolit po tato možnost je zakázaná, pravidelné heslo funkce pro způsob, jak můžeme udělat tak, aby uživatelé mohou použít pouze jednotné přihlašování.

    * Povolení automatického zřizování – když je tato možnost povolena, bude e-mailovou adresu používá k přihlašování do seznamu uživatelů v způsob, jak jsme to automaticky porovnání. Pokud e-mailovou adresu se neshoduje s aktivní uživatel v způsob děláme, automaticky se přidá nový uživatelský účet pro osobu, která se přihlašujete, požadování všechny chybějící informace.

      > [!NOTE]
      > Přidané prostřednictvím jednotného přihlašování uživatele jsou přidány jako obecný uživatel a nejsou přiřazena role v systému. Správce se zúčastnit a změnit jejich role zabezpečení jako editor nebo správce a můžete také přiřadit organizační diagram jednoho nebo několika rolí. 

    g. Klikněte na tlačítko **Uložit** k uchování vašeho nastavení.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu způsob, jak můžeme udělat.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **způsob děláme**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **způsob děláme**.

    ![Způsob, jak děláme odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-way-we-do-test-user"></a>Vytvoření způsob děláme testovacího uživatele

V této části se vytvoří uživateli Britta Simon v způsob, jak jsme to. Způsob, jak děláme podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi způsob děláme, vytvoří se nový po ověření.

> [!Note]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory způsob, jak můžeme udělat klienta](mailto:support@waywedo.com).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici způsob, jak můžeme udělat na přístupovém panelu, vám by měl být automaticky přihlášeni způsob, jak můžeme udělat u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

