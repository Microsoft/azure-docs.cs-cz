---
title: 'Kurz: Integrace Azure Active Directory s Leapsome | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: dbdf6847658e6a5a3999007f51b409e47a65e4cd
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988078"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Kurz: Integrace Azure Active Directory s Leapsome

V tomto kurzu se dozvíte, jak integrovat Leapsome s Azure Active Directory (Azure AD).
Leapsome integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Leapsome.
* Můžete povolit uživatelům být automaticky přihlášeni k Leapsome (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Leapsome, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat [bezplatný účet](https://azure.microsoft.com/free/)
* Leapsome jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Leapsome **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí

## <a name="adding-leapsome-from-the-gallery"></a>Přidání Leapsome z Galerie

Konfigurace integrace Leapsome do služby Azure AD, budete muset přidat Leapsome z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Leapsome z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Leapsome**vyberte **Leapsome** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

    ![Leapsome v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Leapsome podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Leapsome.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Leapsome, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Leapsome Single Sign-On](#configure-leapsome-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Leapsome](#create-leapsome-test-user)**  – Pokud chcete mít protějšek Britta Simon Leapsome, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Leapsome, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Leapsome** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![[Název aplikace] Domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL: `https://www.leapsome.com`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![[Název aplikace] Domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > Předchozí hodnota adresy URL adresy URL odpovědi a Sign-on není skutečné hodnoty. Budete aktualizovat těchto prvků s skutečné hodnoty, který je vysvětlen později v tomto kurzu.

6. Vaše aplikace Leapsome očekává, že kontrolní výrazy SAML v určitém formátu, který je potřeba přidat vlastní atribut mapování konfigurace atributy tokenu SAML. Na následujícím snímku obrazovky se zobrazí v seznamu atributů výchozí. Klikněte na tlačítko **upravit** ikony otevřete **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky: 

    | Název | Zdrojový atribut | Obor názvů |
    | ---------------| --------------- | --------- |  
    | jméno | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Příjmení | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | název | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Obrázek | Adresa URL obrázku zaměstnance. | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > Hodnota atributu obrázek není skutečný. Aktualizujte tuto hodnotu s adresou URL aktuální obrázek. Chcete-li získat tento kontakt hodnotu [tým podpory Leapsome klienta](mailto:support@leapsome.com).

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. V **Namespace** textového pole zadejte identifikátor uri oboru názvů pro tento řádek.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

9. Na **nastavení Leapsome** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor Azure AD

    c. Adresa URL – odhlášení

### <a name="configure-leapsome-single-sign-on"></a>Konfigurace Leapsome jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k Leapsome jako správce zabezpečení.

1. Vpravo nahoře klikněte na logo nastavení a potom klikněte na tlačítko **nastavení správy**.

    ![Sada Leapsome](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. V nabídce vlevo klikněte na tlačítko **jednotné přihlašování (SSO)** a dále **založené na SAML jednotného přihlašování (SSO)** stránku, proveďte následující kroky:

    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Vyberte **založené na SAML povolit jednotné přihlašování**.

    b. Kopírovat **adresu URL pro přihlášení (přejděte sem uživatelé start přihlášení)** hodnotu a vložte ho do **přihlašovací adresa URL** textového pole v **základní konfiguraci SAML** části na webu Azure portal.

    c. Kopírovat **adresy URL odpovědi (obdrží odpověď od vašeho zprostředkovatele identity)** hodnotu a vložte ho do **adresy URL odpovědi** textového pole v **základní konfiguraci SAML** části na webu Azure portal .

    d. V **adresu URL pro přihlášení SSO (získaný od zprostředkovatele identity)** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    e. Zkopírujte certifikát, který jste si stáhli z webu Azure portal bez `--BEGIN CERTIFICATE and END CERTIFICATE--` komentáře a vložte ji **certifikát (poskytnutý poskytovatelem identity)** textového pole.

    f. Klikněte na tlačítko **aktualizace nastavení jednotného přihlašování**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadat **BrittaSimon**.
  
    b. V **uživatelské jméno** typ pole `brittasimon@yourcompanydomain.extension`  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Leapsome použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Leapsome**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Leapsome**.

    ![Odkaz Leapsome v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-leapsome-test-user"></a>Vytvoření Leapsome testovacího uživatele

V této části vytvoříte uživatele v Leapsome jako Britta Simon. Práce s [tým podpory Leapsome klienta](mailto:support@leapsome.com) k přidání uživatele nebo doménu, která musí být přidaný do seznamu povolených položek pro platformu Leapsome. Pokud je doména přidána týmem, se získat uživatele automaticky přiřazeni k Leapsome platformy. Uživatelé musí vytvořit a aktivovat, než použití jednotného přihlašování.

### <a name="test-single-sign-on"></a>Otestovat jednotné přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Leapsome na přístupovém panelu, můžete by měl být automaticky přihlášeni k Leapsome, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)