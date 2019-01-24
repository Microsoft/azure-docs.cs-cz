---
title: 'Kurz: Integrace Azure Active Directory s Firstbird | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Firstbird.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: acab1200-32d3-4f4b-953f-f2a7e812b6a3
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: d11674632599afa190237b2a4a516d7cce6e2150
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810240"
---
# <a name="tutorial-azure-active-directory-integration-with-firstbird"></a>Kurz: Integrace Azure Active Directory s Firstbird

V tomto kurzu se dozvíte, jak integrovat Firstbird s Azure Active Directory (Azure AD).
Firstbird integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Firstbird.
* Můžete povolit uživatelům být automaticky přihlášeni k Firstbird (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Firstbird, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Firstbird jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Firstbird **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* Podporuje Firstbird **JIT** zřizování uživatelů

## <a name="adding-firstbird-from-the-gallery"></a>Přidání Firstbird z Galerie

Konfigurace integrace Firstbird do služby Azure AD, budete muset přidat Firstbird z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Firstbird z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select_azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise_applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add_new_app.png)

4. Do vyhledávacího pole zadejte **Firstbird**vyberte **Firstbird** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Firstbird v seznamu výsledků](common/search_new_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfiguraci a testování Azure AD jednotné přihlašování s [název aplikace] podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v [název aplikace].

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s [název aplikace], které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Firstbird Single Sign-On](#configure-firstbird-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Firstbird](#create-firstbird-test-user)**  – Pokud chcete mít protějšek Britta Simon Firstbird, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s [název aplikace], proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Firstbird** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select_sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select_saml_option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit_urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Firstbird domény a adresy URL jednotného přihlašování – informace](common/idp_intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company-domain>.auth.1brd.com/saml/sp`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<company-domain>.auth.1brd.com/saml/callback`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Firstbird domény a adresy URL jednotného přihlašování – informace](common/metadata_upload_additional_signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<company-domain>.1brd.com/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL identifikátor, adresa URL odpovědi a přihlašování. Kontakt [tým podpory Firstbird klienta](mailto:support@firstbird.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Firstbird aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit_attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, nakonfigurovat atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky:

    | Název | Zdrojový atribut|
    | ---------------| --------- |
    | křestní_jméno | `user.givenname` |
    | Příjmení | `user.surname` |
    | e-mail | `user.mail` |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new_save_attribute.png)

    ![image](common/new_attribute_details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** Stáhnout **kód XML metadat federace** a uložte ho do počítače.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

### <a name="configure-firstbird-single-sign-on"></a>Konfigurace Firstbird jednotné přihlašování

Po dokončení těchto kroků, pošlete prosím Firstbird kód XML metadat federace v žádosti o podporu prostřednictvím e-mailovou adresu k [ support@firstbird.com ](mailto:support@firstbird.com) s předmětem: "Konfigurace jednotného přihlašování".

Firstbird potom uložení konfigurace v systému odpovídajícím způsobem a aktivovat jednotné přihlašování pro váš účet. Za členem pracovníkům podpory bude vás kontaktovat a ověřit konfiguraci.

> [!NOTE]
> Musíte mít možnost jednotného přihlašování, které jsou součástí vaší smlouvy.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new_user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user_properties.png)

    a. V **název** zadejte **BrittaSimon**.

    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Firstbird použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Firstbird**.

    ![Okno aplikace organizace](common/enterprise_applications.png)

2. V seznamu aplikace zadejte a vyberte **Firstbird**.

    ![Odkaz Firstbird v seznamu aplikací](common/all_applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users_groups_blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add_assign_user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-firstbird-test-user"></a>Vytvoření Firstbird testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Firstbird. Firstbird podporuje just-in-time zřizování, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Firstbird, vytvoří se při pokusu o přístup k Firstbird nový.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Firstbird na přístupovém panelu, můžete by měl být automaticky přihlášeni k Firstbird, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
