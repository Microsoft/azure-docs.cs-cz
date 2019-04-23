---
title: 'Kurz: Integrace Azure Active Directory s TextMagic | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 66d0d791e4f32fc0828d400db7b59d836d221aee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60315619"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Kurz: Integrace Azure Active Directory s TextMagic

V tomto kurzu se dozvíte, jak integrovat TextMagic s Azure Active Directory (Azure AD).
TextMagic integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TextMagic.
* Můžete povolit uživatelům být automaticky přihlášeni k TextMagic (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TextMagic, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* TextMagic jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje TextMagic **IDP** jednotné přihlašování zahájené pomocí
* Podporuje TextMagic **JIT** zřizování uživatelů

## <a name="adding-textmagic-from-the-gallery"></a>Přidání TextMagic z Galerie

Konfigurace integrace TextMagic do služby Azure AD, budete muset přidat TextMagic z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TextMagic z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TextMagic**vyberte **TextMagic** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![TextMagic v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí TextMagic podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TextMagic.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TextMagic, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace TextMagic Single Sign-On](#configure-textmagic-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele TextMagic](#create-textmagic-test-user)**  – Pokud chcete mít protějšek Britta Simon TextMagic, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TextMagic, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TextMagic** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![TextMagic domény a adresy URL jednotného přihlašování – informace](common/idp-identifier.png)

    V **identifikátor** textové pole, zadejte adresu URL:  `https://my.textmagic.com/saml/metadata`

5. Vaše aplikace TextMagic očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Očekává, že aplikace TextMagic **nameidentifier** namapovat s **user.mail**, takže budete muset upravit mapování atributů po kliknutí na **upravit** ikonu a změňte mapování atributů.

    ![image](common/edit-attribute.png)

6. Kromě toho výše TextMagic aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Name |   Zdrojový atribut| Obor názvů  |
    | --------------- | --------------- | --------------- |
    | Společnosti | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Jméno               | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Příjmení            | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | telefon               | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. V **Namespace** textového pole zadejte hodnotu oboru názvů, který je zobrazený pro tento řádek.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

8. Na **nastavení TextMagic** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-textmagic-single-sign-on"></a>Konfigurace TextMagic jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti TextMagic jako správce.

2. Vyberte **nastavení účtu** podle uživatelského jména.

    ![TextMagic Configuration](./media/textmagic-tutorial/config1.png)

3. Klikněte na **jednotné přihlašování (SSO)** kartě a vyplňte následující pole:  

    ![TextMagic Configuration](./media/textmagic-tutorial/config2.png)

    a. V **zprostředkovatele Identity Entity ID:** textového pole vložte hodnotu **Azure AD identifikátor**, který jste zkopírovali z portálu Azure portal.

    b. V **zprostředkovatele Identity adresu URL jednotného přihlašování:** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    c. V **zprostředkovatele Identity adresa URL cíle na úrovni služby:** textového pole vložte hodnotu **odhlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    d. Otevřete váš **certifikáty s kódováním base-64** v poznámkovém bloku stáhnout z webu Azure portal, zkopírujte obsah ho do schránky a vložte ho do **x509 veřejný certifikát:** textového pole.

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
  
    b. V **uživatelské jméno** typ pole **brittasimon@yourcompanydomain.extension**. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k TextMagic použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **TextMagic**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TextMagic**.

    ![Odkaz TextMagic v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-textmagic-test-user"></a>Vytvoření TextMagic testovacího uživatele

Aplikace podporuje **čas zřizování uživatelů podle potřeby** a po ověření uživatele budou vytvořeny v aplikaci automaticky. Je potřeba vyplnit informace jednou při prvním přihlášení k aktivaci dílčí účet do systému.
Neexistuje žádná položka akce pro vás v této části.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici TextMagic na přístupovém panelu, můžete by měl být automaticky přihlášeni k TextMagic, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)