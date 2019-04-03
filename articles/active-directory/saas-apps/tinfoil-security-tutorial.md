---
title: 'Kurz: Integrace Azure Active Directory s TINFOIL SECURITY | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a4e64bf6899d3384e0dadc9c2b4ce2fd5d092385
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2019
ms.locfileid: "58849426"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Kurz: Integrace Azure Active Directory s TINFOIL SECURITY

V tomto kurzu se dozvíte, jak integrovat TINFOIL SECURITY s Azure Active Directory (Azure AD).
Integrace TINFOIL SECURITY s Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k TINFOIL SECURITY.
* Uživatelům se automaticky přihlášeni k TINFOIL SECURITY (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s TINFOIL SECURITY, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* TINFOIL SECURITY jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje TINFOIL SECURITY **IDP** jednotné přihlašování zahájené pomocí

## <a name="adding-tinfoil-security-from-the-gallery"></a>Přidání TINFOIL SECURITY z Galerie

Konfigurace integrace TINFOIL SECURITY do služby Azure AD, budete muset přidat TINFOIL SECURITY z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat TINFOIL SECURITY z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **TINFOIL SECURITY**vyberte **TINFOIL SECURITY** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![TINFOIL SECURITY v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části, konfigurace a testování Azure AD jednotné přihlašování s TINFOIL SECURITY na základě testovací uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v TINFOIL SECURITY.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s TINFOIL SECURITY, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace aplikace TINFOIL SECURITY Single Sign-On](#configure-tinfoil-security-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele TINFOIL SECURITY](#create-tinfoil-security-test-user)**  – Pokud chcete mít protějšek Britta Simon TINFOIL SECURITY, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s TINFOIL SECURITY, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **TINFOIL SECURITY** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** oddílu, uživatel nebude muset provést libovolný krok, protože aplikace je už předem integrováno s Azure.

    ![TINFOIL SECURITY domény a adresy URL jednotného přihlašování – informace](common/preintegrated.png)

5. Aplikace TINFOIL SECURITY očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikony otevřete **atributy uživatele** dialogového okna.

        ![image](common/edit-attribute.png)

6. Kromě toho výše aplikace TINFOIL SECURITY očekává, že několik dalších atributů musí být předány zpět odpověď SAML. V **deklarace identity uživatelů** části na **atributy uživatele** dialogového okna, proveďte následující kroky pro přidání atributu tokenu SAML, jak je znázorněno v následující tabulka:

    | Název | Zdrojový atribut |
    | ------------------- | -------------|
    | ID účtu | UXXXXXXXXXXXXX |

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. V **zdrojový atribut** textové pole, vložte ID účtu hodnotu, která se zobrazí později v kurzu.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

7. V **podpisový certifikát SAML** klikněte na tlačítko **upravit** tlačítko Otevřít **podpisový certifikát SAML** dialogového okna.

    ![Upravit podpisového certifikátu SAML](common/edit-certificate.png)

8. V **podpisový certifikát SAML** tématu, zkopírujte **kryptografický otisk** a uložte ho do počítače.

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-thumbprint.png)

9. Na **nastavení TINFOIL SECURITY** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-tinfoil-security-single-sign-on"></a>Konfigurace aplikace TINFOIL SECURITY jednotného přihlašování

1. V okně jiné webové prohlížeče přihlaste jako správce serveru vaší společnosti TINFOIL SECURITY.

2. Na panelu nástrojů v horní části klikněte na tlačítko **Můj účet**.
   
    ![Řídicí panel](./media/tinfoil-security-tutorial/ic798971.png "řídicího panelu")

3. Klikněte na tlačítko **zabezpečení**.
   
    ![Zabezpečení](./media/tinfoil-security-tutorial/ic798972.png "zabezpečení")

4. Na **Single Sign-On** konfigurační stránce, proveďte následující kroky:
   
    ![Jednotné přihlašování](./media/tinfoil-security-tutorial/ic798973.png "jednotného přihlašování")
   
    a. Vyberte **povolit SAML**.
   
    b. Klikněte na tlačítko **ruční konfigurace**.
   
    c. V **SAML Post URL** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal
   
    d. V **otisku certifikátu SAML** textového pole vložte hodnotu **kryptografický otisk** zkopírovanou z **podpisový certifikát SAML** oddílu.
  
    e. Kopírování **ID vašeho účtu** hodnotu a vložte tuto hodnotu v **hodnota atributu** textového pole pod **přidat atribut** části webu Azure Portal.
   
    f. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole brittasimon@yourcompanydomain.extension. Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k TINFOIL SECURITY.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **TINFOIL SECURITY**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **TINFOIL SECURITY**.

    ![TINFOIL SECURITY odkaz v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-tinfoil-security-test-user"></a>Vytvořit testovacího uživatele TINFOIL SECURITY

Chcete-li povolit Azure AD uživatelům umožní přihlásit k TINFOIL SECURITY, musí být poskytnuty do TINFOIL SECURITY. V případě TINFOIL SECURITY zřizování je ruční úloha.

**K získání uživatele zřízené, proveďte následující kroky:**

1. Pokud uživatel je součástí účet organizace, budete muset [obraťte se na tým podpory TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) získat uživatelský účet vytvořený.

1. Pokud uživatel je běžný uživatel TINFOIL SECURITY SaaS, pak může uživatel přidat spolupracovníka k některému z webů uživatele. Tím se spustí proces odeslání pozvánky k zadané e-mailu k vytvoření nového uživatelského účtu TINFOIL SECURITY.

> [!NOTE]
> Další nástroje pro tvorbu účtu TINFOIL SECURITY uživatele nebo rozhraní API poskytovaných TINFOIL SECURITY můžete použít ke zřízení uživatelských účtů služby Azure AD.
> 

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici TINFOIL SECURITY na přístupovém panelu, můžete by měl být automaticky přihlášeni k TINFOIL SECURITY, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

