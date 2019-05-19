---
title: 'Kurz: Integrace Azure Active Directory s Trello | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Trello.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: cd5ae365-9ed6-43a6-920b-f7814b993949
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 453827b42d12333fd4f27761e7f73484fb749532
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "65865789"
---
# <a name="tutorial-azure-active-directory-integration-with-trello"></a>Kurz: Integrace Azure Active Directory s Trello

V tomto kurzu se dozvíte, jak integrovat Trello s Azure Active Directory (Azure AD).
Trello integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Trello.
* Můžete povolit uživatelům, aby se automaticky přihlášeni k Trellu (jednotné přihlašování) s jejich účty Azure AD.
* Můžete spravovat své účty v jednom centrálním místě: na webu Azure portal.

Další informace o integraci aplikací SaaS v Azure AD najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Trello, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [zkušební verze na jeden měsíc](https://azure.microsoft.com/pricing/free-trial/).
* Jeden znak na podporou předplatné Trello.

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Trello podporuje iniciovaného Zprostředkovatelem přihlašování a zprostředkovatele identity – jednotné přihlašování

* Trello podporuje JIT zřizování uživatelů

## <a name="add-trello-from-the-gallery"></a>Přidání Trello z Galerie

Pokud chcete nakonfigurovat integraci Trello do služby Azure AD, nejprve přidáte Trello z Galerie na váš seznam spravovaných aplikací SaaS.

Chcete-li přidat Trello z galerie, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com), v levém podokně, vyberte **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Vyberte **podnikové aplikace**a pak vyberte **všechny aplikace**.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Trello**a pak vyberte **Trello** v podokně výsledků.

5. Vyberte **přidat** tlačítko pro přidání aplikace.

     ![Trello v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Trello podle testovacího uživatele volá **Britta Simon**.

Pro jednotné přihlašování pro práci budete muset vytvořit propojení mezi uživatele služby Azure AD a související uživatel v Trellu.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Trello, které potřebujete k dokončení následujících stavebních bloků:

1. [Konfigurace služby Azure AD jednotného přihlašování](#configure-azure-ad-single-sign-on) aby uživatelé mohli tuto funkci používat.
2. [Konfigurace Trello jednotného přihlašování](#configure-trello-single-sign-on) ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. [Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user) k otestování služby Azure AD jednotné přihlašování s Britta Simon.
4. [Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user) umožňující Britta Simon používat Azure AD jednotného přihlašování.
5. [Vytvoření zkušebního uživatele Trello](#create-a-trello-test-user) mít protějšek Britta Simon trello, který je propojený s Azure AD zastoupení uživatele.
6. [Otestovat jednotné přihlašování](#test-single-sign-on) k ověření, že konfigurace funguje.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

> [!NOTE]
> Měli byste obdržet **\<enterprise\>** zkráceného názvu stránky z Trella. Pokud nemáte hodnotu zkráceného názvu stránky, obraťte se [tým podpory Trello](mailto:support@trello.com) zobrazíte popisu pro vaši firmu.

Ke konfiguraci Azure AD jednotné přihlašování s Trello, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Trello** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. V **vybrat jedinou metodu přihlašování** dialogovém okně vyberte **SAML** povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavit jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** ikony otevřete **základní konfiguraci SAML** dialogové okno.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. V **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v režimu zahájené pomocí IDP, proveďte následující kroky:

    ![Trello domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** pole, zadejte adresu URL pomocí následujícího vzorce: `https://trello.com/auth/saml/metadata`

    b. V **adresy URL odpovědi** pole, zadejte adresu URL pomocí následujícího vzorce: `https://trello.com/auth/saml/consume/<enterprise>`

5. Vyberte **nastavit další adresy URL**a pak proveďte následující kroky, pokud chcete nakonfigurovat aplikace v režimu iniciovaného Zprostředkovatelem přihlašování:

    ![Trello domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** pole, zadejte adresu URL pomocí následujícího vzorce:  `https://trello.com/auth/saml/login/<enterprise>`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Tyto hodnoty aktualizovat s identifikátorem skutečné odpovědi adresy URL a přihlašovací adresa URL. Obraťte se [tým podpory Trello klienta](mailto:support@trello.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají v **základní konfiguraci SAML** části webu Azure Portal.

6. Trello aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** stránky, vyberte **upravit** tlačítko Otevřít **atributy uživatele** dialogové okno.

    ![Dialogové okno atributů uživatele](common/edit-attribute.png)

7. V **deklarace identity uživatelů** tématu **atributy uživatele** dialogového okna nastavte atribut tokenu SAML, jak je znázorněno na předchozím obrázku. Pak proveďte následující kroky:

    | Název |  Zdrojový atribut|
    | --- | --- |
    | User.Email | user.mail |
    | User.FirstName | user.givenname |
    | User.LastName | user.surname |

    a. Vyberte **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogové okno.

    ![Dialogové okno deklarace identity uživatele](common/new-save-attribute.png)

    ![Spravovat deklarace identity uživatele](common/new-attribute-details.png)

    b. V **název** zadejte název atributu, který je zobrazený pro tento řádek.

    c. Ponechte **Namespace** prázdné.

    d. Pro **zdroj**vyberte **atribut**.

    e. V **zdrojový atribut** seznamu, zadejte hodnotu atributu, který je zobrazený pro tento řádek.

    f. Vyberte **OK**.

    g. Vyberte **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** vyberte **Stáhnout** ke stažení **certifikát (Base64)**  z se zadanými možnostmi podle vašich požadavků. Pak ho uložte na vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. Na **nastavení Trello** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-trello-single-sign-on"></a>Konfigurace Trello jednotného přihlašování

Konfigurace jednotného přihlašování na straně Trello, nejdřív odesílat na stažený **certifikát (Base64)** a zkopírovat z portálu Azure portal do adresy URL [tým podpory Trello](mailto:support@trello.com). Zajišťují, že je správně nastaveno připojení SAML SSO na obou stranách.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. V **uživatele** dialogové okno pole, proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte "brittasimon@yourcompanydomain.extension". Například v takovém případě můžete například zadat "BrittaSimon@contoso.com".

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a potom si poznamenejte hodnotu, která se zobrazí **heslo** pole.

    d. Vyberte **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k Trello.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Trello**.

    ![Okno podnikových aplikací](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Trello**.

    ![V seznamu aplikací na odkaz Trello](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Vyberte **přidat uživatele** tlačítko. Potom v **přidat přiřazení** dialogu **uživatelů a skupin**.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogu **Britta Simon** v seznamu uživatelů. Klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML, pak v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu. Klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno, vyberte **přiřadit** tlačítko.

### <a name="create-a-trello-test-user"></a>Vytvoření zkušebního uživatele Trello

V této části vytvořte uživatele Britta Simon v Trellu. Trello podporuje pouze v čase zřizování uživatelů, která je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v Trellu, se po ověření vytvoří nový.

> [!NOTE]
> Pokud je potřeba ručně vytvořit uživatele, obraťte se [tým podpory Trello](mailto:support@trello.com).

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace s použitím portálu MyApps.

Při výběru dlaždice Trello portálu MyApps vám by měl být automaticky přihlášeni k Trellu. Další informace o portálu Moje aplikace najdete v tématu [novinky na portálu MyApps?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další materiály

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

