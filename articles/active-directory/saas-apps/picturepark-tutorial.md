---
title: 'Kurz: Integrace Azure Active Directory s Picturepark | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 7a4db08a342bc4004d4957e26353ef810ebae4d3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689591"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Kurz: Integrace Azure Active Directory s Picturepark

V tomto kurzu se dozvíte, jak integrovat Picturepark s Azure Active Directory (Azure AD).
Picturepark integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Picturepark.
* Můžete povolit uživatelům být automaticky přihlášeni k Picturepark (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Picturepark, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Picturepark jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Picturepark **SP** jednotné přihlašování zahájené pomocí

## <a name="adding-picturepark-from-the-gallery"></a>Přidání Picturepark z Galerie

Konfigurace integrace Picturepark do služby Azure AD, budete muset přidat Picturepark z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Picturepark z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Picturepark**vyberte **Picturepark** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Picturepark v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Picturepark podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Picturepark.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Picturepark, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Picturepark Single Sign-On](#configure-picturepark-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Picturepark](#create-picturepark-test-user)**  – Pokud chcete mít protějšek Britta Simon Picturepark, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Picturepark, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Picturepark** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, proveďte následující kroky:

    ![Picturepark domény a adresy URL jednotného přihlašování – informace](common/sp-identifier.png)

    a. V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<companyname>.picturepark.com`

    b. V **identifikátor (Entity ID)** textové pole, zadejte adresu URL, pomocí následujícího vzorce:

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Tyto hodnoty nejsou skutečný. Aktualizujte tyto hodnoty skutečné přihlašovací adresu URL a identifikátor. Kontakt [tým podpory Picturepark klienta](https://picturepark.com/about/contact/) k získání těchto hodnot. Můžete také odkazovat na tyto vzory se dají ukazuje **základní konfiguraci SAML** části webu Azure Portal.

5. V **podpisový certifikát SAML** klikněte na tlačítko **upravit** tlačítko Otevřít **podpisový certifikát SAML** dialogového okna.

    ![Upravit podpisového certifikátu SAML](common/edit-certificate.png)

6. V **podpisový certifikát SAML** tématu, zkopírujte **kryptografický otisk** a uložte ho do počítače.

    ![Zkopírujte hodnotu kryptografického otisku](common/copy-thumbprint.png)

7. Na **nastavení Picturepark** tématu, zkopírujte příslušné adresy URL podle vašich požadavků. Pro **přihlašovací adresa URL**, použijte hodnotu s následujícím vzorem: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ je id tenanta předplatného služby Azure AD.

    ![Zkopírování adresy URL konfigurace](./media/picturepark-tutorial/configurls.png)

    a. Identifikátor Azure AD

    b. Adresa URL – odhlášení

### <a name="configure-picturepark-single-sign-on"></a>Konfigurace Picturepark jednotné přihlašování

1. V okně jiný webový prohlížeč přihlaste jako správce serveru vaší společnosti Picturepark.

2. Na panelu nástrojů v horní části klikněte na tlačítko **nástroje pro správu**a potom klikněte na tlačítko **Konzola pro správu**.
   
    ![Konzola pro správu](./media/picturepark-tutorial/ic795062.png "Konzola pro správu")

3. Klikněte na tlačítko **ověřování**a potom klikněte na tlačítko **zprostředkovatelé Identity**.
   
    ![Ověřování](./media/picturepark-tutorial/ic795063.png "ověřování")

4. V **konfigurace zprostředkovatele Identity** části, proveďte následující kroky:
   
    ![Konfigurace zprostředkovatele identity](./media/picturepark-tutorial/ic795064.png "konfigurace zprostředkovatele Identity")
   
    a. Klikněte na tlačítko **Add** (Přidat).
  
    b. Zadejte název pro vaši konfiguraci.
   
    c. Vyberte **nastavit jako výchozí**.
   
    d. V **identifikátor URI vystavitele** textového pole vložte hodnotu **přihlašovací adresa URL** zkopírovanou z webu Azure portal.
   
    e. V **důvěryhodného vystavitele tisk miniatury** textového pole vložte hodnotu **kryptografický otisk** zkopírovanou z **podpisový certifikát SAML** oddílu. 

5. Klikněte na tlačítko **JoinDefaultUsersGroup**.

6. Nastavit **Emailaddress** atribut **deklarace identity** textové pole, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` a klikněte na tlačítko **Uložit**.

      ![Konfigurace](./media/picturepark-tutorial/ic795065.png "konfigurace")

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

V této části je povolit Britta Simon k udělení přístupu k Picturepark použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Picturepark**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Picturepark**.

    ![Odkaz Picturepark v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-picturepark-test-user"></a>Vytvoření Picturepark testovacího uživatele

Chcete-li povolit Azure AD uživatelům přihlašovat se do Picturepark, musí být poskytnuty do Picturepark. V případě Picturepark zřizování se ruční úlohy.

**K poskytnutí uživatelského účtu, postupujte následovně:**

1. Přihlaste se k vaší **Picturepark** tenanta.

1. Na panelu nástrojů v horní části klikněte na tlačítko **nástroje pro správu**a potom klikněte na tlačítko **uživatelé**.
   
    ![Uživatelé](./media/picturepark-tutorial/ic795067.png "uživatelů")

1. V **přehled uživatelů** klikněte na tlačítko **nový**.
   
    ![Správa uživatelů](./media/picturepark-tutorial/ic795068.png "Správa uživatelů")

1. Na **vytvořit uživatele** dialogového okna, proveďte následující kroky platného Azure Active Directory uživatele chcete ke zřízení:
   
    ![Vytvoření uživatele](./media/picturepark-tutorial/ic795069.png "vytvoření uživatele")
   
    a. V **e-mailovou adresu** textové pole, typ **e-mailová adresa** uživatele `BrittaSimon@contoso.com`.  
   
    b. V **heslo** a **potvrzení hesla** textová pole, typ **heslo** z BrittaSimon. 
   
    c. V **křestní jméno** textové pole, typ **křestní jméno** uživatele **Britta**. 
   
    d. V **příjmení** textové pole, typ **příjmení** uživatele **Simon**.
   
    e. V **společnosti** textové pole, typ **název společnosti** uživatele. 
   
    f. V **země** textové pole, vyberte **země** uživatele.
  
    g. V **ZIP** textové pole, typ **PSČ** města.
   
    h. V **Město** textové pole, typ **název města** uživatele.

    i. Vyberte **jazyka**.
   
    j. Klikněte na možnost **Vytvořit**.

>[!NOTE]
>Můžete použít jakékoli jiné Picturepark uživatelského účtu nástrojů pro vytváření nebo rozhraní API poskytovaných Picturepark zřízení uživatelských účtů služby Azure AD.
>

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Picturepark na přístupovém panelu, můžete by měl být automaticky přihlášeni k Picturepark, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

