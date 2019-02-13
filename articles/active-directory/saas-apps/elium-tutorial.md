---
title: 'Kurz: Integrace Azure Active Directory s Elium | Dokumentace Microsoftu'
description: Zjistěte, jak nakonfigurovat jednotné přihlašování mezi Azure Active Directory a Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85d9c56fa1f0da5bf4747693ebe2361205e83ba0
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56196735"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Kurz: Integrace Azure Active Directory s Elium

V tomto kurzu se dozvíte, jak integrovat Elium s Azure Active Directory (Azure AD).
Elium integraci se službou Azure AD poskytuje následující výhody:

* Můžete řídit ve službě Azure AD, který má přístup k Elium.
* Můžete povolit uživatelům být automaticky přihlášeni k Elium (Single Sign-On) s jejich účty Azure AD.
* Můžete spravovat své účty na jediném místě – na webu Azure portal.

Pokud chcete zjistit další podrobnosti o integraci aplikací SaaS v Azure AD, přečtěte si téma [co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Konfigurace integrace Azure AD s Elium, potřebujete následující položky:

* Předplatné služby Azure AD. Pokud nemáte prostředí Azure AD, můžete získat měsíční zkušební verze [zde](https://azure.microsoft.com/pricing/free-trial/)
* Elium jednotného přihlašování povolená předplatného

## <a name="scenario-description"></a>Popis scénáře

V tomto kurzu konfigurace a testování v testovacím prostředí Azure AD jednotného přihlašování.

* Podporuje Elium **SP** a **IDP** jednotné přihlašování zahájené pomocí

* Podporuje Elium **JIT** zřizování uživatelů

## <a name="adding-elium-from-the-gallery"></a>Přidání Elium z Galerie

Konfigurace integrace Elium do služby Azure AD, budete muset přidat Elium z Galerie na váš seznam spravovaných aplikací SaaS.

**Chcete-li přidat Elium z galerie, postupujte následovně:**

1. V **[webu Azure portal](https://portal.azure.com)**, v levém navigačním panelu klikněte na **Azure Active Directory** ikonu.

    ![Tlačítko Azure Active Directory](common/select-azuread.png)

2. Přejděte do **podnikové aplikace** a pak vyberte **všechny aplikace** možnost.

    ![V okně podnikové aplikace](common/enterprise-applications.png)

3. Chcete-li přidat novou aplikaci, klikněte na tlačítko **novou aplikaci** tlačítko v horní části dialogového okna.

    ![Tlačítko nové aplikace](common/add-new-app.png)

4. Do vyhledávacího pole zadejte **Elium**vyberte **Elium** z panelu výsledků klikněte **přidat** tlačítko pro přidání aplikace.

     ![Elium v seznamu výsledků](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurace a otestování služby Azure AD jednotného přihlašování

V této části je konfigurace a testování Azure AD jednotné přihlašování pomocí Elium podle testovacího uživatele volá **Britta Simon**.
Pro jednotné přihlašování pro práci je potřeba navázat vztah odkazu mezi uživatele služby Azure AD a související uživatelské v Elium.

Nakonfigurovat a otestovat Azure AD jednotné přihlašování s Elium, které potřebujete k dokončení následujících stavebních bloků:

1. **[Konfigurovat Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)**  – Pokud chcete, aby uživatelé mohli tuto funkci používat.
2. **[Konfigurace Elium Single Sign-On](#configure-elium-single-sign-on)**  – ke konfiguraci nastavení jednotného přihlašování na straně aplikace.
3. **[Vytvořit testovacího uživatele Azure AD](#create-an-azure-ad-test-user)**  – Pokud chcete otestovat Azure AD jednotné přihlašování s Britta Simon.
4. **[Přiřadit uživatele Azure AD](#assign-the-azure-ad-test-user)**  – Pokud chcete povolit Britta Simon používat Azure AD jednotného přihlašování.
5. **[Vytvořit testovacího uživatele Elium](#create-elium-test-user)**  – Pokud chcete mít protějšek Britta Simon Elium, který je propojený s Azure AD reprezentace uživatele.
6. **[Otestovat jednotné přihlašování](#test-single-sign-on)**  – Pokud chcete ověřit, jestli funguje v konfiguraci.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurace služby Azure AD jednotného přihlašování

V této části můžete povolit Azure AD jednotného přihlašování na portálu Azure portal.

Ke konfiguraci Azure AD jednotné přihlašování s Elium, proveďte následující kroky:

1. V [webu Azure portal](https://portal.azure.com/)na **Elium** integrace stránce aplikace vyberte **jednotného přihlašování**.

    ![Nakonfigurovat jednotné přihlašování – odkaz](common/select-sso.png)

2. Na **vybrat jedinou metodu přihlašování** dialogového okna, vyberte **SAML/WS-Fed** chcete povolit jednotné přihlašování.

    ![Jednotné přihlašování režim výběru](common/select-saml-option.png)

3. Na **nastavení jednotného přihlašování pomocí SAML** klikněte na **upravit** ikony otevřete **základní konfiguraci SAML** dialogového okna.

    ![Upravit konfiguraci základní SAML](common/edit-urls.png)

4. Na **základní konfiguraci SAML** části, pokud chcete nakonfigurovat aplikace v **IDP** iniciované režimu, proveďte následující kroky:

    ![Elium domény a adresy URL jednotného přihlašování – informace](common/idp-intiated.png)

    a. V **identifikátor** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. V **adresy URL odpovědi** textové pole, zadejte adresu URL, pomocí následujícího vzorce: `https://<platform-domain>.elium.com/login/saml2/acs`

5. Klikněte na tlačítko **nastavit další adresy URL** a provést následující krok, pokud chcete nakonfigurovat aplikace v **SP** iniciované režimu:

    ![Elium domény a adresy URL jednotného přihlašování – informace](common/metadata-upload-additional-signon.png)

    V **přihlašovací adresa URL** textové pole, zadejte adresu URL, pomocí následujícího vzorce:  `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE] 
    > Tyto hodnoty nejsou skutečný. Zobrazí se tyto hodnoty z **metadata souboru SP** ke stažení na `https://<platform-domain>.elium.com/login/saml2/metadata`, což je vysvětleno dále v tomto kurzu.

6. Elium aplikace očekává, že kontrolní výrazy SAML v určitém formátu. Nakonfigurujte následující deklarace identity pro tuto aplikaci. Můžete spravovat hodnotami těchto atributů z **atributy uživatele** části na stránce aplikací pro integraci. Na **nastavte si jednotné přihlašování pomocí SAML** klikněte na **upravit** tlačítko Otevřít **atributy uživatele** dialogového okna.

    ![image](common/edit-attribute.png)

7. V **deklarace identity uživatelů** části na **atributy uživatele** dialogovém okně Upravit deklarace identity pomocí **ikonu pro úpravu** nebo přidání deklarace identity pomocí **přidat novou deklaraci**ke konfiguraci atribut tokenu SAML, jak je znázorněno na obrázku výše a proveďte následující kroky: 

    | Název | Zdrojový atribut|
    | ---------------| ----------------|
    | e-mail   |user.mail |
    | křestní_jméno| user.givenname |
    | Příjmení| user.surname|
    | job_title| user.jobtitle|
    | Společnosti| user.companyname|

    > [!NOTE]
    > Jedná se o výchozí deklarací identity. **Deklarace identity e-mailu je povinné jenom**. Pro JIT zřizování také e-mailu jenom deklarace identity je povinné. Další vlastní deklarace identity se může lišit od platformy jednoho zákazníka na jiné platformě zákazníka.

    a. Klikněte na tlačítko **přidat novou deklaraci** otevřít **spravovat deklarace identity uživatelů** dialogového okna.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. V **název** textového pole zadejte název atributu, který je zobrazený pro tento řádek.

    c. Nechte **Namespace** prázdné.

    d. Vyberte zdroj jako **atribut**.

    e. Z **zdrojový atribut** seznamu, zadejte hodnotu atributu zobrazený pro tento řádek.

    f. Klikněte na tlačítko **Ok**

    g. Klikněte na **Uložit**.

8. Na **nastavení jednotného přihlašování pomocí SAML** stránku, **podpisový certifikát SAML** klikněte na tlačítko **Stáhnout** ke stažení **kód XML metadat federace**  z se zadanými možnostmi podle vašich požadavků a uložit je ve vašem počítači.

    ![Odkaz ke stažení certifikátu](common/metadataxml.png)

9. Na **nastavení Elium** tématu, zkopírujte příslušné adresy URL podle vašich požadavků.

    ![Zkopírování adresy URL konfigurace](common/copy-configuration-urls.png)

    a. Přihlašovací adresa URL

    b. Identifikátor služby Azure Ad

    c. Adresa URL – odhlášení

### <a name="configure-elium-single-sign-on"></a>Konfigurace Elium jednotné přihlašování

1. V okně jiné webové prohlížeče Přihlaste se k webu společnosti Elium jako správce.

2. Klikněte na **profilu uživatele** v pravém horním rohu a pak vyberte **správu**.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user1.png)

3. Vyberte **zabezpečení** kartu.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user2.png)

4. Přejděte dolů k položce **jednotné přihlašování (SSO)** části a proveďte následující kroky:

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user3.png)

    a. Zkopírujte hodnotu **ověřte, že ověřování typu SAML2 funguje pro váš účet** a vložte ji **přihlašovací adresa URL** textového pole na **základní konfiguraci SAML** oddíl ve službě Azure portál.

    > [!NOTE]
    > Po dokončení konfigurace jednotného přihlašování, můžete vždy přístup k výchozí stránka vzdálené přihlášení na následující adrese URL: `https://<platform_domain>/login/regular/login` 

    b. Vyberte **povolit SAML2 federace** zaškrtávací políčko.

    c. Vyberte **JIT zřizování** zaškrtávací políčko.

    d. Otevřít **SP metadat** kliknutím na **Stáhnout** tlačítko.

    e. Hledat **entityID** v **SP metadat** soubor, zkopírujte **entityID** hodnotu a vložte ji **identifikátor** textového pole na  **Základní konfigurace SAML** části webu Azure Portal. 

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user4.png)

    f. Vyhledejte **AssertionConsumerService** v **SP metadat** soubor, zkopírujte **umístění** hodnotu a vložte ji **adresy URL odpovědi** textové pole na **základní konfiguraci SAML** části webu Azure Portal.

    ![Konfigurace jednotného přihlašování](./media/elium-tutorial/user5.png)

    g. Do poznámkového bloku otevřete soubor stažený metadat z webu Azure portal, zkopírujte obsah a vložte ho do **metadat zprostředkovatele identity** textového pole.

    h. Klikněte na **Uložit**.

### <a name="create-an-azure-ad-test-user"></a>Vytvořit testovacího uživatele Azure AD 

Cílem této části je vytvoření zkušebního uživatele na webu Azure Portal volá Britta Simon.

1. Na webu Azure Portal, v levém podokně vyberte **Azure Active Directory**vyberte **uživatelé**a pak vyberte **všichni uživatelé**.

    !["Uživatele a skupiny" a "Všechny uživatele" odkazy](common/users.png)

2. Vyberte **nového uživatele** v horní části obrazovky.

    ![Tlačítko Nový uživatel](common/new-user.png)

3. Ve vlastnosti uživatele proveďte následující kroky.

    ![Dialogové okno uživatele](common/user-properties.png)

    a. V **název** zadejte **BrittaSimon**.
  
    b. V **uživatelské jméno** zadejte **brittasimon@yourcompanydomain.extension**  
    Například BrittaSimon@contoso.com.

    c. Vyberte **zobrazit heslo** zaškrtněte políčko a zapište si hodnotu, která se zobrazí v poli heslo.

    d. Klikněte na možnost **Vytvořit**.

### <a name="assign-the-azure-ad-test-user"></a>Přiřadit uživatele Azure AD

V této části je povolit Britta Simon k udělení přístupu k Elium použití Azure jednotného přihlašování.

1. Na webu Azure Portal, vyberte **podnikové aplikace**vyberte **všechny aplikace**a pak vyberte **Elium**.

    ![Okno aplikace organizace](common/enterprise-applications.png)

2. V seznamu aplikací vyberte **Elium**.

    ![Odkaz Elium v seznamu aplikací](common/all-applications.png)

3. V nabídce na levé straně vyberte **uživatelů a skupin**.

    ![Odkaz "Uživatele a skupiny"](common/users-groups-blade.png)

4. Klikněte na tlačítko **přidat uživatele** tlačítko a pak vyberte **uživatelů a skupin** v **přidat přiřazení** dialogového okna.

    ![Podokno Přidat přiřazení](common/add-assign-user.png)

5. V **uživatelů a skupin** dialogového okna, vyberte **Britta Simon** v seznamu uživatelů, klikněte **vyberte** tlačítko v dolní části obrazovky.

6. Pokud očekáváte libovolnou hodnotu role v kontrolní výraz SAML a potom v **vybrat roli** dialogového okna, vyberte vhodnou roli pro uživatele ze seznamu a klikněte **vyberte** tlačítko v dolní části obrazovky.

7. V **přidat přiřazení** dialogového okna, klikněte na tlačítko **přiřadit** tlačítko.

### <a name="create-elium-test-user"></a>Vytvoření Elium testovacího uživatele

V této části se vytvoří uživateli Britta Simon v Elium. Podporuje Elium **just-in-time zřizování**, který je ve výchozím nastavení povolené. Neexistuje žádná položka akce pro vás v této části. Pokud uživatel již neexistuje mezi Elium, vytvoří se při pokusu o přístup k Elium nový.

>[!Note]
>Pokud je potřeba ručně vytvořit uživatele, obraťte se na [tým podpory Elium](mailto:support@elium.com).

### <a name="test-single-sign-on"></a>Test jednotného přihlašování 

V této části Testování služby Azure AD jednotné přihlašování – konfigurace pomocí přístupového panelu.

Po kliknutí na dlaždici Elium na přístupovém panelu, můžete by měl být automaticky přihlášeni k Elium, u kterého nastavíte jednotné přihlašování. Další informace o přístupovém panelu, naleznete v tématu [Úvod k přístupovému panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Další prostředky

- [ Seznam kurzů o integraci aplikací SaaS pomocí Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co je přístup k aplikaci a jednotné přihlašování s Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co je podmíněný přístup v Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

