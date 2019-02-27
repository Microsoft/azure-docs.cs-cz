---
title: 'Kurz: Integrace Azure Active Directory s Scout Nápověda | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a pomáhají Scout.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0aad9910-0bc1-4394-9f73-267cf39973ab
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3650704cd3d01d1cce21a665f3731a00a2107ab7
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865718"
---
# <a name="tutorial-azure-active-directory-integration-with-help-scout"></a>Kurz: Integrace Azure Active Directory s pomáhají Scout

V tomto kurzu se dozvíte, jak integrovat Azure Active Directory (Azure AD) pomáhají Scout.
Integrace s Azure AD pomoct Scout poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k nápovědě Scout.
* Uživatelům se automaticky přihlášeni k nápovědě Scout (Single Sign-On) můžete povolit pomocí jejich účtů služby Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s pomáhají Scout, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Nápověda Scout jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Scout nápovědy **SP a zprostředkovatele identity** jednotné přihlašování zahájené pomocí
* Podporuje Scout nápovědy **JIT** zřizování uživatelů

## <a name="adding-help-scout-from-the-gallery"></a>Přidání pomáhají Scout z Galerie

Pokud chcete nakonfigurovat integrace pomůže Scout do služby Azure AD, potřebujete přidat pomáhají Scout z Galerie na váš seznam spravovaných aplikací SaaS.

**Nápověda Scout přidat z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **pomáhají Scout**vyberte **pomáhají Scout** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Nápověda Scout v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí pomáhají Scout podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a souvisejících uživatele v nápovědě Scout.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Scout nápovědy, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace pomoci Scout Single Sign-On](#configure-help-scout-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele pomáhají Scout](#create-help-scout-test-user)**  – Pokud chcete mít protějšek Britta Simon pomáhají Scout, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s pomáhají Scout, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **pomáhají Scout** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Nápověda Scout domény a adresy URL jednotné přihlašování](common/idp-intiated.png)

    a. **Identifikátor** je **identifikátor URI cílové skupiny (ID Entity poskytovatele služeb)** začíná od pomáhají Scout `urn:`

    b. **Adresa URL pro odpověď** je **po adresy URL (adresa URL služby příjemce kontrolního výrazu)** začíná od pomáhají Scout `https://` 

    > [!NOTE]
    > Hodnoty v těchto adres URL jsou pouze ukázku. Je potřeba aktualizovat tyto hodnoty z skutečnou odpověď URL a identifikátor. Získání těchto hodnot z **Single Sign-On** kartu v části ověřování, který je vysvětlen později v tomto kurzu.

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Nápověda Scout domény a adresy URL jednotné přihlašování](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textového pole zadejte adresu URL jako: `https://secure.helpscout.net/members/login/`

6. Na **nastavte si jednotné přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **certifikát (Base64)** z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/certificatebase64.png)

7. Na **nastavení nápovědy Scout** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-help-scout-single-sign-on"></a>Konfigurace nápovědy Scout jednotného přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k serveru vaší společnosti pomůžou Scout jako správce.

2. Klikněte na **spravovat** v horní nabídce a pak vyberte **společnosti** z rozevírací nabídky.

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings1.png)

3. Vyberte **ověřování** v levém navigačním podokně.

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings2.png)

4. Tím přejdete do části Nastavení SAML a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings3.png)

    a. Kopírovat **po adresy URL (adresa URL služby příjemce kontrolního výrazu)** hodnotu a vložte tuto hodnotu v **adresy URL odpovědi** textové pole **základní konfiguraci SAML** oddíl ve službě Azure portál.

    b. Kopírovat **identifikátor URI cílové skupiny (ID Entity poskytovatele služeb)** hodnotu a vložte tuto hodnotu v **identifikátor** textové pole **základní konfiguraci SAML** části webu Azure Portal.

5. Přepnout **povolit SAML** na a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/helpscout-tutorial/settings4.png)

    a. V **přihlašovací adresu URL jednotného** textového pole vložte hodnotu **přihlašovací adresa URL**, který jste zkopírovali z portálu Azure portal.

    b. Klikněte na tlačítko **nahrát certifikát** k nahrání **Certificate(Base64)** stáhnout z webu Azure portal.

    c. Zadejte vaše organizace e-mailových domén např - `contoso.com` v **e-mailových domén** textového pole. Více domén můžete oddělit čárkou. Kdykoli pomáhají Scout uživatel nebo správce, který zadá na tuto konkrétní doménu [pomáhají Scout přihlašovací stránka](https://secure.helpscout.net/members/login/) přesměruje se ke zprostředkovateli Identity kvůli ověření pomocí svých přihlašovacích údajů.

    d. A konečně, můžete přepínat **přihlašování SAML platnost** , aby uživatelé přihlašovat pouze k nápovědě Scout prostřednictvím prostřednictvím této metody. Pokud stále chcete ponechte možnost pro ně se přihlásit pomocí svých přihlašovacích údajů pomůžou Scout, můžete nechat ji přepínat vypnout. I v případě, že je tato možnost povolena, vlastníka účtu vždy bude moct přihlásit k nápovědě Scout s své heslo účtu.

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

V této části je povolit Britta Simon používat jednotné přihlašování Azure díky udělení přístupu k nápovědě Scout.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **pomáhají Scout**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **pomáhají Scout**.

    ![Na odkaz Nápověda Scout v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogové okno Vybrat **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogové okno vybrat vhodnou roli pro uživatele ze seznamu, klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogové okno kliknutím **přiřadit** tlačítko.

### <a name="create-help-scout-test-user"></a>Vytvořit testovacího uživatele pomáhají Scout

V této části se vytvoří uživateli Britta Simon v nápovědě Scout. Nápověda Scout podporuje zřizování uživatelů v čase, je ve výchozím nastavení povolená. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel ještě neexistuje v nápovědě Scout, se po ověření vytvoří nový.

### <a name="test-single-sign-on"></a>Test jednotného přihlašování

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Když kliknete na dlaždici Nápověda Scout na přístupovém panelu, by měl být automaticky přihlásíte k nápovědě Scout, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)