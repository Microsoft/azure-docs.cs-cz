---
title: 'Kurz: Integrace Azure Active Directory s rozvoji Linkedinem | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a zvýšení oprávnění LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49e5b6f52b92889ccc7c46f091ea1b90d43b0307
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64704797"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Kurz: Integrace Azure Active Directory s rozvoji Linkedinem

V tomto kurzu se dozvíte, jak integrovat zvýšení Linkedinu s Azure Active Directory (Azure AD).
Integrace zvýšení Linkedinu s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k rozvoji Linkedinem.
* Uživatelům se automaticky přihlášeni k rozvoji Linkedinem (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s rozvoji Linkedinem, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Rozvoji Linkedinem jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Rozvoji Linkedinem podporuje **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

* Rozvoji Linkedinem podporuje **JIT** zřizování uživatelů

* Rozvoji Linkedinem podporuje [ **automatizovaná** zřizování uživatelů](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Přidání rozvoji Linkedinem z Galerie

Pokud chcete nakonfigurovat integraci rozvoji Linkedinem do služby Azure AD, musíte doplnit rozvoji Linkedinem z Galerie váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat rozvoji Linkedinem z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **rozvoji Linkedinem**vyberte **rozvoji Linkedinem** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![LinkedIn zvýšit v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování pomocí Linkedinu zvýšit oprávnění na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v rozvoji Linkedinem.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s rozvoji Linkedinem, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace LinkedIn zvýšení Single Sign-On](#configure-linkedin-elevate-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele rozvoji Linkedinem](#create-linkedin-elevate-test-user)**  – Pokud chcete mít protějšek Britta Simon v Linkedinu zvýšení oprávnění, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s rozvoji Linkedinem, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **rozvoji Linkedinem** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![LinkedIn zvýšení domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte **Entity ID** hodnotu, bude zkopírujte hodnotu Entity ID z portálu Linkedin vysvětleny dále v tomto kurzu.

    b. V **adresy URL odpovědi** text, zadejte **Assertion Consumer přístupu (ACS) adresy Url** hodnotu, bude zkopírujte adresu Url Assertion Consumer přístupu (ACS) hodnotu z Linkedinu portálu vysvětleny dále v tomto kurzu.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![LinkedIn zvýšení domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. Rozvoji Linkedinem aplikace očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Následující snímek obrazovky ukazuje seznam výchozích atributů, přičemž **nameidentifier** je namapována na žádnou **user.userprincipalname**. Rozvoji Linkedinem aplikace očekává nameidentifier namapovat s **user.mail**, takže budete muset upravit mapování atributů kliknutím na ikonu úprav a změnit mapování atributů.

    ![image](common/edit-attribute.png)

7. Kromě toho výše rozvoji Linkedinem aplikace očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V části deklarace identity uživatelů na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Zdrojový atribut|
    | -------| -------------|
    | Oddělení | user.department |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. Na **nastavení Linkedinu zvýšení** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-linkedin-elevate-single-sign-on"></a>Konfigurace LinkedIn zvýšení jednotného přihlašování

1. V okně prohlížeče jiných webových přihlašování k vašemu tenantovi rozvoji Linkedinem jako správce.

1. V **centra pro účty**, klikněte na tlačítko **globální nastavení** pod **nastavení**. Kromě toho **zvýšení – zvýšení testu AAD** z rozevíracího seznamu.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klikněte na **nebo klikněte sem pro načítání a kopírování jednotlivých polí ve formuláři** a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Kopírování **Entity Id** a vložte ho do **identifikátor** textové pole **základní konfiguraci SAML** na webu Azure Portal.

    b. Kopírování **Assertion Consumer přístupu (ACS) adresy Url** a vložte ho do **adresy URL odpovědi** textové pole **základní konfiguraci SAML** na webu Azure Portal.

1. Přejděte na **nastavení Linkedinu správce** oddílu. Nahrajte soubor XML, který jste si stáhli z portálu Azure portal kliknutím na možnost nahrát XML soubor.

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Klikněte na tlačítko **na** pro povolení jednotného přihlašování. Stav jednotného přihlašování se změní z **Nepřipojeno** k **připojeno**

    ![Konfigurace jednotného přihlašování](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k rozvoji Linkedinem.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **rozvoji Linkedinem**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **rozvoji Linkedinem**.

    ![Zvýšit oprávnění Linkedinu propojení v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-linkedin-elevate-test-user"></a>Vytvoření rozvoji Linkedinem testovacího uživatele

LinkedIn zvýšit oprávnění aplikace podporuje pouze v době zřizování uživatelů a po ověření uživatele budou vytvořeny v aplikaci automaticky. Na správce stránky nastavení na portálu vyfiltrují rozvoji Linkedinem přepínač **automaticky přiřadit licence** na aktivní pouze v čase zřizování a to se také přiřadit licenci uživateli. Rozvoji Linkedinem také podporuje automatické zřizování uživatelů, další podrobnosti můžete najít [tady](linkedinelevate-provisioning-tutorial.md) o tom, jak nakonfigurovat automatické zřizování uživatelů.

   ![Vytváří se testovací uživatele služby Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici rozvoji Linkedinem na přístupovém panelu, můžete by měl být automaticky přihlášeni ke zvýšení LinkedIn, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)