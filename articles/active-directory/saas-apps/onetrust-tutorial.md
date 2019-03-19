---
title: 'Kurz: Integrace Azure Active Directory s Software pro správu ochrany osobních údajů OneTrust | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Software pro správu OneTrust ochrany osobních údajů.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 71c2b6d0-3d28-4130-a2c8-1e72ab3d5814
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: f65bb7506b112828a49f9410f4790932f0abbfbc
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58177530"
---
# <a name="tutorial-azure-active-directory-integration-with-onetrust-privacy-management-software"></a>Kurz: Integrace Azure Active Directory s Software pro správu OneTrust ochrany osobních údajů

V tomto kurzu se dozvíte, jak integrovat Software pro správu OneTrust ochrany osobních údajů Azure Active Directory (Azure AD).
Software pro správu ochrany osobních údajů OneTrust integrace s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k softwaru pro správu OneTrust ochrany osobních údajů.
* Uživatelům se automaticky přihlášeni k OneTrust Software pro správu ochrany osobních údajů (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Software pro správu OneTrust ochrany osobních údajů, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Software pro správu ochrany osobních údajů OneTrust jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Software pro správu ochrany osobních údajů OneTrust podporuje **SP** a **IDP** jednotné přihlašování zahájené pomocí

* Software pro správu ochrany osobních údajů OneTrust podporuje **JIT** zřizování uživatelů

## <a name="adding-onetrust-privacy-management-software-from-the-gallery"></a>Přidání Software pro správu OneTrust ochrany osobních údajů z Galerie

Konfigurace integrace Software pro správu OneTrust ochrany osobních údajů do služby Azure AD, budete muset přidat Software pro správu OneTrust ochrany osobních údajů z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Software pro správu OneTrust ochrany osobních údajů z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Software pro správu ochrany osobních údajů OneTrust**vyberte **Software pro správu ochrany osobních údajů OneTrust** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Software pro správu OneTrust ochrany osobních údajů v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování služby Azure AD jednotné přihlašování s Software pro správu OneTrust ochrany osobních údajů na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Software pro správu OneTrust ochrany osobních údajů.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Software pro správu OneTrust ochrany osobních údajů, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace OneTrust ochrany osobních údajů správy softwaru jednotného přihlašování](#configure-onetrust-privacy-management-software-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit Software pro správu OneTrust ochrany osobních údajů testovacího uživatele](#create-onetrust-privacy-management-software-test-user)**  – Pokud chcete mít protějšek Britta Simon Software pro správu ochrany osobních údajů OneTrust, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Chcete-li nakonfigurovat Software pro správu OneTrust ochrany osobních údajů Azure AD jednotného přihlašování, postupujte následovně:

1. V [webu Azure portal](https://portal.azure.com/)na **Software pro správu ochrany osobních údajů OneTrust** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Doméně pro správu softwaru OneTrust ochrany osobních údajů a adres URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL: `https://www.onetrust.com/saml2`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<subdomain>.onetrust.com/auth/consumerservice`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Doméně pro správu softwaru OneTrust ochrany osobních údajů a adres URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<subdomain>.onetrust.com/auth/login`

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečnou adresu URL adresy URL odpovědi a přihlašování. Kontakt [tým podpory OneTrust ochrany osobních údajů správy softwarového klienta](mailto:support@onetrust.com) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

7. Na **nastavit Software pro správu ochrany osobních údajů OneTrust** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-onetrust-privacy-management-software-single-sign-on"></a>Konfigurace OneTrust ochrany osobních údajů správy softwaru jednotného přihlašování

Ke konfiguraci jednotného přihlašování na **Software pro správu ochrany osobních údajů OneTrust** straně, je nutné odeslat na stažený **kód XML metadat federace** a odpovídající zkopírován adresy URL z webu Azure portal [ Tým podpory Software pro správu ochrany osobních údajů OneTrust](mailto:support@onetrust.com). Nastavují tohoto nastavení můžete mít správně nastavené na obou stranách připojení SAML SSO.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu na Software pro správu OneTrust ochrany osobních údajů.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Software pro správu ochrany osobních údajů OneTrust**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Software pro správu ochrany osobních údajů OneTrust**.

    ![Software pro správu ochrany osobních údajů OneTrust odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-onetrust-privacy-management-software-test-user"></a>Vytvořit Software pro správu OneTrust ochrany osobních údajů testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Software pro správu OneTrust ochrany osobních údajů. Software pro správu ochrany osobních údajů OneTrust podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Software pro správu OneTrust ochrany osobních údajů, vytvoří se nový po ověření.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Software pro správu ochrany osobních údajů OneTrust](mailto:support@onetrust.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Software pro správu OneTrust ochrany osobních údajů na přístupovém panelu, vám by měl být automaticky přihlášeni na Software pro správu OneTrust ochrany osobních údajů u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

