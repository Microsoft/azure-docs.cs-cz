---
title: 'Kurz: Integrace Azure Active Directory s Allocadia | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Allocadia.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c415fc55-6dc1-49f2-a8a2-2fc6e3790d65
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f02564115cd856560535bf98e8ce825dc50f7550
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175213"
---
# <a name="tutorial-azure-active-directory-integration-with-allocadia"></a>Kurz: Integrace Azure Active Directory s Allocadia

V tomto kurzu se dozvíte, jak integrovat Allocadia s Azure Active Directory (Azure AD).
Allocadia integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Allocadia.
* Můžete povolit uživatelům být automaticky přihlášeni k Allocadia (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Allocadia, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Allocadia jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Allocadia **IDP** jednotné přihlašování zahájené pomocí
* Podporuje Allocadia **JIT** zřizování uživatelů

## <a name="adding-allocadia-from-the-gallery"></a>Přidání Allocadia z Galerie

Konfigurace integrace Allocadia do služby Azure AD, budete muset přidat Allocadia z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Allocadia z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Allocadia**vyberte **Allocadia** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Allocadia v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Allocadia podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Allocadia.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Allocadia, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Allocadia Single Sign-On](#configure-allocadia-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Allocadia](#create-allocadia-test-user)**  – Pokud chcete mít protějšek Britta Simon Allocadia, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Allocadia, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Allocadia** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **nastavte si jednotné přihlašování pomocí SAML** stránce, proveďte následující kroky:

    ![Allocadia domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    Pro testovací prostředí –  `https://na2standby.allocadia.com`

    Pro produkční prostředí – `https://na2.allocadia.com`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    Pro testovací prostředí – `https://na2standby.allocadia.com/allocadia/saml/SSO`

    Pro produkční prostředí – `https://na2.allocadia.com/allocadia/saml/SSO`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty se skutečné identifikátorem a adresa URL odpovědi. Kontakt [tým podpory Allocadia klienta](mailto:support@allocadia.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. Allocadia aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

6. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky: 

    | Název | Zdrojový atribut|
    | ------------------- | -------------------- |
    | jméno | user.givenname |
    | Příjmení | user.surname |
    | e-mail | user.mail |
    | | |

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

8. Na **nastavení Allocadia** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-allocadia-single-sign-on"></a>Konfigurace Allocadia jednotné přihlašování

Ke konfiguraci jednotného přihlašování na **Allocadia** straně, je nutné odeslat na stažený **kód XML metadat federace** a vhodné zkopírovaný adresy URL z webu Azure portal [tým podpory Allocadia](mailto:support@allocadia.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon k udělení přístupu k Allocadia použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Allocadia**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Allocadia**.

    ![Odkaz Allocadia v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-allocadia-test-user"></a>Vytvoření Allocadia testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Allocadia. Allocadia podporuje zřizování uživatelů v čase, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Allocadia, vytvoří se nový po ověření.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Allocadia na přístupovém panelu, můžete by měl být automaticky přihlášeni k Allocadia, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
