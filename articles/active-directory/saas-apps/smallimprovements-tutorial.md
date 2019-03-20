---
title: 'Kurz: Integrace Azure Active Directory s menších vylepšení | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a menších vylepšení.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 1b59d1c030df95a650124f9e9ea34e06db7d08af
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180853"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Kurz: Integrace Azure Active Directory s menších vylepšení

V tomto kurzu se dozvíte, jak integrovat menších vylepšení Azure Active Directory (Azure AD).
Malá vylepšení integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup do menších vylepšení.
* Uživatelům se automaticky přihlášeni do menších vylepšení (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s menších vylepšení, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Malá vylepšení jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Malá vylepšení podporuje **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-small-improvements-from-the-gallery"></a>Přidání menších vylepšení z Galerie

Konfigurace integrace menších vylepšení do služby Azure AD, budete muset přidat malá vylepšení v galerii na váš seznam spravovaných aplikací SaaS.

**Přidání menších vylepšení z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **menších vylepšení**vyberte **menších vylepšení** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Malá vylepšení v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí menších vylepšení podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v menších vylepšení.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s menších vylepšení, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Nakonfigurujte malá vylepšení Single Sign-On](#configure-small-improvements-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele menších vylepšení](#create-small-improvements-test-user)**  – Pokud chcete mít protějšek Britta Simon v menších vylepšení, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s menších vylepšení, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **menších vylepšení** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Malá vylepšení domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.small-improvements.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory malá vylepšení klienta](mailto:support@small-improvements.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

6. Na **nastavení menších vylepšení** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-small-improvements-single-sign-on"></a>Nakonfigurujte malá vylepšení jednotného přihlašování

1. V jiném okně prohlížeče Přihlaste se k webu společnosti menších vylepšení jako správce.

1. Na stránce hlavní řídicí panel, klikněte na tlačítko **správu** tlačítko na levé straně.

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klikněte na tlačítko **SAML SSO** tlačítko **integrace** oddílu.

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Na stránce nastavení jednotného přihlašování proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. V **koncový bod HTTP** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. Otevřete stažený certifikát v poznámkovém bloku, zkopírujte jeho obsah a vložte jej do **x509 certifikát** textového pole. 

    c. Pokud chcete mít jednotného přihlašování a přihlašovací formulář možnost ověřování k dispozici pro uživatele, zkontrolujte **povolte přístup přes přihlašovací jméno/heslo příliš** možnost.  

    d. Zadejte odpovídající hodnotu název tlačítka pro přihlášení SSO v **SAML výzvy** textového pole.  

    e. Klikněte na **Uložit**.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure tím, že uděluje přístup do menších vylepšení.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **menších vylepšení**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **menších vylepšení**.

    ![Odkaz malá vylepšení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-small-improvements-test-user"></a>Vytvoření menších vylepšení testovacího uživatele

Pokud chcete povolit Azure AD uživatelům přihlášení do menších vylepšení, musí být poskytnuty do menších vylepšení. V případě menších vylepšení zřizování je ruční úloha.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlašování k webu společnosti menších vylepšení jako správce.

1. Na domovské stránce, přejděte do nabídky na levé, klikněte na tlačítko **správu**.

1. Klikněte na tlačítko **adresář uživatele** tlačítko z části Správa uživatelů.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klikněte na tlačítko **přidat uživatele**.

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Na **Add Users** dialogového okna, proveďte následující kroky: 

    ![Vytváří se testovací uživatele služby Azure AD](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Zadejte **křestní jméno** uživatele, jako je **Britta**.

    b. Zadejte **příjmení** uživatele, jako je **Simon**.

    c. Zadejte **e-mailu** uživatele, jako je **brittasimon@contoso.com**.

    d. Můžete také zadat osobní zprávu v **odeslat e-mail s oznámením** pole. Pokud nechcete, aby se odeslat oznámení, poté zrušte zaškrtnutí tohoto políčka.

    e. Klikněte na tlačítko **vytvořit uživatele**.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici menších vylepšení na přístupovém panelu, vám by měl být automaticky přihlášeni do menších vylepšení, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)